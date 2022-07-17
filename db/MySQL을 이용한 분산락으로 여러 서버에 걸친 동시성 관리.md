# MySQL을 이용한 분산락으로 여러 서버에 걸친 동시성 관리

## 왜 MySQL 을 이용하여 분산락을 구현하였나?
- ZooKeeper, Redis 등을 이용하여 분산락을 구현하는 것도 좋지만 ZooKeeper, Redis 등을 이용하여 분산락을 구현하게 되면 인프라 구축에 대한 비용이 발생할 뿐만 아니라 인프라 구축 후 유지보수에 대한 비용 또한 발생.
- MySQL은 프로젝트 초기부터 RDBMS로 사용해오고 있었기때문에 인프라 구축 및 유지보수에 대한 추가 비용이 들지 않았고, 분산락의 사용량이 추가적인 비용을 들일만큼 많지않음.
- MySQL에서 제공하는 USER-LEVEL LOCK 은 LOCK 에 이름을 지정할 수 있기 때문에 LOCK 의 이름을 이용하여 어플리케이션단에서 제어가 가능하다는 장점.

## MySQL USER-LEVEL LOCK 관련 함수

### GET_LOCK(str,timeout)

- 입력받은 이름(str) 으로 timeout 초 동안 잠금 획득을 시도. timeout 에 음수를 입력하면 잠금을 획득할 때까지 무한대로 대기.
- 한 session에서 잠금을 유지하고 있는동안에는 다른 session에서 동일한 이름의 잠금을 획득 할 수 없음.
- GET_LOCK() 을 이용하여 획득한 잠금은 Transaction 이 commit 되거나 rollback 되어도 해제되지 않음.
- GET_LOCK() 의 결과값은 1, 0, null 을 반환.
  - 1: 잠금 획득 성공.
  - 0: timeout 초 동안 잠금 획득 시도 실패.
  - null: 잠금획득 중 에러가 발생.(ex: Out Of Memory, 스레드 강제 종료)
- MySQL 5.7 이상 버전과 5.7 미만 버전의 차이
    | 5.7 미만 |5.7 이상 |
    | --- | --- |
    | 동시에 하나의 잠금만 획득 가능 | 동시에 여러개 잠금 획득 가능 |
    | 잠금 이름 글자수 무제한 | 잠금 이름 글자수 60자로 제한 |
- MySQL 5.7 이전 버전에서 GET_LOCK() 을 이용하여 동시에 여러개의 잠금을 획득하게 되면, 이전에 획득했던 잠금이 해제.
- 동시에 잠금을 획득하기위해 대기할 때 대기 순서는 보장되지 않음.

### RELEASE_LOCK(str)

- 입력받은 이름(str) 의 잠금을 해제.
- RELEASE_LOCK() 의 결과값은 1, 0, null 을 반환.
  - 1 : 잠금을 성공적으로 해제.
  - 0 : 잠금이 해제되지는 않았지만, 현재 쓰레드에서 획득한 잠금이 아닌경우.
  - null : 잠금이 존재하지 않음.
 
### RELEASE_ALL_LOCKS()

- 현재 세션에서 유지되고 있는 모든 잠금을 해제하고 해제한 잠금 갯수를 반환

### IS_FREE_LOCK(str)

- 입력한 이름(str)에 해당하는 잠금이 획득 가능한지 확인.
- 결과 값으로 1, 0, null 을 반환.
  - 1 : 입력한 이름의 잠금이 없을때
  - 0 : 입력한 이름의 잠금이 있을때
  - null : 에러발생시(ex : 잘못된 인자)

### IS_USED_LOCK(str)
- 입력한 이름(str)의 잠금이 사용중인지 확인.
- 입력받은 이름의 잠금이 존재하면 connection id 를 반환하고, 없으면 null 을 반환.

