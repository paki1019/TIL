# JPA란?

- Java Persistence API
- 자바 진영의 ORM 기준 표준으로, 인터페이스의 모음

## ORM이란?

- Object-Relational Mapping(객체 관계 매핑)
- 객체는 객체대로 설계
- 관계형 데이터베이스는 관계형 데이터베이스대로 설계
- ORM 프레임워크가 중간에서 매핑
- 대중적인 언어는 대부분 ORM 기술이 존재

## JPA 동작 과정

![JPA 동작 과정](https://gmlwjd9405.github.io/images/inflearn-jpa/jpa-basic-structure.png)

- JPA는 애플리케이션과 JDBC 사이에서 동작
  - 개발자가 JPA를 사용하면, JPA 내부에서 JDBC API를 사용하여 SQL을 호출하여 DB와 통신
  - 개발자는 간접적으로 JDBC API를 사용

### 저장 과정

![저장 과정](https://gmlwjd9405.github.io/images/inflearn-jpa/jpa-insert-structure.png)

- MemberDAO에서 객체를 저장
  - 개발자는 JPA에 Member 객체를 넘김
  - JPA는
    1. Member 엔티티를 분석
    2. INSERT SQL을 생성
    3. JDBC API를 사용하여 SQL를 DB에 날림

### 조회 과정

![조회 과정](https://gmlwjd9405.github.io/images/inflearn-jpa/jpa-select-structure.png)

- Member 객체를 조회하고 싶을 때
  - 개발자는 member의 pk값을 JPA에 넘김
  - JPA는
  1. 엔티티의 매핑 정보를 바탕으로 적절한 SELECT SQL을 생성.
  2. JDBC API를 사용하여 SQL을 DB에 날림.
  3. DB로부터 결과를 밥아옴.
  4. 결과(ResultSet)를 객체에 모두 매핑

## 왜 JPA를 사용해야 하는가?

### 1. SQL 중심적인 개발에서 객체 중심으로 개발

### 2. 생산성

- 마치 Java Collection에 데이터를 넣어다 빼는 것처럼 사용 가능
- 간단한 CRUD
  - 저장: jpa.persist(member)
  - 조회: Member member = jpa.find(memberId)
  - 수정: member.setName("변경할 이름")
  - 삭제: jpa.remove(member)
- 수정이 매우 간단
  - 객체를 변경하면 알아서 DB에 UPDATE Query가 나감.

### 3. 유지보수

- 기존: 필드 변경 시 모든 SQL을 수정해야 함.
- JPA: 필드만 추가하면 됨. SQL은 JPA가 처리함.

### 4. Object와 RDB간의 패러다임 불일치 해결

### 5. JPA의 성능 최적화 기능

1. 1차 캐시와 동일성(identity)보장 - 캐싱 기능
   1. 같은 트랜젝션 안에서는 같은 엔티티를 반환 - 약간의 조회 성능 향상
   2. DB Isolation Level이 Read Commit이어도 애플리케이션에서 Repeatable Read 보장
2. 트랜잭션을 지원하는 쓰기 지연(transactional write-behind) - 버퍼링 기능
   1. [트랜잭션]을 commit할 때까지 INSERT SQL를 메모리에 쌓아서 한번에 전송 - 지연 로딩 전략(Lazy Loading) 옵션을 사용
3. 지연 로딩(Lazy Loading)/즉시 로딩

- 지연 로딩
  - 객체가 실제로 사용될 때 로딩하는 전략
- 즉시 로딩
  - Join을 통해 항상 연관된 모든 객체를 같이 가져옴.
- 애플리케이션 개발시 일단 모두 지연 로딩으로 설정한 후, 성능 최적화가 필요할 때 옵션을 변경하는것이 좋음.
