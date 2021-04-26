# 영속성 전이 cascade

## 영속성 전이

특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속 상태로 만들기 위해 사용. 영속성 전이를 사용하면 부모 엔티티를 저장할 때 자식 엔티티도 함께 저장할 수 있다.

```
@Entity
@Data
public class Parent {

  @Id @GeneratedValue
  private Long id;

  @OneToMany(mappedBy = "parent")
  private List<Child> children = new ArrayList<>();
}

@Entity
@Data
public class Child {

  @Id @GeneratedValue
  private Long ig;

  @ManyToOne
  private Parent parent;
}
```

이와 같은 엔티티 구조에서 1명의 부모와 2명의 자식을 저장할 때 기본적으론 3번의 persist를 해야함.

```
private static void saveNoCascade(EntityManager entityManager){
  Parent parent = new Parent();
  entityManager.persist(parent);

  Child child1 = new Child();
  child1.setParent(parent);
  entityManager.persist(child1);

  Child child2 = new Child();
  child2.setParent(parent);
  entityManager.persist(child2);
}
```

## 영속성 전이 (저장)

```
@Entity
@Data
public class Parent {

  @Id @GeneratedValue
  private Long id;

  @OneToMany(mappedBy = "parent" ,cascade = CascadeType.PERSIST)
  private List<Child> children = new ArrayList<>();
}
```

위와 같이 cascade = CascadeType.PERSIST 로 지정하면 부모를 영속화할 때 연관된 자식들도 함께 영속화 됨.

```
private static void saveWithCascade(EntityManager entityManager){
  Child child1 = new Child();
  Child child2 = new Child();

  Parent parent = new Parent();
  child1.setParent(parent);
  child2.setParent(parent);

  parent.getChildren().add(child1);
  parent.getChildren().add(child2);

  entityManager.persist(parent);
}
```

### 영속성 전이 (삭제)

```
@OneToMany(mappedBy = "parent" ,cascade = CascadeType.REMOVE)
private List<Child> children = new ArrayList<>();
```

```
private static void deleteWithCascade(EntityManager entityManager){
  Parent parent = entityManager.find(Parent.class, 1L);
  entityManager.remove(parent);
}
```

영속성 전이 CascadeType.REMOVE를 사용하면 부모 엔티티와 연관된 자식 엔티티도 함께 삭제 됨

## 고아 객체

JPA는 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제하는 기능을 제공하는데 이것을 고아 객체 제거라 함. 이 기능을 사용해서 부모 엔티티의 컬렉션에서 자식 엔티티의 참조만 제거하면 자식 엔티티가 자동으로 삭제 됨.

```
@OneToMany(mappedBy = "parent", orphanRemoval = true)
private List<Child> children = new ArrayList<>();
```

```
private static void deleteOrphan(EntityManager entityManager){
  Parent parent = entityManager.find(Parent.class, 1L);
  parent.getChildren().remove(0);
}
```

orphanRemoval 옵션은 @OneToOne 또는 @OneToMany에서만 사용할 수 있다.

## 영속성 전이 + 고아 객체

orphanRemoval = true 와 CascadeType.ALL을 동시에 사용하면 부모 엔티티를 통해서 자식의 생명주기까지 관리할 수 있다.

```
Parent parent = entityManager.find(Parent.class, parentId);
parent.addChild(child1);
```

자식을 저장하려면 부모에 등록만 하면 된다.

```
Parent parent = entityManager.find(Parent.class, parentId);
parent.getChildren().remove(child1);
```

자식을 삭제하려면 부모에서 제거하면 된다.