## 분산락 구현
- MySQL에서 제공하는 함수 중에서 `GET_LOCK()` 과 `RELEASE_LOCK()` 을 이용하여 분산락을 구현.
- `executeWithLock()` 한번의 호출로 Lock 을 얻고, Lock 을 푸는 작업을 하기 위해 비즈니스 로직은 Supplier 인터페이스를 이용한 콜백으로 수행되도록 구현.
- `GET_LOCK()` 과 `RELEASE_LOCK()`을 사용하기 위해서는 쿼리를 이용하여 호출해야 했기 때문에 처음에는 NamedJdbcTemplate 이용하여 아래와 같이 구현
- Lock 을 얻는 부분이 로직을 수행하는 부분에 영향을 주는것을 방지하기 위해 Lock 을 얻는부분에서 사용하는 ConnectionPool 과 로직을 수행하는 부분에서 사용하는 ConnectionPool 을 분리하여 설정, 각각 다른 ConnectionPool 을 사용해야 하므로 `excuteWithLock()` 에 `@Transactional` 을 붙이지 않음.

```java
public class UserLevelLockWithJdbcTemplate {

    private static final String GET_LOCK = "SELECT GET_LOCK(:userLockName, :timeoutSeconds)";
    private static final String RELEASE_LOCK = "SELECT RELEASE_LOCK(:userLockName)";
    private static final String EXCEPTION_MESSAGE = "LOCK 을 수행하는 중에 오류가 발생하였습니다.";

    private final NamedParameterJdbcTemplate jdbcTemplate;

    public UserLevelLockWithJdbcTemplate(NamedParameterJdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public <T> T executeWithLock(String userLockName,
                                int timeoutSeconds,
                                Supplier<T> supplier) {

        try {
            getLock(userLockName, timeoutSeconds);
            return supplier.get();
        } finally {
            releaseLock(userLockName);
        }
    }

    private void getLock(String userLockName,
                        int timeoutSeconds) {

        Map<String, Object> params = new HashMap<>();
        params.put("userLockName", userLockName);
        params.put("timeoutSeconds", timeoutSeconds);

        log.info("GetLock!! userLockName ], timeoutSeconds ]", userLockName, timeoutSeconds);
        Integer result = jdbcTemplate.queryForObject(GET_LOCK, params, Integer.class);
        checkResult(result, userLockName, "GetLock");
    }

    private void releaseLock(String userLockName) {

        Map<String, Object> params = new HashMap<>();
        params.put("userLockName", userLockName);

        log.info("ReleaseLock!! userLockName ]", userLockName);

        Integer result = jdbcTemplate.queryForObject(RELEASE_LOCK, params, Integer.class);

        checkResult(result, userLockName, "ReleaseLock");
    }

    private void checkResult(Integer result,
                            String userLockName,
                            String type) {
        if (result == null) {
            log.error("USER LEVEL LOCK 쿼리 결과 값이 없습니다. type = [], userLockName ]", type, userLockName);
            throw new RuntimeException(EXCEPTION_MESSAGE);
        }
        if (result != 1) {
            log.error("USER LEVEL LOCK 쿼리 결과 값이 1이 아닙니다. type = [], result ] userLockName ]", type, result, userLockName);
            throw new RuntimeException(EXCEPTION_MESSAGE);
        }
    }
}
```



- Lock 을 얻어오는 부분과 로직을 수행하는 부분을 @Transactional을 통해 하나의 트랜잭션으로 묶게 되면 Lock 을 얻어오는 부분과 로직을 수행하는 부분이 동일한 ConnectionPool 을 사용하게 되어 Lock을 얻어오는 부분이 로직을 수행하는 부분에 영향을 줄 수 있기에, 결국 JdbcTemplate 의 사용을 포기하고 최종적으로는 DataSource 를 주입받아 JDBC 를 이용하여 직접 구현.
> 내용 추가 필요.

> 출저
> - https://techblog.woowahan.com/wp-content/uploads/img/2019-05-30/release_lock%20%EC%8B%A4%ED%8C%A8.png