# Replication
DB의 데이터는 매우 중요함. 용량의 2~3배를 써서라도 안정성있게 데이터를 관리할 필요가 있음. 이렇게 데이터를 백업해 두는 방식으로 Replication을 사용함.

2대 이상의 DBMS를 나눠서 데이터를 저장하는 방식이며, 최소 Master / Slave 두개 계층으로 구성함.

![Replication](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FceKJsj%2FbtqwhVhCOBZ%2FuPK5tKNtwc1VJcLp5TBN81%2Fimg.png)

## Master DBMS 역할
웹서버로 부터 데이터 등록/수정/삭제 요청시 바이너리로그(Binarylog)를 생성하여 Slave 서버로 전달

## Slave DBMS 역할
Master DBMS로 부터 전달받은 바이너리로그(Binarylog)를 데이터로 반영

# Replication의 목적
## 1.데이터의 백업
Master 서버를 데이터의 원본서버, Slave서버를 백업서버로 지정, Master 서버에 DBMS의 등록/수정/업데이터가 생기는 즉시 Slave 서버에 변경된 데이터를 전달하여 백업을 할 수 있음. 또한 마스터 서버에 장애가 발생했을시 Slave 서버로 대체할 수 있음.
![데이터의 백업](https://t1.daumcdn.net/cfile/tistory/9940F3445A9DFC9E18)

## 2.부하분산
1대의 DB서버로 감당할수 없는 트래픽이 발생했을 때, Replication으로 부하를 분산할 수 있음.
![부하분산](https://t1.daumcdn.net/cfile/tistory/99198E355A9DFDCA1C)

# Mysql Replication 적용
- https://github.com/paki1019/mysql-replication