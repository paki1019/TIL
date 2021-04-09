# 커넥션 풀(DBCP)이란?
웹 컨테이너(WAS)가 실행되면서 DB와 connection(연결)을 해놓은 객체들을 pool에 저장해뒀다가 클라이언트의 요청이 오면 connection을 빌려주고, 처리가 끝나면 다시 connection을 반납받아 pool에 저장하는 방식
!(이미지)[https://linked2ev.github.io/assets/img/devlog/201908/cp-s1.png]

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
