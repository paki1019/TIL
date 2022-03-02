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

## 프로젝션

- 엔티티 프로젝션(멤버 조회)
  - `SELECT m FROM Member m ...`
- 엔티티 프로젝션(멤버 안에 있는 팀 조회)
  - `SELECT m.team FROM Member m ...`
- 단순 값 프로젝션
  - hibernate에서 지원을 해서 username, age로 쓸 수 있지만, 공식적으로는 m.username, m.age로 접근해야 함.
  - `SELECT m.username, m.age FROM Member m ...`
- new 명령어
  - 단순 값을 DTO로 바로 조회
  - new 패키지명, DTO를 넣고 생성자처럼 사용해서 DTO로 바로 반환 받을 수 있음.
  - `SELECT new jpabook.jpql.UserDTO(m.username, m.age) FROM Member m ...`
- DISTINCT는 중복을 제거함.

## 페이징 API

- JPA는 페이징을 다음 두 API로 추상화.
- 조회 시작위치(0부터 시작)
  - setFirstResult(int startPosition)
- 조회할 데이터 수
  - setMaxResults(int maxResult)
- 페이징 쿼리 예시

  ```
  String jpql = "select m from Member m order by m.name desc";
  ​
  List<Member> resultList = em.createQuery(jpql, Member.class)
  .setFirstResult(10)
  .setMaxResult(20)
  .getResultList();

  ```

- 모든 데이터 베이스의 방언이 동작.

## 집합과 정렬

- 기본적인 집합 명령어 다 동작함.
  ```
  select
      COUNT(m),   //회원수
      SUM(m.age), //나이 합
      AVG(m.age), //평균 나이
      MAX(m.age), //최대 나이
      MIN(m.age)  //회소 나이
  from Member m
  ```
- GROUP BY, HAVING
- ORDER

## 조인

- 내부 조인
  - 멤버 내부의 팀에 m.team으로 접근
  ```
  SELECT m
  FROM Member m
  [INNER] JOIN m.team t
  ```
- 외부 조인
  ```
  SELECT m
  FROM MEMBER m
  LEFT [OUTER] JOIN m.team t
  ```
- 세타 조인
  - 연관관계 상관 없이 조인
  ```
  SELECT COUNT(m)
  FROM Member m, Team t
  WHERE m.username = t.name
  ```

## 페치 조인

- fetchType을 LAZY로 다 세팅 해놓고, 쿼리 튜닝할때 한꺼 번에 조회가 필요한 경우 페치 조인을 사용.
- 엔티티 객체 그래프를 한번에 조회하는 방법.
- 별칭을 사용할 수 없음.
- JPA N+1 문제를 해결.
- `select m from Member m join fetch m.team`
- JPA 최신 버전에는 페치 조인과 유사한 엔티티 그래프라는 기능도 제공.

## 서브 쿼리

- 예시1
  ```
  select m from Member m
  where m.age > (select avg(m2.age) from Member m2)
  ```
- 예시2
  ```
  select m from Member m
  where (select count(o) from Order o where m = o.member) > 0
  ```
- 서브쿼리에서 지원하는 함수
  - [NOT] EXISTS (서브쿼리) : 서브쿼리에 결과가 존재하면 참
  - {ALL | ANY | SOME} (서브쿼리) : ALL은 모두 만족할 때, ANY와 SOME은 조건을 하나라도 만족할 때 참
  - [NOT] IN (서브쿼리) : 서브쿼리 결과 중 하나라도 같은 값이 있으면 참

### JPA 서브쿼리의 한계

- JPA는 where, having 절에서만 서브 쿼리 사용 가능
- select 절에서도 서브쿼리 사용은 가능(하이버네이트에서 지원)
- from 절의 서브쿼리(인라인뷰)는 현재 JPQL에서 불가능하므로 조인으로 풀 수 있다면 조인으로 해결.

## JPQL 타입 표현

