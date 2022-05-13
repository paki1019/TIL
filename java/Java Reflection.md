# Java Reflection

자바 리플렉션은 자바에서 기본적으로 제공하는 API로, 구체적인 클래스 타입을 알지 못해도 그 클래스의 정보(메서드, 타입, 변수 등등)에 접근할 수 있게 해주는 API이다.

## 예제 코드

- 자바 특징 중 하나인 다형성은 아래와 같이 상위 클래스 참조 변수로 하위 클래스 객체를 참조가 가능케 함.

```java
public static void main(String[] args) {
    Object obj = new Car("foo", 0);
}
```

- 자바는 컴파일 타임에 타입이 결정되기 때문에 obj라는 이름의 객체는 컴파일 타임에 Object로 타입이 결정됐기 때문에 Object 클래스의 인스턴스 변수와 메서드만 사용할 수 있음.

```java
public static void main(String[] args) {
    Object obj = new Car("foo", 0);
    obj.move();    // 컴파일 에러 발생 java: cannot find symbol
}
```

- 하지만 아래와 같이 Java Reflection API를 사용한다면 가능함.

```java
public static void main(String[] args) throws Exception {
    Object obj = new Car("foo", 0);
    Class carClass = Car.class;
    Method move = carClass.getMethod("move");

    // move 메서드 실행, invoke(메서드를 실행시킬 객체, 해당 메서드에 넘길 인자)
    move.invoke(obj, null);

    Method getPosition = carClass.getMethod("getPosition");
    int position = (int)getPosition.invoke(obj, null);
    System.out.println(position);
    // 출력 결과: 1
}
```

## 동작 원리

- 자바에서는 JVM이 실행되면 사용자가 작성한 자바 코드가 컴파일러를 거쳐 바이트 코드로 변환되어 static 영역에 저장됨.
- Reflection API는 이 static 영역을 뒤져서 정보를 가져올 수 있는 것.

## 주의사항 및 단점

- 컴파일 타임이 아닌 런타임에 동적으로 타입을 분석하고 정보를 가져오므로 JVM을 최적화할 수 없기 때문에 성능 오버헤드가 약간 존재함.
- 직접 접근할 수 없는 private 인스턴스 변수, 메서드에 접근하기 때문에 내부를 노출하면서 추상화가 깨질 위험이 있음.

## 활용처

- 위와 같은 주의사항 때문에 Java Reflection은 애플리케이션 개발보다는 주로 프레임워크나 라이브러리에서 많이 사용됨. 프레임워크나 라이브러리는 사용자가 어떤 클래스를 만들지 예측할 수 없기 때문에 동적으로 해결해주기 위해 Reflection을 사용함.
- 실제로 intellij의 자동완성, jackson 라이브러리, Hibernate 등등 많은 프레임워크나 라이브러리에서 Reflection을 사용
- Spring Framework에서도 Reflection API를 사용하는데 대표적으로 Spring Container의 BeanFactory. Bean은 애플리케이션이 실행한 후 런타임에 객체가 호출될 때 동적으로 객체의 인스턴스를 생성하는데 이때 Spring Container의 BeanFactory에서 리플렉션을 사용.
- Reflection API로 생성자의 인자 정보는 가져올 수 없기 때문에 Reflection을 통한 객체 생성을 위해서 기본 생성자를 필요로 함. (MyBatis, Spring Data Jpa)

> 출처
>
> - https://tecoble.techcourse.co.kr/post/2020-07-16-reflection-api/
