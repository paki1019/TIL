# @MappedSuperclass

부모 클래스는 테이블과 매핑하지 않고 부모 클래스를 상속 받는 자식 클래스에게 매핑 정보만 제공하고 싶을 때 `@MappedSuperclass`를 사용. `@MappedSuperclass`는 실제 테이블과는 매핑되지 않음. 단순히 매핑 정보를 상속할 목적으로만 사용.

![@MappedSuperclass 설명 객체](https://incheol-jung.gitbook.io/~/files/v0/b/gitbook-28427.appspot.com/o/assets%2F-M5HOStxvx-Jr0fqZhyW%2F-M6xBNg_qefrpc7aASBC%2F-M6xPbxY5i4xiOPkL66O%2F7-5.png?alt=media&token=9717d4fc-157e-44e0-a3c6-d39d12183784)

```
@MappedSuperclass
public abstract class BaseEntity {
  @Id @GeneratedValue
  private Long id;
  private String name;
  ...
}

@Entity
public class Member extends BaseEntity {

  //ID 상속
  //NAME 상속

  private String email;
  ...
}

@Entity
public class Seller extends BaseEntity {

  //ID 상속
  //NAME 상속

  private String shopName;
  ...
}
```

부모로부터 물려받은 매핑 정보를 재정의하려면 @AttributeOverrides나 @AttributeOverride를 사용하고, 연관관계를 재정의하려면 @AssociationOverrides나 @AssociationOverride를 사용

특징

- 테이블과 매핑 되지 않고 자식 클래스에 엔티티의 매핑 정보를 상속하기 위해 사용.
- `@MappedSuperclass`로 지정한 클래스는 엔티티가 아니므로 em.find()나 JPQL에서 사용할 수 없음.
- 이 클래스를 직접 생성해서 사용할 일은 거의 없으므로 추상 클래스로 만드는 것을 `권장`.
