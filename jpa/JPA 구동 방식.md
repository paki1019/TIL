# JPA 구동 방식

![JPA 구동 방식](https://media.vlpt.us/images/tmdgh0221/post/bf85fa44-6452-4f03-8045-be8ab11380d0/operation_way.PNG)

- 먼저 persistence.xml 파일을 조회해서 설정에 맞게 DB를 구성.
- DB에 접근할 때 매번 커넥션을 생성해주는 EntityManagerFactory를 생성. 이는 각 DB당 하나만 생성해서 Application 전체에서 공유.
- 각 커넥션(액세스) 때마다 EntityManager가 생성되어 트랜잭션을 처리한 후 소멸. 이는 쓰레드 간에 공유하면 안되며 각각의 커녁션마다 생성하고 다 사용했다면 버려야 함.

## 객체와 테이블을 생성하고 매핑

```
# SQL DDL
create table Member (
  id bigint not null,
  name varchar(255),
  primary key (id)
);
```

위 ddl은 아래 class에 매팽됨.

```
# Entity class
package hellojpa;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Member {

    @Id
    private Long id;
    private String name;
}

```

## JPA 동작 확인

```
public static void main(String[] args) {
// persistence.xml의 persistence-unit name 속성과 일치해야 한다.
EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

    // 각 액세스마다 생성한다.
    EntityManager em = emf.createEntityManager();

    // JPA의 모든 데이터 변경은 트랜잭션 안에서 실행되어야 한다.
    EntityTransaction tx = em.getTransaction();
    tx.begin();

    try {
        /*
         * create new member
         */
        Member newMember = new Member();
        newMember.setId(1L);
        newMember.setName("memberA");
        em.persist(newMember);

        /*
         * read member
         */
        Member findMember = em.find(Member.class, 1L);
        System.out.println("findMember = " + findMember.getId() + ": " + findMember.getName());

        /*
         * update member
         */
        Member updateMember = em.find(Member.class, 1L);
        updateMember.setName("memberX");

        /*
         * delete member
         */
        Member deleteMember = em.find(Member.class, 1L);
        em.remove(deleteMember);

        /*
         * read all members by JPQL
         * SQL은 DB 테이블을 대상으로 쿼리를 작성하지만
         * JPQL은 객체를 대상으로 검색하는 객체 지향 쿼리이다.
         */
        List<Member> members = em.createQuery("select m from Member as m", Member.class)
                .getResultList();
        for (Member member: members) {
            System.out.println("member = " + member.getId() + ": " + member.getName());
        }

        // SQL을 모와서 한번에 보낸다.
        tx.commit();
    } catch (Exception e) {
        // error가 발생한다면 DB를 롤백한다.
        tx.rollback();
    } finally {
        // DB 커넥션을 닫는다.
        em.close();
    }

    emf.close();
}

```

> 출처
>
> - https://www.inflearn.com/course/ORM-JPA-Basic
> - https://velog.io/@tmdgh0221/JPA-%EA%B8%B0%EB%B3%B8%ED%8E%B8-%EC%A0%95%EB%A6%AC
