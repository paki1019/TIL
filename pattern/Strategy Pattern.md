# 스트래티지 패턴

- 행위를 클래스로 캡슐화해 동적으로 행위를 자유롭게 바꿀 수 있게 해주는 패턴.

  - 같은 문제를 해결하는 여러 알고리즘이 클래스별로 캡슐화되어있고 필요할 때 이를 교체함으로써 여러 알고리즘으로 동일한 문제를 해결할 수 있게 하는 디자인 패턴.
  - 행위(Behavioral) 패턴

- 전략을 쉽게 바꿀수 있도록 해주는 디자인 패턴
  - 전략: 어떤 목적을 달성하기 위해 일을 수행하는 방식, 비즈니스 규칙, 문제를 해결하는 알고리즘

![스트래티지 패턴](https://gmlwjd9405.github.io/images/design-pattern-strategy/strategy-pattern.png)

- Strategy: 인터페이스나 추상 클래스로 구현, 외부에서 동일한 방식으로 호출하는 방법을 명시
- ConcreteStrategy: Strategy 인터페이스/추상 클래스의 구현체, 실제 알고리즘
- Context: Strategy를 이용하는 역할, 필요에 따라 ConcreteStrategy를 교체(집약 관계)

> 행위 패턴
>
> - 객체나 클래스 사이의 알고리즘이나 책임 분배에 관련된 패턴
> - 한 객체가 혼자 수행할 수 없는 작업을 여러 개의 객체로 어떻게 분배하는지, 또 그렇게 하면서도 객체 사이의 결합도를 최소화하는 것에 중점

> 집약 관계
>
> - 참조값을 인자로 받아 필드를 세팅하는 경우

## 예시

![예시](https://t1.daumcdn.net/cfile/tistory/99B3E8415D04F51C18)

- https://github.com/paki1019/SimUDuck/tree/main/src/main/java

> 출처
>
> - https://gmlwjd9405.github.io/2018/07/06/strategy-pattern.html
