# 데코레이터 패턴

- 객체의 결합 을 통해 기능을 동적으로 유연하게 확장 할 수 있게 해주는 패턴

  - 기본 기능에 추가할 수 있는 기능의 종류가 많은 경우에 각 추가 기능을 Decorator 클래스로 정의 한 후 필요한 Decorator 객체를 조합함으로써 추가 기능의 조합을 설계 하는 방식
  - 구조(Structur) 패턴

- 기본 기능에 추가할 수 있는 많은 종류의 부가 기능에서 파생되는 다양한 조합을 동적으로 구현할 수 있는 패턴

![데코레이터 패턴](https://gmlwjd9405.github.io/images/design-pattern-decorator/decorator-pattern.png)

- Component: 기본 기능을 뜻하는 ConcreteComponent와 추가 기능을 뜻하는 Decorator의 공통 기능 정의, 클라이언트는 Component를 통해 실제 객체를 사용.
- ConcreteComponent: 기본 기능을 구현하는 클래스.
- Decorator: 많은 수가 존재하는 구체적은 Decorator 공통 기능 제공
- ConcretaDecoratorA, ConcretaDecoratorB
  - Decorator의 하위 클래스로 기본 기능에 추가되는 개별적인 기능.
  - ConcreteDecorator 클래스는 ConcreteComponent 객체에 대한 참조가 필요한데, 이는 Decorator 클래스에서 Component 클래스로의 '합성(composition)'관계를 통해 표현됨.

> 구조 패턴
>
> - 클래스나 객체를 조합해 더 큰 구조를 만드는 패턴
> - 서로 다른 인터페이스를 지닌 2개의 객체를 묶어 단일 인터페이스를 제공하거나 객체들을 서로 묶어 새로운 기능을 제공하는 패턴

> 합성 관계
>
> - 생성자에서 필드에 대한 객체를 생성하는 경우.
> - 전체 객체의 라이프타임과 부분 객체의 라이프 타임은 의존적.
> - 전체 객체가 없어지면 부분 객체도 없어진다.

## 예시

![예시](https://t1.daumcdn.net/cfile/tistory/186FF13C4E817DEF1E)

- https://github.com/paki1019/starbuzz/tree/main/src/main/java/starbuzz/coffee

> 출처
>
> - https://gmlwjd9405.github.io/2018/07/09/decorator-pattern.html
