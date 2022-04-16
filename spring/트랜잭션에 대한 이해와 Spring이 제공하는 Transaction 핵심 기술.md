# 트랜잭션에 대한 이해와 Spring이 제공하는 Transaction 핵심 기술

## 1. Transaction(트랜잭션)에 대한 이해

만약 데이터베이스의 데이터를 수정하는 도중에 예외가 발생하는 경우, DB의 데이터들은 수정이 되기 전의 상태로 다시 되돌아가져야 하고, 다시 수정 작업을 진행해야 함.

이렇듯 여러 작업을 진행하다가 문제가 생겼을 경우 이전 상태로 롤백하기 위해 사용되는 것이 트랜잭션(Transaction).

트랜잭션은 더 이상 쪼갤 수 없는 최소 작업 단위를 의미함. 트랜잭션은 commit으로 성공하거나 rollback으로 실패 이후 취소되어야 함. 속성에 따라 동작 방식을 다르게 해줄 수 있음.

- 트랜잭션 커밋: 작업 마무리
- 트랜잭션 롤백: 작업을 취소하고 이전의 상태로 돌림

만약 여러 작업이 모두 마무리 되었다면 트랜잭션 커밋을 통해 작업이 마무리되었음을 알려주어 반영해야 하며, 만약 문제가 생겼다면 작업 취소를 위해 트랜잭션 롤백 처리를 해주어야 함.

## 2. Spring이 제공하는 Transaction(트랜잭션) 핵심 기술

Spring은 트랜잭션과 관련된 3가지 핵심 기술을 제공

1. 트랜잭션(Transaction) 동기화
2. 트랜잭션(Transaction) 추상화
3. AOP를 이용한 트랜잭션(Transaction) 분리

### 1. 트랜잭션(Transaction) 동기화

JDBC를 이용하는 개발자가 직접 여러 개의 작업을 하나의 트랜잭션으로 관리하려면 Connection 객체를 공유하는 등 상당히 불필요한 작업들이 많이 생김.

Spring은 이러한 문제를 해결하고자 트랜잭션 동기화(Transaction Synchronization) 기술을 제공, 트랜잭션을 시작하기 위한 Connection 객체를 특별한 저장소에 보관해두고 필요할 때 꺼내쓸 수 있도록 하는 기술.

트랜잭션 동기화 저장소는 작업 쓰레드마다 Connection 객체를 독립적으로 관리하기 때문에, 멀티쓰레드 환경에서도 충돌이 발생할 여지가 없음.

아래 코드와 같이 트랜잭션 동기화를 적용.

```java
// 동기화 시작
TransactionSynchronizeManager.initSynchronization(); Connection c = DataSourceUtils.getConnection(dataSource);

// 작업 진행

// 동기화 종료
DataSourceUtils.releaseConnection(c, dataSource); TransactionSynchronizeManager.unbindResource(dataSource); TransactionSynchronizeManager.clearSynchronization();
```

하지만 JDBC가 아닌 Hibernate와 같은 기술을 쓴다면 위의 JDBC 종속적인 트랜잭션 동기화 코드들은 문제를 유발하게 됨. Hibernate에서는 Connection이 아닌 Session이라는 객체를 사용하기 때문. 이러한 기술 종속적인 문제를 해결하기 위해 Spring은 트랜잭션 관리 부분을 추상화한 기술을 제공함.

### 2. 트랜잭션(Transaction) 추상화

Spring은 트랜잭션 기술의 공통점을 담은 트랜잭션 추상화 기술을 제공함. 이를 이용함으로써 애플리케이션에 각 기술마다(JDBC, JPA, Hibernate 등) 종속적인 코드를 이용하지 않고도 일관되게 트랜잭션을 처리할 수 있도록 해주고 있음.

![트랜잭션(Transaction) 추상화](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F6eLCk%2Fbtq5gFwQO4x%2FKT2qebNokRvImqLY6iKcK0%2Fimg.png)

이로소 사용하는 기술과 무관하게 PlatformTransactionManager를 통해 다음의 코드와 같이 트랜잭션을 공유하고, 커밋하고, 롤백할 수 있게 되됨.

```java
public Object invoke(MethodInvoation invoation) throws Throwable {
    TransactionStatus status = this.transactionManager.getTransaction(new DefaultTransactionDefinition());

    try {
        Object ret = invoation.proceed();
        this.transactionManager.commit(status);
        return ret;
    } catch (Exception e) {
        this.transactionManager.rollback(status); throw e;
    }
}
```

