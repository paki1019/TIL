# equals와 hashCode는 왜 같이 재정의 해야하는가?

자바를 사용하는 개발자들은 전부 equals와 hashCode를 같이 재정의하라는 말을 어디선가 들어보셨을 겁니다. 몇몇 IDE의 Generate 기능에서도 equals와 hashCode를 같이 재정의해주고, lombok에서도 EqualsAndHashCode 어노테이션으로 같이 재정의 해주는데요. 그렇다면 왜 equals와 hashCode를 같이 정의해야할까요?

## Object.equals()

Object.equals()는 기본적으로 아래와 같이 구현되어있습니다.

```
# Object.equals() 기본 구현
public boolean equals(Object obj) {
    return (this == obj);
}
```

자바 객체간의 `==` 은 물리적 주소를 비교하기 때문에 아무런 재정의 없는 equals는 동일 메모리 주소를 가진 객체만을 동일하다고 반환하는데요, 우리는 객체의 논리적 동치를 확인하기 위해서 자바 Object클래스의 equals를 아래와 같이 재정의해서 씁니다.

```
# equals 재정의
public class Wiki {
    private String name;
    private String content;

    public Wiki(String name, String content) {
        this.name = name;
        this.content = content;
    }

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
equals와 함께 재정의되는 hashCode()는 아래와 같습니다.

```
# hashCode 재정의
public class Wiki {
    private String name;
    private String content;

    public Wiki(String name, String content) {
        this.name = name;
        this.content = content;
    }

    // intellij Generate 자동 생성
    @Override
    public int hashCode() {
        return Objects.hash(name, content); // name, content 값으로 임의의 해시코드 생성
    }
}
```

## equals만 재정의 하는 경우

문제의 경우를 살펴봅시다. 위의 예제 Wiki 클래스에서 equals만 재정의 했을 경우 아래의 코드에서는 문제점을 찾을 수 없습니다.

```
public static void main(String[] args) {
    Wiki wiki1 = new Wiki("name", "content");
    Wiki wiki2 = new Wiki("name", "content");

    System.out.println(wiki1.equals(wiki2)); // true 출력
}
```

하지만 다음과 같이 Collection 인터페이스를 사용할 경우 문제점을 찾을 수 있습니다.

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

hashCode()를 같이 재정의 하지 않을 경우 위와 같이 코드가 예상과 다르게 작동하는 문제가 확인되었습니다. 정확히는 hash 값을 사용하는 Collection(HashSet, HashMap, HashTable)에서 재현되는데요, 그 이유는 해당 Collection은 아래와 같이 동등성을 비교하기 때문입니다.

![hash Collection 동등성 비교](https://tecoble.techcourse.co.kr/static/c248e8d79140c18ed9895d1c95dd7ad0/54e75/2020-07-29-equals-and-hashcode.png)

hashCode 메서드의 리턴 값이 우선 일치하고 equals 메서드의 리턴 값이 true여야 논리적으로 같은 객체라고 판단합니다.

equals 메서드만 재정의 할 경우 hashCode 메서드의 서로 다른 리턴 값으로 인해 다른 객체로 판단되어 기대와 다른 결과가 나오게 됩니다.

> 참조

- https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/
- https://atoz-develop.tistory.com/entry/%EC%9E%90%EB%B0%94-Object-%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%A0%95%EB%A6%AC-toString-equals-hashCode-clone
