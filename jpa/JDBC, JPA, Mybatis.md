# 1. JDBC(Java Database Connectivity)

![JDBC](https://gmlwjd9405.github.io/images/spring-framework/spring-jdbc-architecture.png)

- JDBC는 DB에 접근할 수 있도록 Java에서 제공하는 API
  - JDBC는 DB에 접근할 수 있도록 Java에서 제공하는 API
  - JDBC는 데이터베이스에서 자료를 쿼리하거나 업데이트하는 방법을 제공한다.

# 2. JPA(Java Persistent API)

![JPA](https://gmlwjd9405.github.io/images/spring-framework/spring-jpa-architecture.png)

- 자바 ORM 기술에 대한 API 표준 명세로, Java에서 제공하는 API
  - JPA는 ORM을 사용하기 위한 표준 인터페이스를 모아둔 것
  - 기존에 EJB에서 제공되던 엔터티 빈(Entity Bean)을 대체하는 기술
- JPA 구성 요서 (세 가지)
  1. `javax.persistance` 패키지로 정의된 API 그 자체
  2. JPQL (Java Persistance Query Language)
  3. 객체/관계 메타데이터
- 사용자가 원하는 JPA 구현체를 선택해서 사용할 수 있음.
  - 이 구현체들을 ORM Framework 라고 부름.

```
package javax.persistence;

import ...

public interface EntityManager {

    public void persist(Object entity);

    public <T> T merge(T entity);

    public void remove(Object entity);

    public <T> T find(Class<T> entityClass, Object primaryKey);

    // More interface methods...
}
```

## Hibernate

![Hibernate](https://gmlwjd9405.github.io/images/spring-framework/spring-hibernate-architecture.png)

- Hibernate는 JPA의 구현체 중 하나.
- Hibernate가 지원하는 메서드 내부에서는 JDBC API가 동작하고 있음.
- 장점
  - HQL은 완전히 객체 지향적이며 이로써 상속, 다형성, 관계등의 객체지향의 강점을 누릴 수 있음.
  - 테이블 생성, 변경, 관리가 쉬움.
  - 로직을 쿼리에 집중하기 보다는 객체자체에 집중 할 수 있음.
  - 빠른 개발이 가능.
- 단점
  - 어려움.
  - 잘 이해하고 사용하지 않으면 데이터 손실이 있을수도 있음.
  - 성능상 문제가 있을수도 있음.

## Spring Data JPA

- Spring Data JPA는 Spring에서 제공하는 모듈 중 하나로, 개발자가 JPA를 더 쉽고 편하게 사용할 수 있도록 도와줌.
- JPA를 한 단계 추상화시킨 Repository라는 인터페이스를 제공함으로써 이루어짐.
  - Repository의 구현에서 내부적으로 EntityManager을 사용.
- Repository 인터페이스에 정해진 규칙대로 메소드를 입력하면, Spring이 알아서 해당 메소드 이름에 적합한 쿼리를 날리는 구현체를 만들어서 Bean으로 등록해줌.

![구분](https://suhwan.dev/images/jpa_hibernate_repository/overall_design.png)

## QueryDSL

- SQL, JPQL을 코드로 작성할 수 있도록 도와주는 빌더 API
- JPA 크리테이라에 비해서 편리하고 실용적
- SQL, JPQL의 문제점

  - SQL, JPQL은 문자열이다. Type-check가 불가능
  - 해당 로직 실행 전까지 작동여부 확인을 할 수 없고, 해당 쿼리 실행 시점에 오류를 발견 가능.

- QueryDSL 장점
  - 문자가 아닌 코드로 쿼리를 작성
  - 컴파일 시점에 문법 오류 발견 가능
  - 동적 쿼리를 자바코드로 작성 가능
  - 코드 자동완성(IDE 지원)

```
JPAFactoryQuery query = new JPAQueryFactory(em);
QMember m = QMember.member;
​
List<Member> list =
    query.selectFrom(m)
         .where(m.age.gt(18))
         .orderBy(m.name.desc())
         .fetch();

```

# Mybatis

![Mybatis](https://gmlwjd9405.github.io/images/spring-framework/spring-mybatis-architecture.png)

- 개발자가 지정한 SQL, 저장 프로시저 그리고 몇 가지 고급 매핑을 지원하는 SQL Mapper
- JDBC로 처리하는 상당 부분의 코드와 파라미터 설정 및 결과 매핑을 대신해줌.
  - 기존에 JDBC를 사용할 때는 DB와 관련된 여러 복잡한 설정(Connection)들을 다루어야 했지만 SQL Mapper는 자바 객체를 실제 SQL문에 연결함으로써, 빠른 개발과 편리한 테스트 환경을 제공.
- MyBatis는 원래 Apache Foundation의 iBatis였으나, 생산성, 개발 프로세스, 커뮤니티 등의 이유로 Google Code로 이전되면서 이름이 바뀜.
- 장점
  - SQL에 대한 모든 컨트롤을 하고자 할때 매우 적합.
  - SQL쿼리들이 매우 잘 최적화되어 있을 때 유용.
- 단점

  - 애플리케이션과 데이터베이스 간의 설계에 대한 모든 조작을 하고자 할 때는 많은 설정을 건드려야 하기 때문에 부적절.

> 출처
>
> - https://gmlwjd9405.github.io/2018/12/25/difference-jdbc-jpa-mybatis.html
> - https://suhwan.dev/2019/02/24/jpa-vs-hibernate-vs-spring-data-jpa/
> - https://ict-nroo.tistory.com/117