- 문자 : 'HI', 'He''s' -> ' '안에 (')을 넣어야 할 때는 작은 따옴표 두 개 사용해서 표현
- 숫자 : 10L(Long), 10D(Double), 10F(Float)
- Boolean : true, false
- enum : 패키지명을 모두 포함해서 적어야 함.

  ```
  public enum MemberType{
  ADMIN, USER;
  }
  public class Member{

  	@Enumerated(EnumType.STRING)
  	private MemberType type;
    // 기본이 EnumType.ORDINAL이므로 항상 STRING으로 변경해주어야 함
  }
  // jpql.MemberType.ADMIN 처럼 enum의 패키지명을 적어야 함.
  String query = "select m.username from Member m " +
  			"where m.type = jpql.MemberType.ADMIN";
  ```

- 엔티티타입 : Type(m) = Member (상속 관계에서 사용)

## SQL 과 문법이 같은 식

- EXISTS, IN
- AND, OR, NOT
- =, >, >= , like between 등

## 조건식 - CASE

```
String query =
"select
	case when m.age <= 10 then '학생요금'
    	 when m.age >= 60 then '경로요금'
         else '일반요금'
    end
from Member m";
List<String> result = em.createQuery(query, String.class).getResultList();
```

- COALESCE : 하나씩 조회하다가 NULL이 아니면 반환
- NULLIF : 두 값이 같으면 NULL 반환, 다르면 첫째 값 반환

```
SELECT COALESCE(m.username, '이름 없는 회원') from Member m

select NULLIF(m.username, '관리자') from Member m
```

## JPQL 기본 함수

- CONCAT (문자 더하기) select 'a' || 'b' from Member m 또는 concat('a','b')
- SUBSTRING (문자 잘라내기)
- TRIM (공백제거)
- LOWER
- UPPER
- LENGTH
- LOCATE : loacate('de', 'abcdef')는 4 반환 (1부터 시작)
- ABS, SQRT, MOD
- SIZE, INDEX(JPA 용도) : select size(t.members) from Team t 는 t의 멤버 수 반환
  - index는 값 타입에서 컬렉션의 위치 값을 구하는 함수, 안 쓰는 것이 좋음.

## 사용자 정의 함수 호출

- 기본함수를 사용할 수 없을 때 사용자 정의 함수를 호출. 사용하는 DB 방언을 상속받고 사용자 정의 함수를 등록할 수 있음.

```
public class MyH2Dialect extends H2Dialect {
	public MyH2Dialect(){
    	registerFunction("group_concat", new StandardSQLFunction("group_concat", StandardBasicTypes.STRING));
    }
}


String query = "select function("group_concat", m.username) from Member m;
List<String> result = em.createQuery(query, Integer.class).getResultList();
//관리자1, 관리자 2 라는 하나의 String으로 반환됨
```

## 경로 표현식

```
select m.username -> 상태필드
from Member m
join m.team t     -> 단일 값 연관 필드
join m.orders o   -> 컬렉션 값 연관 필드
where t.name = 'A'
```

.(점)을 찍어 객체 그래프를 탐색하는 것.

### 경로 표현식 용어 정리

- 상태필드 : 단순히 값을 저장하기 위한 필드 (m. username)
- 연관필드 : 연관관계를 위한 필드
  - 단일 값 연관 필드 (@ManyToOne, @OneToOne, 대상이 Entity[m.team])
  - 컬렉션 값 연관 필드 (@OneToMany, ManyToMany, 대상이 컬렉션[m.orders])

### 경로 표현식 특징

- 상태필드 : 경로 탐색의 끝, 탐색X
- 단일 값 연관 경로 : 묵시적 내부 조인(inner join) 발생, 탐색O
  - 조심해서 사용. 튜닝하기 힘들다.
- 컬렉션 값 연관 경로 : 묵시적 내부 조인 발생, 탐색 X
  - 컬렉션이기 때문에 내부 탐색이 안되며 SIZE밖에 안된다.
- From 절에서 명시적 조인을 통해 별칭을 얻으면 별칭을 통해 탐색이 가능함.

```
Member member1 = new Member();
member1.setUsername("관리자1");
em.persist(member1);

Member member2 = new Member();
member2.setUsername("관리자1");
em.persist(member2);

em.flush();
em.clear();

// 상태필드 username에서 더이상 참조할 수 있는게 없다.
String query1 = "select m.username from Member m";
List<String> result = em.createQuery(query1, String.class).getResultList();

// 묵시적인 내부 조인 발생 ** 중요, 탐색이 가능하다. m.team.name
String query2 = "select m.team from Member m";
List<Team> teamResult = em.createQuery(query2, Team.class).getResultList();

// 묵시적 내부 조인 발생, 탐색이 안된다.
String query3 = "select t.members from Team t";
// From절에서 명시적으로 조인을 통해 탐색이 가능하다.
String query31 = "select m.team.name from Team t join t.members m";
List result = em.createQuery(query31, Collection.class).getResultList();
```

### 주의 사항

- 실무에서는 묵시적 조인을 사용X, 명시적 조인을 사용해야 튜닝하기 쉬움.
