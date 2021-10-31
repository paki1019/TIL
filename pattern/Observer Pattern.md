# 옵저버 패턴

- 한 객체의 상태 변화에 따라 다른 객체의 상태도 연동되도록 일대다 객체 의존 관계를 구성 하는 패턴

  - 데이터의 변경이 발생했을 경우 상대 클래스나 객체에 의존하지 않으면서 데이터 변경을 통보하고자 할 때 유용
  - 행위(Behavioral) 패턴

- 옵저버 패턴은 통보 대상 객체의 관리를 Subject 클래스와 Obaserver 인터페이스로 일반화
  - 이를 통해 데이터 변경을 통보하는 클래스(ConcreteSubject)는 통보 대상 클래스나 객체(ConcreteObserver)에 대한 의존성을 없앨 수 있음.
  - 결과적으로 통보 대상 클래스나 대상 객체의 변경에도 통보하는 클래스(ConcreteSubject)를 수정 없이 그대로 사용할 수 있음.

![옵저버 패턴](https://gmlwjd9405.github.io/images/design-pattern-observer/observer-pattern.png)

- Observer: 데이터의 변경을 통보 받는 인터페이스

  - Subject에서는 Observer 인터페이스의 update 메서드를 호출함으로써 ConcreteSubject의 데이터 변경을 ConcreteObserver에게 통보.

- Subject: ConcreteObserver 객체를 관리하는 요소

  - Observer 인터페이스를 참조해서 ConcreteObserver를 관리하므로 ConcreteObserver의 변화에 독립적.

- ConcreteSubject: 변경 관리 대상이 되는 데이터가 있는 클래스(통보하는 클래스)

  - 데이터 변경을 위한 메서드인 setState 존재.
  - setState 메서드에서는 자신의 데이터인 subjectState를 변경하고 Subject의 notifyObservers 메서드를 호출해서 ConcreteObserver 객체에 변경을 통보.

- ConcreteObserver: ConcreteSubject의 변경을 통보받는 클래스
  - Observer 인터페이스의 update 메서드를 구현함으로써 변경을 통보.
  - 변경된 데이터는 ConcreteSubject의 getState 메서드를 호출함으로써 변경을 조회.

> 행위 패턴
>
> - 객체나 클래스 사이의 알고리즘이나 책임 분배에 관련된 패턴
> - 한 객체가 혼자 수행할 수 없는 작업을 여러 개의 객체로 어떻게 분배하는지, 또 그렇게 하면서도 객체 사이의 결합도를 최소화하는 것에 중점

## 예시

![예시](https://snowdeer.github.io/assets/design-pattern-headfirst/observer_pattern.png)

- https://github.com/paki1019/Weather-O-Rama/tree/main/src/main/java

> 출처
>
> - https://gmlwjd9405.github.io/2018/07/06/strategy-pattern.html
