# Java 버전

## JDK 1.0

- JDK 1.0a, JDK 1.0a2 를 거쳐 JDK 1.0으로 1996년 발표됨.
- 발표 이전에 불렸던 이름은 Oak였으며, 최초 안정화 버전 JDK 1.0.2 버전에서 Java로 이름이 바뀌었음.

## JDK 1.1

- 1997년 Java Development Kit 1.1으로 발표.

### 주요 기능

- JavaBeans 1.0 컴포넌트 아키텍처 추가
- Java Archive (JAR) 파일 형식 추가
- Java Database Connectivity (JDBC) 추가

## J2SE 1.2

- 1998년 Java 2 Standard Edition Software Development Kit으로 발표
- 새로운 GUI, JIT, CORBA 등의 굵직한 기능이 추가되면서 2 부터 약칭을 J2SE(Java 2 Standard Edition) 로 표기(이 표기는 5까지 사용, ava 커뮤니티에서는 여전히 JDK 1.2로 부름.)
- JDK 1.2부터 Java의 제품은 Standard / Enterprise / Micro로 나누어짐.

## J2SE 1.3

- 2000년 발표 Java 2 Standard Edition 1.3으로 발표.
- HotSpot JVM, JNDI, JPDA, JavaSound 등이 추가.

## J2SE 1.4

- 2002년 발표 Java 2 Platform, Standard Edition 1.4으로 발표.
- assert, 정규표현식, IPv6, Non-Blocking IO, XML API, JCE, JSSE, JAAS, Java Web Start 등이 추가
- Java 2 Standard Edition 1.4부터 JCP(Java Community Process)에 의해 오픈소스로 관리.

## J2SE 5

- 2004년 Java Platform, Standard Edition 5.0으로 발표
- 이 때부터 버젼 중 앞의 1을 빼버리고 표기하기 시작. 그러나 내부적으론 1.5, 1.6, 1.7과 같이 데이터를 표시.

### 주요기능

- Generics
- Autoboxing/Unboxing
- Enumerations
- Static imports
- java.util.Scanner

## Java SE 6

- 2006년 Java Platform, Standard Edition 6 (Java SE 6)으로 발표.
- 이 때부터 표기가 J2SE에서 Java SE로 바뀜.

## 오라클의 Sun 인수 발표

- 원래 2년 주기로 발표되던 새로운 버전의 Java가 발표되지 않음.
- 2009년 JavaOne 개발자 회의의 오프닝 세션에서 오라클의 Sun 인수 발표가 나오게 되고, 2010년이 되어서야 JCP는 Java 7과 Java 8 로드맵을 승인.

## Java SE 7

- 2011년 업데이트.
- JDK 7이 나오기까지 Sun 사는 오라클에 인수되는 등 Java는 여러가지 부침을 겪음. 업데이트가 너무 늦어지자 오라클은 일부 명세를 모아서 업데이트를 하기로 결정하였고 이 결과가 JDK 1.7.
- 이때 구현되지 못한 기능은 JDK 8에 추가됨.

### 주요 추가

- 다이아몬드 연산자(<l;>) 사용
- Generics 사용성 개선
- 리소스 자동 해제
- Garbage Collector 기능 개선
- Switch문 문자열 지원

## Java SE 8

- 2014년 업데이트.
- 1.7에서 구현되지 못했던 많은 변화들이 담기게 됨.

### Lambda Expression 추가

- 기존에 익명 클래스로 사용하던 부분을 람다로 더 직관적으로 코드 작성 가능.

```
Runnable runnable = new Runnable(){
  @Override
  public void run(){
    System.out.println("Hello world !");
  }
};
```

```
Runnable runnable = () -> System.out.println("Hello world two!");
```

### Streams 인터페이스 추가

- 컬렉션을 처리하면서 발생하는 `모호함과 반복적인 코드 문제`, `멀티코어 활용 어려움` 문제 해결.

```
List<String> list = Arrays.asList("franz", "ferdinand", "fiel", "vom", "pferd");
list.stream()
    .filter(name -> name.startsWith("f"))
    .map(String::toUpperCase)
    .sorted()
    .forEach(System.out::println);

```

- Default Method 추가
- 새로운 날짜와 시간 API (LocalDateTime, ...)

## Java SE 9

- 2017년 9월 발표.
- 이때부터 오라클 JDK의 릴리즈 주기를 6개월 단위로 하고, LTS 버전을 3년 주기로 내겠다고 선언.
- 9 버전과 10 버전은 non-LTS로 릴리즈되었기 때문에 6개월 무상 업데이트 후 패치가 진행됨.

### Java Platform Module System(Jigsaw) 추가

### 컬랙션

- list, set, map을 쉽게 구성할 수 있는 몇 가지 추가 기능 추가

```java
List<String> list = List.of("one", "two", "three");
Set<String> set = Set.of("one", "two", "three");
Map<String, String> map = Map.of("foo", "one", "bar", "two");
```

- 스트림
  - takeWhile, dropWhile, iterate 메서드의 형태로 몇 가지 추가
  ```java
  Stream<String> stream = Stream.iterate("", s -> s + "s")
  .takeWhile(s -> s.length() < 10);
  ```
- optional
  - ifPresentOrElse 추가
  ```java
  user.ifPresentOrElse(this::displayAccount, this::displayLogin);
  ```

### 인터페이스

- 인터페이스에 private method 사용 가능

```java
public interface MyInterface {

    private static void myPrivateMethod(){
        System.out.println("Yay, I am private!");
    }
}
```

### try-with-resources 문 또는 다이아몬드 연산자(<>) 확장, HTTP클라이언트와 같은 몇 가지 다른 개선 사항 존재.

## Java SE 10

