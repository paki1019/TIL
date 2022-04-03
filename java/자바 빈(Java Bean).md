# 자바 빈(Java Bean)

- 자바빈즈(Java Beans)는 자바(Java)로 작성된 소프트웨어 컴포넌트를 일컫는 말로 데이터 표현을 목적으로하는 자바 클래스.
- 자바 빈즈 클래스는 'Java Beans Convention'을 지켜야 함.
  - 클래스는 인자(Argument)가 없는 기본 생성자(Default constructor)를 갖는다
  - 클래스의 멤버 변수는 프로퍼티(Properties)라고 하며 private 접근 제한자를 가져야 한다.
  - 클래스의 프로퍼티들은 Getter/Setter를 통해 접근할 수 있어야 한다
    - Getter의 이름은 get{프로퍼티 이름} 이며, Setter의 이름은 set{프로퍼티 이름}이다
    - Getter/setter의 접근 제한자는 public이어야 한다.
    - 프로퍼티의 타입이 Boolean인 경우 is로 시작할 수 있다
    - Getter의 경우 파라미터가 존재하지않아야 하며, setter의 경우 하나 이상의 파라미터가 존재한다
    - Read Only인 경우 Setter는 없을 수 있다.
  - Serializable 인터페이스를 구현한다.
  - 자바빈 클래스는 패키징 되어야 한다. (Package)
