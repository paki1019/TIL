# DB 파티셔닝(Partitioning)이란?
- 서비스의 크기가 점점 커지고 DB에 저장하는 데이터의 규모 또한 대용량화 되면서, 기존에 사용하는 DB 시스템의 용량(storage)의 한계와 성능(performance)의 저하 를 가져오게 됨.
- 이런 이슈를 해결하기 위한 방법으로 table을 파티션(partition)이라는 작은 단위로 나눠 관리하는 기법이 바로 파티셔닝(Partitioning).
- 논리적인 데이터 element 들을 다수의 entity로 쪼개는 행위를 뜻하며, 큰 table이나 index를, 관리하기 쉬운 partition이라는 작은 단위로 물리적으로 분할하는 것을 의미함.

# DB 파티셔닝(Partitioning)의 종류
### 1. 수평(horizontal) 파티셔닝
![이미지](https://gmlwjd9405.github.io/images/database/horizontal-partitioning.png)
- 하나의 테이블의 몇몇 행을 다른 테이블로 분산시키는 것.
- 샤딩(Sharding)과 동일한 개념으로 스키마(schema)가 같은 데이터를 두 개 이상의 테이블에 나누어 저장하는 것을 의미함.
- 일반적으로 분산 저장 기술에서 파티셔닝은 수평 분할을 의미
- 예시
- 고객의 데이터베이스를 CustomerId를 샤드키로 사용하여 샤딩
    - 0 ~ 10000번 고객의 정보는 0번 샤드, 10001 ~ 20000 번 고객의 정보는 1번 샤드에 저장
    - DBA는 데이터 엑세스 패턴과 저장 공간 이슈를 고려하여 적절한 샤드키를 결정
- 장단점
- 장점
    - 데이터의 개수가 작아지고 따라서 index의 개수도 작아짐. 성능 향상
- 단점
    - 서버간의 연결과정이 많아짐
    - 데이터를 찾는 과정이 기존보다 복잡하기 때문에 latency가 증가함.
    - 하나의 서버가 고장나게 되면 데이터의 무결성이 깨질 수 있음.
  
### 2. 수직(vertical) 파티셔닝
![이미지](https://gmlwjd9405.github.io/images/database/vertical-partitioning.png)
- 테이블의 일부 열을 빼내는 형태로 분할하는 것.
- 관계형 DB에서 제 3정규화와 비슷, 그러나 수직 파티셔닝은 이미 정규화된 데이터를 분리하는 것
- 예시
  - 한 고객은 하나의 청구 주소를 가지고 있다. 그러나 데이터의 유연성을 위해 청구 주소 정보를 다른 테이블에 CustomerId를 참조하는 형태로 저장함
- 장단점
  - 장점
    - 자주 사용하는 컬럼 등을 분리시켜 성능을 향상시킬 수 있다.
    - 테이블을 SELECT할 때 필요없는 컬럼까지 읽는 상황을 방지할 수 있다. 성능 향상.

# DB 파티셔닝(Partitioning)의 분할 기준
데이터를 여러 파티셔닝 중 어떤 파티션에 넣을지 구분하는 기준을 분할 기준이라 함

## 1. 범위 분할(range partitioning)
분할 키 값이 범위 내에 있는지 여부로 구분.
```
CREATE TABLE t1 ( 
    id INT, 
    year_col INT 
) 
PARTITION BY RANGE (year_col) ( 
    PARTITION p0 VALUES LESS THAN (1991), 
    PARTITION p1 VALUES LESS THAN (1995), 
    PARTITION p2 VALUES LESS THAN (1999) 
); 

INSERT INTO t2 (id, year_col) VALUES (1, 1990),(2, 1991),(3,1996),(4,1999),(5,1995),(6,1989),(7,1891),(8,1892);
```

## 2. 목록 분할(list partitioning)
값 목록을 지정해서 그 목록을 기준으로 파티션을 구분, Country 라는 컬럼의 값이 Iceland, Norway, Sweden, Finland, Denmark 인 북유럽 국가 파티션을 구축 할 수 있음.
```
CREATE TABLE employees ( 
    id INT NOT NULL, 
    fname VARCHAR(30), 
    lname VARCHAR(30), 
    hired DATE NOT NULL DEFAULT '1970-01-01', 
    separated DATE NOT NULL DEFAULT '9999-12-31', 
    job_code INT, 
    store_id INT 
) PARTITION BY LIST(store_id) ( 
    PARTITION pNorth VALUES IN (3,5,6,9,17), 
    PARTITION pEast VALUES IN (1,2,10,11,19,20), 
    PARTITION pWest VALUES IN (4,12,13,14,18), 
    PARTITION pCentral VALUES IN (7,8,15,16) 
); 

INSERT INTO employees (id, fname, lname, hired, separated, job_code, store_id) 
VALUES (1, 'soyeon', 'yoon', '2013-12-26', '9999-12-31', 1, 20) 
,(2, 'gildong', 'hong', '2011-01-01', '9999-12-31', 1, 3) 
,(2, 'jungwoo', 'ha', '2011-04-12', '9999-12-31', 1, 7) 
,(2, 'suzy', 'bae', '2012-06-21', '9999-12-31', 1, 6) 
,(2, 'you', 'kong', '2012-09-04', '9999-12-31', 1, 12) 
,(2, 'hyogin', 'kong', '2013-01-23', '9999-12-31', 1, 1) 
,(2, 'jungmin', 'hwang', '2015-12-26', '9999-12-31', 1, 8) 
,(2, 'nayeong', 'lee', '2011-01-01', '9999-12-31', 1, 2);
```

## 3. 해시 분할(hash partitioning)
해시 함수의 값에 따라서 파티션에 포함할지 여부를 결정함.
```
CREATE TABLE t1 ( 
    id INT, 
    year_col INT 
) 
PARTITION BY HASH(id) 
PARTITIONS 8; 

INSERT INTO t1 (id, year_col) 
VALUES (1, 1990),(2, 1991),(3,1996),(4,1999),(5,1995),(6,1989),(7,1891),(8,1892),(9,1891),(10,1810),(11,1990),(12,1993);
```

## 4. 합성 분할(Composite partitioning)
위의 범위 분할, 목록 분할, 해시 분할을 결합하는 것을 의미함.

# DB 파티셔닝(Partitioning)의 장단점
## 장점
- 전체 데이터를 손실할 가능성이 줄어들어 데이터 가용상이 향상됨.
- partition별로 백업 및 복구가 가능함.
- partition 단위로 I/O 분산이 가능하여 UPDATE 성능이 향상됨.

## 단점
- 데이터를 입력받았을 때 어디에 넣어야 하는지에 대한 연산 오버헤드가 발생할 수도 있음.
- table간 JOIN 비용이 증가함.
- table과 index를 별도로 파티셔닝 할 수는 없음.