- 2018년 3월 발표.
- non-LTS로 릴리즈.

### 지역 변수 유형 추론: var-keyword

```
// Pre-Java 10
String myName = "Marco";

// With Java 10
var myName = "Marco"
```

### 병렬 처리 가비지 컬렉션 도입으로 인한 성능 향상

### JVM 힙 영역을 시스템 메모리가 아닌 다른 종류의 메모리에도 할당 가능

## Java SE 11

- 2018년 9월 발표. 이클립스 재단으로 넘어간 Java EE가 JDK에서 삭제되고, JavaFX도 JDK에서 분리되어 별도의 모듈로 제공.
- Oracle JDK와 OpenJDK 통합.
- Oracle JDK가 구독형 유료 모델로 전환.
- 서드파티 JDK 로의 이전 필요.
- lambda 지역변수 사용법 변경.

### Strings & Files 메서드 추가.

```
"Marco".isBlank();
"Mar\nco".lines();
"Marco  ".strip();

Path path = Files.writeString(Files.createTempFile("helloworld", ".txt"), "Hi, my name is!");
String s = Files.readString(path);
```

### Run Source Files

- Java 11부터 Java 소스 파일 을 먼저 컴파일 하지 않고도 실행할 수 있음.

```
javac MyScript.java
java MyScript.class
```

```
java MyScript.java
```

### 람다 매개변수에 대한 지역 변수 유형 추론(var)

- 람다 표현식에 var 사용 가능

```
(var firstName, var lastName) -> firstName + lastName
```

## Java SE 12

- 2019년 3월 공개.
- non-LTS로 릴리즈.

### 문법적으로 Switch문을 확장(preview).

```
switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> System.out.println(6);
    case TUESDAY                -> System.out.println(7);
    case THURSDAY, SATURDAY     -> System.out.println(8);
    case WEDNESDAY              -> System.out.println(9);
}
```

### 유니코드 11 지원.

### 가비지 컬렉터 개선, 마이크로 벤치마크 툴 추가, 성능 개선 등.

## Java SE 13

- 2019년 9월 공개.
- 유니코드 12.1 지원.
- non-LTS로 릴리즈.

### 주요 기능

- Java 12에서의 스위치 개선을 이어 yield 라는 예약어가 추가(preview)

```
var a = switch (day) {
    case MONDAY, FRIDAY, SUNDAY:
        System.out.print("Six");
        yield 6;
    case TUESDAY:
        yield 7;
    case THURSDAY, SATURDAY:
        yield 8;
    case WEDNESDAY:
        yield 9;
};
```

## Java SE 14

- 2020년 3월 공개.
- non-LTS로 릴리즈.

### 스위치 표현(Standard)

- 버전 12 및 13에서 preview 였던 스위치 표현식 이 이제 표준화.

```
int numLetters = switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> 6;
    case TUESDAY                -> 7;
    default      -> {
        String s = day.toString();
        int result = s.length();
        yield result;
    }
};
```

### record(preview)

- Java로 많은 상용구를 작성하는 고통을 완화하는 데 도움이 되는 레코드 클래스 등장.
- getter/setters, equals/hashcode, toString만 포함하는 클래스를 record로 선언 가능.

```
final class Point {
    public final int x;
    public final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
// state-based implementations of equals, hashCode, toString// nothing else*
```

```
record Point(int x, int y) { }
```

### NullPointerExceptions 설명 보충.

```
author.age = 35;
---
Exception in thread "main" java.lang.NullPointerException:
     Cannot assign field "age" because "author" is null

```

### Pattern Matching For InstanceOf (Preview)

- 이전에는 다음과 같이 instanceof 내부에서 객체를 캐스팅 필요, 이제 캐스트를 효과적으로 삭제 가능.

```
if (obj instanceof String) {
    String s = (String) obj;
    *// use s*}
```

```
if (obj instanceof String s) {
    System.out.println(s.contains("hello"));
}
```

## Java 15

- 2020년 9월 공개.
- non-LTS로 릴리즈.

### Text-Blocks / Multiline Strings(Finalized)

```
String text = """
                Lorem ipsum dolor sit amet, consectetur adipiscing \
                elit, sed do eiusmod tempor incididunt ut labore \
                et dolore magna aliqua.\
                """;
```

### Sealed Classes(Preview)

- 상속 가능한 클래스를 지정할 수 있는 봉인 클래스가 제공.
- 상속 가능한 대상은 상위 클래스 또는 인터페이스 패키지 내에 속해 있어야 함.

```
public abstract sealed class Shape
    permits Circle, Rectangle, Square {...}
```

## Java SE 16

- 2021년 3월 공개.
- non-LTS로 릴리즈.

### Unix-Domain Socket Channels

- Unix 도메인 소켓에 연결 가능.

```
socket.connect(UnixDomainSocketAddress.of(
    "/var/run/postgresql/.s.PGSQL.5432"));
```

- 등등 추가 필요.

## Java SE 17

- 2021년 9월 출시.
- Java 11 이후의 새로운 LTS 버전.

### Pattern Matching for switch (Preview)

- 객체를 전달하여 기능을 전환하고 특정 유형을 확인 가능.

```
public String test(Object obj) {

    return switch(obj) {

    case Integer i -> "An integer";

    case String s -> "A string";

    case Cat c -> "A Cat";

    default -> "I don't know what it is";

    };

}
```

### Sealed Classes (Finalized)

- Java 15에서 preview 제공되었던 기능 정식 추가.

### Foreign Function & Memory API (Incubator)

- Java Native Interface(JNI)를 대체

### Deprecating the Security Manager

- 자바 1.0 이후로 보안 관리자가 존재해 왔었지만 현재는 더 이상 사용되지 않으며 향후 버전에서는 제거될 예정
