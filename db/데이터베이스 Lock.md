# 데이터베이스 Lock

## Lock이란?

- 데이터의 접근을 제한
- Lock이 없을 경우 데이터 무결성 및 일관성이 깨질 위험 존재
- 여러 사용자들이 존재하는 환경에서 동시성 제어를 위해 필요

## Lock의 종류

- 벤더 사 마다 조금씩 다름(MyISAM, InnoDB)
- 여기서는 InnoDB에 대해 설명

### 1. 배타 잠금(Exclusive Locks)

- Exclusive Lock(X Lock)은 write에 대한 Lock
- SELECT ... FOR UPDATE, UPDATE, DELETE 등의 수정 쿼리를 날릴 때 각 row에 걸리는 Lock
- X Lock이 걸려 있으면 다른 트랜잭션은 S Lock, X Lock 둘 다 걸 수 없다.

```
create table booth (
    id int auto_increment primary key,
    name varchar(255),
    booth_no int) ENGINE=InnoDB;
)

// 트랜잭션1
update booth set name = 'botobo' where booth_no = 0;

// 트랜잭션2
update booth set name = 'pick git' where booth_no = 0; # 에러

// Lock 조회
select * from information_schema.INNODB_LOCKS; # X 락 확인
```

### 2. 공유 잠금(Shared Locks)

- Shared Lock(S Lock)은 read에 대한 Lock

- 일반적인 SELECT 쿼리가 아닌 SELECT ...LOCK IN SHARE MODE 또는 SELECT ... FOR SHARE를 사용해 read 작업을 수행할 때 사용

- S Lock이 걸려있는 row에 다른 트랜잭션이 S Lock은 걸 수 있으나 X Lock은 걸 수 없다.

```
// 트랜잭션1
select * from booth where booth_no = 0 lock in share mode;

// 트랜잭션2
update booth set name = 'pick git' where booth_no = 0;

// Lock 조회
select * from information_schema.INNODB_LOCKS; # S 락 확인
```

### 3. 레코드 락(Record Lock)

- InnoDB에서 Record Lock은 row가 아닌 DB index record에 걸리는 Lock
- 테이블에 index가 없다면, 숨겨져 있는 clusterd index를 사용하여 record를 잠금

```
create index 'idx_booth_booth_no' on 'booth' (booth_no);

// 트랜잭션 1
update booth set booth_no = 4 where booth_no = 2 and name = 'see you there';

// 트랜잭션 2
update booth set booth_no = 5 where booth_no = 2 and name = 'nolto';

// Lock 조회
select * from information_schema.INNODB_LOCKS; # 테이블 레코드가 아닌 인덱스 레코드에 락이 걸림, 앞서 소개한 X, S락도 인덱스 레코드에 걸린것
```

### 4. 갭 락(Gap Locks)

- index record에 걸리는 Lock
- gap이란 index record가 없는 부분
- 조건에 해당하는 새로운 row가 추가되는 것을 방지하기 위한 것

```
select * from booth;

// 트랜잭션 1
select booth_no from booth where booth_no between 1 and 6 for update; # 명시적 S락

// 트랜잭션2
insert into booth (name, booth_no) values ('podoal', 4); # 실패
insert into booth (name, booth_no) values ('podoal', 5); # 실패
insert into booth (name, booth_no) values ('podoal', 2); # 실패

// Lock 조회
select * from information_schema.INNODB_LOCKS; # GAP 락 확인
```

## DB에서 발생하는 Deadlock 예제

```
// 트랜잭션 1
update booth set booth_no = 3 where name = 'botobo';

// 트랜잭션 2
update booth set booth_no = 1 where name = 'cvi';

// 트랜잭션 1
update booth set booth_no = 1 where name = 'cvi';

// 트랜잭션 2
update booth set booth_no = 3 where name = 'botobo'; # 데드락 발생
```

### 최근에 감지된 데드락 조회

`SHOW ENGINE INNODB STATUS;`

### 해결 방식

- deadlock detection or lock wait timeout

- deadlock detection이 활성화 되있으면 rollback할 작은 트랜잭션을 선택.(트랜잭션의 크기는 insert, update, delete된 행 수에 의해 결정, 벤더 사 마다 다를 수 있음.)
- 활성화 되어있지 않으면 설정된 lock wait timeout으로 해결

> 출처

- https://www.youtube.com/watch?v=onBpJRDSZGA
