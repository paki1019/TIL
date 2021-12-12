자바를 사용하는 개발자들은 전부 equals와 hashCode를 같이 재정의하라는 말을 어디선가 들어보셨을 겁니다.  
IDE의 Generate 기능에서도 equals와 hashCode를 같이 재정의해주고, lombok에서도 EqualsAndHashCode 어노테이션으로 같이 재정의 해주는데요. 그렇다면 왜 equals와 hashCode를 같이 정의해야할까요?

## Object.equals()

Object.equals()는 기본적으로 아래와 같이 구현되어있습니다.

```
# Object.equals() 기본 구현
public boolean equals(Object obj) {
    return (this == obj);
}
```

자바 객체간의 `==` 은 동일 메모리 주소를 가진 객체만을 동일하다고 반환합니다.
그러나 개발을 하다 보면 객체의 물리적 주소가 아닌 객체의 논리적 동치를 확인해야할 필요가 있는데요, 이를 확인하기 위해 우리는 equals를 아래와 같이 재정의해서 사용하곤 합니다.

```
# Wiki class equals 재정의
public class Wiki {
    private String name;
    private String content;

    // intellij Generate 자동 생성
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Wiki wiki = (Wiki) o;
        return Objects.equals(name, wiki.name) && Objects.equals(content, wiki.content); // 클래스 타입이 동일하고, name, content 값이 동일하면 true 반환
    }
}
```

## Object.hashCode()

Object.hashCode()는 JVM이 해당 객체에 부여한 코드값, 인스턴스가 저장된 가상머신의 메모리 주소를 10진수로 반환합니다.
이는 결국 물리적 주소이기 떄문에 논리적 동등성을 비교하기 위해선 아래와 같이 별개의 해시 함수로 재정의 하곤합니다.

```
# hashCode 재정의
public class Wiki {
    private String name;
    private String content;

    // intellij Generate 자동 생성
    @Override
    public int hashCode() {
        return Objects.hash(name, content); // name, content 값으로 임의의 해시코드 생성, 내부적으론 Arrays.hashCode로 해싱
    }
}
```

## equals와 hashCode 둘중 하나만 재정의하여 문제가 발생하는 경우

위의 Wiki 클래스에서 equals 만 재정의했다고 가정해봅시다.

```
public static void main(String[] args) {
    List<Wiki> wikiList = new ArrayList<>();
    wikiList.add(new Wiki("name", "content"));
    wikiList.add(new Wiki("name", "content"));

    System.out.println(wikiList.size()); // 2 출력 O

    Set<Wiki> wikiSet = new HashSet<>();
    wikiSet.add(new Wiki("name", "content"));
    wikiSet.add(new Wiki("name", "content"));

    System.out.println(wikiSet.size()); // 1 출력 예상 그러나 2가 출력됨.
}
```

위의 경우 ArrayList인 wikiList에 동일한 Wiki 객체를 2개 추가하였습니다. List의 경우 동일한 객체를 중복해서 넣는 것이 가능하기 때문에 예상한대로 wikiList.size()가 2가 출력되었습니다.
반면 HashSet으로 wikiSet을 할당했고 똑같이 동일한 Wiki Wiki 객체를 2개 추가했습니다. Set은 중복 객체의 추가를 무시하기 때문에 wikiSet.size()가 1이 나올것으로 예상했으나 2가 출력되었습니다.

이는 다른 Hash Collection인 HashMap이나 HashTable에서도 비슷한 문제가 발생합니다. 왜 이런 결과가 나오는 걸까요?

## hash Collection 동등성 비교

![image](https://media.oss.navercorp.com/user/15373/files/121fcc80-5912-11ec-8239-a2828b1a6aa9)

그 이유는 Hash Collection은 위와 같이 동일 객체 여부를 판단하기 때문입니다.

hash Collection에서 동일 객체를 판단할 때는 먼저 hashCode()의 리턴 값을 비교하고, 그 다음 equals()의 리턴값을 비교합니다.

equals 메서드만 재정의 할 경우 hashCode 메서드의 서로 다른 리턴 값으로 인해 다른 객체로 판단되어 기대와 다른 결과가 나오게 되며, hashCode 메서드만 재정의 할 경우에도 마찬가지로 equals의 서로 다른 리턴 값으로 인해 다른 객체로 판단하게 됩니다.

## Hash Collection만 문제인가

Hash Collection을 사용하지 않으면 되지 않는가 라고 생각할 수도 있지만,
협업하는 환경에서 자기 이외의 사랑이 Hash Collection을 사용할 수도 있고
또한 외부 라이브러리(예를 들어 MapStruct) 같은 데서도 객체 내부의 equals와 hashCode를 사용하는 경우가 있기때문에 불가피한 상황이 아니라면 함께 재정의하는것이 바람직한 코드라고 생각합니다.

> 참조

- https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/
- https://atoz-develop.tistory.com/entry/%EC%9E%90%EB%B0%94-Object-%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%A0%95%EB%A6%AC-toString-equals-hashCode-clone
