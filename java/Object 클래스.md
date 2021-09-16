# Object 클래스

## java.lang.Object 클래스

- java.lang 패키지는 자바에서 가장 기본적인 동작을 수행하는 클래스들의 집합
- java.lang 패키지 중에서도 가장 많이 사용되는 클래스가 바로 Object 클래스
- Object 클래스는 모든 자바 클래스의 최고 조상 클래스가 됨.
- Object 클래스는 필드를 가지지 않으며, 총 11개의 메소드만으로 구성

## getClass() 메소드

- 객체의 Class 정보를 반환

## toString() 메소드

- toString() 메소드는 해당 인스턴스에 대한 정보를 문자열로 반환함.
- Object 클래스에서는 아래와 같이 정의되어 있음.

```
return getClass().getName() + "@" + Integer.toHexString(hashCode());
```

## 이외 메소드

- 이외에도 clone, notify, notifyAll, wait 등 여러 메서드 존재

## equals() 메소드

- 일반적으로 equals() 메소드는 매개변수로 전달받는 객체와의 내용을 비교, 내용이 같을 경우 true를 반환, 다를 경우 false를 반환하는걸로 알려져 있음.
- Object 클래스에서는 아래와 같이 정의가 되어있어서, 같은 내용이여도 주소값이 다르면 false를 반환함.

```
return (this == obj);
```

### equals 재정의

- 논리적 동치성을 확인해야하지만 상위 클래스(Object)의 equals가 재정의 되어있지 않을때 equals를 재정의 할 필요가 있음.
- 주로 값 클래스(String, Integer)에서 재정의 해서 사용함.
- equals가 논리적 동치성을 확인하도록 재정의 해두면, 그 인스턴스 값의 비교가 가능하고 Map의 key와 Set의 원소로 사용할 수 있다.

### 값 클래스 중 equals를 재정의하지 않아도 되는 경우

- 인스턴스가 둘 이상 만들어지지 않음을 보장하는 클래스
- 예시로 Enum

### Equals 메서드 규약

- 반사성(reflexivity) : x.equals(x)는 true
- 대칭성(symmetry) : x.equals(y)가 true이면 y.equals(x)도 true
- 추이성(transitivity) : x.equals(y)는 true이고 y.equals(z)는 true이면 x.equals(z)는 true
- 일관성(consistency) : x.equals(y)를 반복해서 호출해도 항상 true 또는 false를 반환
- null-아님 : x.equals(null)는 false

### 리스코프 치환 원칙(Liskov substitution principle)

- 어떤 타입에 있어 중요한 속성이면 그 하위 타입에서도 중요하다.
- 그 타입의 모든 메서드가 하위 타입에도 똑같이 잘 작동해야 한다.
- "Point의 하위 클래스는 여전히 Point이므로 어디서든 Point로써 활용될 수 있어야 한다."

### 양질의 equals 메서드 구현 방법

1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
2. instanceof 연산자로 입력이 올바른 타입인지 확인한다.
3. 입력을 올바른 타입으로 형변환한다.(2번에서 instanceof 검사를 했으니 100% 성공한다.)
4. 입력 객체와 자기 자신의 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사한다.
5. equals를 재정의할 땐 hashCode도 반드시 재정의한다

## hashCode() 메소드

- hashCode()는 heap에 할당된 객체의 메모리 주소값을 반환함.

### hashCode 규약

- equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 객체의 hashCode 메서드는 몇 번을 호출해도 항상 같은 값을 반환한다. (단, 애플리케이션을 다시 실행하면 값은 바뀔 수 있다.)
- equals(Object)가 두 객체를 같다고 판단했으면, 두 객체의 hashCode 값은 항상 같다.
- 하지만 equals(Object)가 두 객체를 다르다고 판단했더라도, 두 객채의 hashCode 값은 같을 수 있다. (해시 충돌)
  - 해시 테이블에서 해시 충돌이 발생하면 LinkedList 형태로 객체를 추가한다.
  - 단, 다른 객체에 대해서는 다른 값을 반환해야 해시테이블의 성능이 좋아진다.
  - 좋은 해시 함수라면 서로 다른 인스턴스에 대해 다른 해시코드를 반환한다.

### hashCode를 재정의 하지 않았을 경우 생기는 문제점

- 같은 값을 가진 객체가 서로 다른 해시값을 갖게 될 수 있다.
- HashMap의 key 값으로 해당 객체를 사용할 경우 문제가 발생

```
@Test
@DisplayName("같은 값 객체는 해시값이 같은지 체크")
void hashcode_menu() {
    //given
    Map<Menu, Integer> menus = new HashMap<>();
    menus.put(new Menu("치킨", 16_000), 10);
    menus.put(new Menu("감자튀김", 8_000), 2);
    menus.put(new Menu("콜라", 2_000), 7);
    //when
    Menu menu = new Menu("치킨", 16_000);
    int count = menus.get(menu);
    //then
    assertThat(count).isEqualTo(10); // NullPointerException 발생
}
```

- HashMap의 key값으로 Menu 클래스를 사용하기 위해서는 Menu 클래스에 hashCode() 메서드를 재정의 해줘야한다.

> 출처
>
> - http://tcpschool.com/java/java_api_object
> - https://velog.io/@sonypark/Java-equals-hascode-%EB%A9%94%EC%84%9C%EB%93%9C%EB%8A%94-%EC%96%B8%EC%A0%9C-%EC%9E%AC%EC%A0%95%EC%9D%98%ED%95%B4%EC%95%BC-%ED%95%A0%EA%B9%8C#hashcode-%EB%A9%94%EC%84%9C%EB%93%9C
