# 추상 팩토리 패턴

- 구체적인 클래스에 의존하지 않고 서로 연관되거나 의존적인 객체들의 조합을 만드는 인터페이스를 제공하는 패턴
  - 관련성 있는 여러 종류의 객체를 일관된 방식으로 생성하는 경우에 유용
  - 싱글턴 패턴, 팩토리 메서드 패턴을 사용
  - 생성(Creational) 패턴

![추상 팩토리 패턴](https://gmlwjd9405.github.io/images/design-pattern-abstract-factory/abstract-factory-pattern.png)

- AbstractFactory: 실제 팩토리 클래스의 공통 인터페이스.
- ConcreateFactory: 구체적인 팩토리 클래스로 AbstractFactory 클래스의 추상 메서드를 오버라이드함으로써 구체적인 제품을 생성.
- AbstractProduct: 제품의 공통 인터페이스.
- ConcreateProduct: 구체적인 팩토리 클래스에서 생성되는 구체적인 제품.

> 생성 패턴
>
> - 객체 생성에 관련된 패턴.
> - 객체 생성과 조합을 캡슐화해 특정 객체가 생성되거나 변경되어도 프로그램 구조에 영향을 받지 않도록 유연성을 제공.

## 예시

![예시](https://gmlwjd9405.github.io/images/design-pattern-abstract-factory/abstract-factory-example.png)
