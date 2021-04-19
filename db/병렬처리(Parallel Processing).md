# 병렬처리(Parallel Processing)

Oracle DB 에서 SQL문을 튜닝을 하다 마지막으로 시도하는것이 병렬처리(Parallel Processing)

시스템 자원을 많이 쓰기 때문에 OLTP(Online Transaction Processing)환경에선 부적절함.(동시접속자가 800명인 시스템에서 자동 병렬처리 쿼리를 16개씩 동시에 실행 했다 가정 -> 사용해야하는 쓰레드의 수는 12800개)

OLAP(Online Analytical Processing) 환경에서 필요시 사용을 고려해볾직 함.

| MySQL은 병렬 처리가 없음. 모든 SQL 처리를 단일 코어에서 Nested Loop Join 방식으로만 데이터를 처리

## 사용법

SQL문에 /_+ parallel(..) _/ 힌트를 주어 사용

```
select /*+ parallel(8) */ ..
     from EMP;
```

다음과 같이 병렬프로세스의 개수를 지정할 수도 있음.

```
/*+ parallel */

/*+ parallel(10) */

/*+ parallel(EMP, 10) */
```

JOIN 시에는 테이블 마다 병렬 갯수를 지정할 수도 있음.

```
/*+ parallel(EMP, 10)(IRRADIATE, 5)(DEFENSIVE, 5) */

```

병렬프로세스 갯수를 지정하지 않으면 병렬프로세스가 시스템 디폴트 갯수(cpu_count x parallel_threads_per_cpu만큼 기동됨. 이것이 크기 때문에 가급적 갯수를 직접 지정하는것이 좋음.

| 병렬프로세스 갯수를 지정한다고 반드시 지정한만큼 프로세스가 뜨는 것은 아님, Oracle 이 시스템 파라메타 설정 또는 시스템 자원 리소스 여유상황에 따라 자동으로 조정함.

## 병렬 실행이 되었는지 확인법

SQL 이 병렬 쿼리로 실행이 된 경우, V$PQ_SESSTAT 에 정보가 기록됨.

```
SQL> SELECT * FROM V$PQ_SESSTAT;

STATISTIC                      LAST_QUERY SESSION_TOTAL     CON_ID
------------------------------ ---------- ------------- ----------
Queries Parallelized                    1             2          0
DML Parallelized                        0             0          0
DDL Parallelized                        0             0          0
DFO Trees                               1             2          0
Server Threads                          4             0          0
Allocation Height                       4             0          0
Allocation Width                        1             0          0
Local Msgs Sent                        14            28          0
Distr Msgs Sent                         0             0          0
Local Msgs Recv'd                      14            28          0
Distr Msgs Recv'd                       0             0          0

STATISTIC                      LAST_QUERY SESSION_TOTAL     CON_ID
------------------------------ ---------- ------------- ----------
DOP                                     4             0          0
Slave Sets                              1             0          0

13 rows selected.
```

Allocation Height 는 1개의 인스턴스의 병렬도를 나타내며,

Allocation Weight 는 RAC 구성때 병렬 쿼리를 실행하는 인스턴스 수를 의미합니다.

따라서 병렬도는 Allocation Height \* Allocation Weight

## 오라클 병렬처리 Parallel DOP (Degree of Parallelism)

DOP 란 Degree Of Parallelism 의 약어로, 병렬도라고 함. 병렬처리할 때 병렬프로세스를 몇개 띄울 것인지를 의미하며, DOP 가 20 이면 20개의 병렬프로세스를 띄워서 작업한다는 의미

```
SELECT degree FROM user_tables WHERE table_name = "EMP";
```

이 degree 값이 테이블의 DOP(병렬도, Degree Of Parallelism)를 나타냄.

alter table ~ parallel 커맨드로 테이블의 DOP를 변경할 수도 있음.

## QC, Consumer, Producer

![QC, Consumer, Producer](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fy9Qa0%2FbtqytV7dIAq%2FK95i8dY3X43nzrZhvhkPrk%2Fimg.png)

테이블에서 데이터를 읽는 작업을 하는 병렬프로세서들을 Producer

이 읽은 데이터를 받아서 Sorting(정렬), DML, DDL, Join 등을 수행하는 작업을 하는 병렬프로세들을 Consumer

작업된 결과들을 취합(통합)하는 작업을 하는 프로세서가 있는데 이를 QC(Query Coordinator)

## 병렬 실행 서버 통신 하는 방법

![병렬 실행 서버 통신 하는 방법](https://docs.oracle.com/cd/E11882_01/server.112/e25523/img/vldbg015.gif)