하지만 위의 코드에서는 트랜잭션 관리 코드들이 비지니스 로직 코드와 결합되어 2가지 책임을 가지게 됨. Spring에서는 AOP를 이용해 이러한 트랜잭션 부분을 핵심 비지니스 로직과 분리함.

### 3. AOP를 이용한 트랜잭션(Transaction) 분리

Spring에서는 마치 트랜잭션 코드와 같은 부가 기능 코드가 존재하지 않는 것 처럼 보이기 위해 해당 로직을 클래스 밖으로 빼내서 별도의 모듈로 만드는 AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)를 고안 및 적용함.

```java
@Service
@RequiredArgsConstructor
@Transactional
public class UserService {
    private final UserRepository userRepository; public void addUsers(List<User> userList) {
        for (User user : userList) {
            if (isEmailNotDuplicated(user.getEmail())) {
                userRepository.save(user);
            }
        }
    }
}
```

## Spring 트랜잭션의 세부 설정

Spring의 DefaultTransactionDefinition이 구현하고 있는 TransactionDefinition 인터페이스는 트랜잭션의 동작방식에 영향을 줄 수 있는 네 가지 속성을 정의하고 있음. 해당 4가지 속성은 트랜잭션을 세부적으로 이용할 수 있게 도와주며, @Transactional 어노테이션에도 공통적으로 적용할 수 있음.

- 트랜잭션 전파
- 격리수준
- 제한시간
- 읽기전용

### 트랜잭션 전파

트랜잭션 전파란 트랜잭션의 경계에서 이미 진행중인 트랜잭션이 있거나 없을 때 어떻게 동작할 것인가를 결정하는 방식을 의미함. A 작업에 대한 트랜잭션이 진행중이고 B 작업이 시작될 때 B 작업에 대한 트랜잭션을 아래와 같이 처리 가능.

#### PROPAGATION_REQUIRED

B의 코드는 새로운 트랜잭션을 만들지 않고 A에서 진행중이 트랜잭션에 참여. B의 작업이 마무리 되고 나서, 남은 A의 작업(2)을 처리할 때 예외가 발생하면 A와 B의 작업이 모두 취소됨.

#### PROPAGATION_REQUIRES_NEW

B의 트랜잭션 경계를 빠져나오는 순간 B의 트랜잭션은 독자적으로 커밋 또는 롤백되고, 이것은 A에 어떠한 영향도 주지 않음. 이후 A가 (2)번 작업을 하면서 예외가 발생해 롤백되어도 B의 작업에는 영향을 주지 않음.

#### PROPAGATION_NOT_SUPPORTED

B의 작업에 대해 트랜잭션을 걸지 않음. B의 작업이 단순 데이터 조회라면 굳이 트랜잭션이 필요 없을 것.

이렇듯 이미 진행중인 트랜잭션이 어떻게 영향을 미칠 수 있는가에 대한 부분이 트랜잭션 전파 속성. 트랜잭션을 시작하는 것처럼 보이는 getTransaction() 코드가 begin이 아닌 get으로 이름이 지어진 이유도 이와 같음(해당 트랜잭션은 다른 트랜잭션에 묶여있을 수 있기 때문).

### 격리 수준

모든 DB 트랜잭션은 격리수준을 가지고 있음. 서버에서는 여러 개의 트랜잭션이 동시에 진행될 수 있는데, 모든 트랜잭션을 독립적으로 만들고 순차 진행 한다면 안전하겠지만 성능이 크게 떨어짐. 따라서 적절하게 격리수준을 조정해서 가능한 많은 트랜잭션을 동시에 진행시키면서 문제가 발생하지 않도록 제어해야 함. 이는 JDBC 드라이버나 DataSource 등에서 재설정할 수 있고, 트랜잭션 단위로 격리 수준을 조정할 수도 있음.

DefaultTransactionDefinition에 설정된 격리수준은 ISOLATION_DEFAUL 로 DataSource에 정의된 격리 수준을 따른다는 것.

기본적으로는 DB나 DataSource에 설정된 기본 격리 수준을 따르는 것이 좋지만, 특별한 작업을 수행하는 메소드라면 독자적으로 지정해줄 필요가 있음.

### 제한시간

트랜잭션을 수행하는 제한시간을 설정할 수 있다. 제한시간의 설정은 트랜잭션을 직접 시작하는 PROPAGATION_REQUIRED나 PROPAGATION_REQUIRES_NEW의 경우에 사용해야만 의미가 있음.

### 읽기전용

읽기전용으로 설정해두면 트랜잭션 내에서 데이터를 조작하는 시도를 막아줄 수 있을 뿐만 아니라 데이터 액세스 기술에 따라 성능이 향상될 수 있다.

> 출처
>
> - https://mangkyu.tistory.com/154
