# 커넥션 풀(DBCP)이란?

웹 컨테이너(WAS)가 실행되면서 DB와 connection(연결)을 해놓은 객체들을 pool에 저장해뒀다가 클라이언트의 요청이 오면 connection을 빌려주고, 처리가 끝나면 다시 connection을 반납받아 pool에 저장하는 방식
![이미지](https://linked2ev.github.io/assets/img/devlog/201908/cp-s1.png)

# 커넥션 풀(DBCP)을 사용하는 이유

자바에서 DB에 직접 연결해서 처리하는 경우(JDBC) 드라이버(Driver)를 로드하고 커넥션(connection) 객체를 받아와 매번 사용자가 요청할 때마다 드라이버를 로드하고 커넥션 객체를 생성하여 연결하고 종료하기 때문에 비효율적이다.

# 커넥션 풀의 특징

- 웹 컨테이너(WAS)가 실행되면서 connection 객체를 미리 pool에 생성해 둠
- HTTP 요청에 따라 pool에서 connection객체를 가져다 쓰고 반환
- 물리적인 데이터베이스 connection(연결) 부하를 줄이고 연결 관리
- pool에 미리 connection이 생성되어 있기 때문에 connection을 생성하는 데 드는 요정 마다 연결 시간이 소비되지 않음
- 커넥션을 계속해서 재사용하기 때문에 생성되는 커넥션 수를 제한적으로 설정함

# 동시 접속자가 많을 경우

- 동시 접속 할 경우 pool에서 미리 생성 된 connection을 일단 제공하고 제공받지 못한 사용자는 connection이 반환될 때까지 번호순대로 대기한다.
- WAS에서 커넥션 풀을 크게 설정하면 메모리 소모가 큰 대신 많은 사용자가 대기시간이 줄어들고, 반대로 커넥션 풀을 적게 설정하면 그 만큼 대기시간이 길어진다.

# Apache의 Commons DBCP

커넥션 풀에는 Commons DBCP와 Tomcat-JDBC, BoneCP, HikariCP 등이 있는데, 스프링 부트 2.0부터는 HikariCP를 디폴트 커넥션 풀로 사용함.

# 커넥션 풀의 저장 구조

Commons DBCP는 아래 그림처럼 commons-pool에서 제공하는 리소스 풀의 기능을 이용한다.
![이미지](https://d2.naver.com/content/images/2015/10/helloworld-201508-CommonsDBCP-------1.png)

커넥션 생성은 Commons DBCP에서 이루어진다. Commons DBCP는 PoolableConnection 타입의 커넥션을 생성하고 생성한 커넥션에 ConnectionEventListener를 등록한다. ConnectionEventListener에는 애플리케이션이 사용한 커넥션을 풀로 반환하기 위해 JDBC 드라이버가 호출할 수 있는 콜백 메서드가 있다. 이렇게 생성된 커넥션은 commons-pool의 addObject() 메서드로 커넥션 풀에 추가된다. 이때 commons-pool은 내부적으로 현재 시간을 담고 있는 타임스탬프와 추가된 커넥션의 레퍼런스를 한 쌍으로 하는 **ObjectTimestampPair**라는 자료구조를 생성한다. 그리고 이들을 LIFO(last in first out) 형태의 **CursorableLinkedList**로 관리한다.

# 커넥션 개수 관련 속성

- initialSize: 최초로 getConnection() 메서드를 호출할 때 커넥션 풀에 채워 넣을 커넥션 개수
- maxActive: 동시에 사용할 수 있는 최대 커넥션 개수
- maxIdle: 커넥션 풀에 반납할 때 최대로 유지될 수 있는 커넥션 개수
- minIdle: 최소한으로 유지할 커넥션 개수

만약 8개의 커넥션을 최대로 활용할 수 있을 때 4개는 사용 중이고 4개는 대기 중인 상태라면 커넥션 풀의 상태는 그림 2와 같을 것이다.
![이미지](https://d2.naver.com/content/images/2015/10/helloworld-201508-CommonsDBCP-------2.png)

# 커넥션을 얻기 전 대기 시간

BasicDataSource 클래스의 maxWait 속성은 커넥션 풀 안의 커넥션이 고갈됐을 때 커넥션 반납을 대기하는 시간(밀리초)이며 기본값은 무한정. 적절하게 설정하지 않아도 일반적인 상황에서는 큰 문제가 되지 않지만, 사용자가 갑자기 급증하거나 DBMS에 장애가 발생하면 장애를 더욱 크게 확산시킬 수 있다.

# 유효성 검사 쿼리 설정

JDBC 커넥션의 유효성은 validationQuery 옵션에 설정된 쿼리를 실행해 확인할 수 있다. validationQUery 관련 설정값은 아래와 같음

- testOnBorrow: 커넥션 풀에서 커넥션을 얻어올 때 테스트 실행(기본값: true)
- testOnReturn: 커넥션 풀로 커넥션을 반환할 때 테스트 실행(기본값: false)
- testWhileIdle: Evictor 스레드(놀고있는 커넥션 제거)가 실행될 때 (timeBetweenEvictionRunMillis > 0) 커넥션 풀 안에 있는 유휴 상태의 커넥션을 대상으로 테스트 실행(기본값: false)

validationQuery 옵션에는 DBMS에 따라 다음과 같이 쿼리를 설정

- Oracle: select 1 from dual
- Microsoft SQL Server: select 1
- MySQL: select 1
- CUBRID: select 1 from db_root

# Evictor 스레드
Evictor(축출자) 스레드는 Commons DBCP 내부에서 커넥션 자원을 정리하는 구성 요소이며 별도의 스레드로 실행됨. 관련된 속성은 아래와 같음
- timeBetweenEvictionRunsMillis: Evictor 스레드가 동작하는 간격. 기본값은 -1이며 비활성화되있음.
- numTestsPerEvictionRun: Evictor 스레드 동작 시 한 번에 검사할 커넥션의 개수
- minEvictableIdleTimeMillis: Evictor 스레드 동작 시 커넥션의 유휴 시간을 확인해 설정 값 이상일 경우 커넥션을 제거(기본값 30분)

Evictor 스레드의 역할 3가지
1. 커넥션 풀 내의 유휴 상태의 커넥션 중에서 오랫동안 사용되지 않은 커넥션을 추출해 제거. Evictor 스레드 실행 시 설정된 numTestsPerEvictionRun 속성값만큼 CursorableLinkedList의 ObjectTimestampPair를 확인, ObjectTimestampPair의 타임스탬프 값과 현재 시간의 타임스탬프 값의 차이가 minEvictableIdleTimeMillis 속성값을 초과하면 해당 커넥션을 제거. 커넥션 숫자를 적극적으로 줄여야 하는 상황이 아니라면 minEvictableIdleTimeMillis 속성값을 -1로 설정해서 해당 기능을 사용하지 않기를 권장.
2. 커넥션에 대해서 추가로 유효성 검사를 수행해 문제가 있을 경우 해당 커넥션을 제거. testWhileIdle 옵션이 true로 설정됐을 때만 이 동작을 수행.
3. 앞의 두 작업 이후 남아 있는 커넥션의 개수가 minIdle 속성값보다 작으면 minIdle 속성값만큼 커넥션을 생성해 유지

Evictor 스레드는 동작 시에 커넥션 풀에 잠금(lock)을 걸고 동작하기 때문에 너무 자주 실행하면 서비스 실행에 부담을 줄 수 있음.