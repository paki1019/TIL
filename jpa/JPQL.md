# JPQL

- 객체 지향 쿼리 언어
  - 테이블이 아니라 엔티티 객체를 대상으로 쿼리.
- SQL을 추상화해서 특정 DB SQL에 의존하지 않음.
- 최종적으로 SQL로 변환되어 실행됨.

## JPQL 문법

![JPQL](https://dodeon.gitbook.io/~/files/v0/b/gitbook-28427.appspot.com/o/assets%2F-LxjHkZu4T9MzJ5fEMNe%2Fsync%2F8857d195148251351c99d8cf473767a7f2d19f7e.png?generation=1617443660720895&alt=media)

- SQL과 거의 동일

```
select m from Member as m where m.age > 18
```

- 엔티티와 속성은 대소문자를 구분함, 객체에 있는 것과 똑같이 사용.
- JPQL 키워드는 대소문자를 구분하지 않음.
- 테이블 이름이 아니라 엔티티 이름을 사용해야 함.
- 별칭을 필수로 사용해야 함.

## 집합과 정렬

![집합과 정렬](https://dodeon.gitbook.io/~/files/v0/b/gitbook-28427.appspot.com/o/assets%2F-LxjHkZu4T9MzJ5fEMNe%2Fsync%2F2fb26d84cf5b926e4a2186badd77f857e50c0cbf.png?generation=1617443658699165&alt=media)

- group by, having등도 똑같이 사용

## TypeQuery, Query

### TypeQuery

```
public class JpaMain {

  public static void main(String[] args) {
    TypedQuery<Member> a = em.createQuery("select m from Member m", Member.class);
    TypedQuery<String> b = em.createQuery("select m.username, m.age from Member m", String.class);
  }
}
```

반환 타입이 명확할 떄 사용.

### Query

```
public class JpaMain {

  public static void main(String[] args) {
    // String과 int
    Query a = em.createQuery("select m.username, m.age from Member m");
  }
}
```

반환 타입이 명확하지 않을 때 사용.

## 결과 조회 API

### query.getResultList()

- 결과가 하나 이상일 때 리스트를 반환.
- 결과가 없으면 빈 리스트를 반환.

### query.getSingleResult()

- 결과가 정확히 하나일 때 단일 객체를 반환.
- 결과가 없으면 javax.persistence.NoResultException
- 둘 이상이면 javax.persistence.NonUniqueResultException

## 파라미터 바인딩

### 이름 기준

```
public class JpaMain {

  public static void main(String[] args) {
    TypedQuery<Member> query = em
        .createQuery("SELECT m FROM Member m where m.username=:username", Member.class);

    query.setParameter("username", usernameParam);
  }
}
```

### 위치 기준

```
public class JpaMain {

  public static void main(String[] args) {
    TypedQuery<Member> query = em
        .createQuery("SELECT m FROM Member m where m.username=?1", Member.class);

    query.setParameter(1, usernameParam);
  }
}
```
