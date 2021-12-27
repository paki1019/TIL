# 정적 팩토리 메서드(Static factory method)

정적 팩토리 메서드는 클래스를 정의할 때 생성자가 아닌 다른 메서드로 객체생성을 하는 기법.
책 Effective Java에서는 생성자 대신 정적 팩토리 메서드를 권장하고 있음.

## 정적 팩토리 메서드의 장점

### 정적 팩토리 메서드는 이름을 가질 수 있음.

`new` 키워드를 통해 객체를 생성하는 생성자는 내부 구조를 잘 알고 있어야 목적에 맞게 객체를 생성할 수 있으나 정적 팩토리 메서드를 사용하면 메서드 이름에 객체의 생성 목적을 담아 낼 수 있음.

```
# 정적 팩토리 메서드의 장점
public class User {
    private Long id;
    private String name;
    private String nickName;
    private boolean isAdmin;

    public static User newUser(String name, String nickName) {
        User user = new User();
        user.name = name;
        user.nickName = nickName;
        user.isAdmin = false;
        return user;
    }

    public static User newAdminUser(String name, String nickName) {
        User user = new User();
        user.name = name;
        user.nickName = nickName;
        user.isAdmin = true;
        return user;
    }
}
```

위 코드에선 정적 팩토리 메서드를 사용해 일반 유저와, 어드민 유저의 생성 메서드를 구분하였음. 실제 User 클래스의 필드 `isAdmin`의 존재를 모르더라도 메서드 만으로 원하는 권한의 User를 생성할 수 있음.

### 정적 팩토리 메소드를 쓰면 호출할 때마다 새로운 객체를 생성할 필요가 없음.

아래 코드와 같이 정적으로 미리 생성해둔 객체를 재사용할 수도 있음.

```
public class User {

    // admin 유저는 유일 객체라 가정(싱글톤)
    // 유일 객체 뿐만 아니라 캐싱용 collection 등도 사용 가능
    static final User ADMIN_USER = new User("admin", "admin", true);

    private Long id;
    private String name;
    private String nickName;
    private boolean isAdmin;

    ...

    public static User getAdminUser(String name, String nickName) {
        return ADMIN_USER;
    }
}
```

위 코드와 같이 클래스에 정적으로 미리 생성해둔 객체를 반환하도록 생성 메서드를 만들수도 있음. 여담으로 싱글톤 패턴에서도 위와 동일한 패턴을 사용함.

### 하위 자료형 객체를 반환할 수 있음.

위 예제의 `User` 클래스를 상속받는 `Teenager`, `Adult` 클래스가 있다고 가정, 아래와 같이 정적 팩토리 메서드내 분기처리를 할 수 있음.

```
public class User {

    // Teenager, Adult 메서드가 User를 상속받음.

    private Long id;
    private String name;
    private String nickName;
    private boolean isAdmin;
    private int age;

    ...

    public static User getUser(String name, String nickName, int age) {
        if (age < 20) {
            User teenager = new Teenager();
            teenager.name = name;
            teenager.nickName = nickName;
            teenager.age = age;
            return teenager;
        } else {
            User adult = new Adult();
            adult.name = name;
            adult.nickName = nickName;
            adult.age = age;
            return adult;
        }
    }
}
```

### 객체 생성을 캡슐화 할 수 있음.

정적 팩토리 메서드는 객체 생성을 캡슐화하는 방법이기도 함. 위에서도 얘기했듯이 내부 상태를 외부에 드러낼 필요없이 객체 생성 인터페이스 단순화 시키는 효과도 가짐.

```
public class UserDto {
    private String name;
    private String nickName;
    private int age;

    # private 생성자
    private UserDto(String name, String nickName, int age) {
        this.name = name;
        this.nickName = nickName;
        this.age = age;
    }


    public static UserDto from(User user) {
        return new UserDto(user.getName(), user.getNickName(), user.getAge());
    }
}
```

위 코드와 같이 생성자를 외부에 드러내지 않고도 객체를 생성할 수 있는 장점이 있음.

## 정적 팩토리 메서드 네이밍 컨벤션

- from : 하나의 매개 변수를 받아서 객체를 생성.
- of : 여러개의 매개 변수를 받아서 객체를 생성.
- getInstance | instance : 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음.
- newInstance | create : 새로운 인스턴스를 생성.
- get[OtherType] : 다른 타입의 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음.
- new[OtherType] : 다른 타입의 새로운 인스턴스를 생성.

## 결론

이와 같이 정적 팩토리 메서드는 단순 생성자를 넘어서 좀더 가독성 좋고 객체지향적으로 좋은 개발을 할 수 있도록 도와 줌. 객체간 형 변환이 필요하거나, 여러 번의 객체 생성이 필요한 경우라면 생성자보다는 정적 팩토리 메서드를 사용하는것이 더 좋아보임.
