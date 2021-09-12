# Filter, Interceptor, AOP 차이

- 스프링 웹 개발을 하면서 공통적으로 처리해야할 로직이 생기기 마련인데, 공통 처리를 위해 활용할 수 있는 것 3가지가 바로 Filter, Interceptor, AOP

## Filter, Interceptor, AOP의 흐름

![Filter, Interceptor, AOP의 흐름](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F9983FB455BB4E5D30C7E10)

- Interceptor와 Filter는 Servlet 단위에서 실행됨, 반면 AOP는 메소드 앞에 Proxy패턴의 형태로 실행.
- 실행순서를 보면 Filter가 가장 밖에 있고 그안에 Interceptor, 그안에 AOP가 있는 형태, 요청이 들어오면 Filter → Interceptor → AOP → Interceptor → Filter 순으로 거치게 됨.

## Filter

- 요청과 응답을 거른뒤 정제하는 역할
- J2EE 표준 스펙으로 Spring 이전에 존재(웹 컨테이너로 관리)
- 서블릿 필터는 DispatcherServlet 이전에 실행이 되는데 필터가 동작하도록 지정된 자원의 앞단에서 요청내용을 변경하거나, 여러가지 체크를 수행할 수 있음.
- 또한 자원의 처리가 끝난 후 응답내용에 대해서도 변경하는 처리를 할 수가 있음.
- 보통 web.xml에 등록하고, 인코딩 처리, XSS 방어 등으로 사용

### 필터의 실행 메서드

- init(): 필터 인스턴스 초기화
- doFilter(): 전/후 처리
- destroy(): 필터 인스턴스 종료

## Interceptor

- 요청에 대한 작업 전/후를 가로챔
- Spring이 제공하는 기술(스프링 컨테이너로 관리)
- 필터는 스프링 컨텍스트 외부에 존재하여 스프링과 무관한 자원에 대해 동작, 하지만 인터셉터는 스프링의 DistpatcherServlet이 컨트롤러를 호출하기 전, 후로 끼어들기 때문에 스프링 컨텍스트(Context, 영역) 내부에서 Controller(Handler)에 관한 요청과 응답에 대해 처리.
- 스프링의 모든 빈 객체에 접근 가능
- 로그인 체크, 권한 체크, 프로그램 실행시간 계산 등으로 사용

### 인터셉터의 실행메서드

- preHandler(): 컨트롤러 메서드가 실행되기 전
- postHandler(): 컨트롤러 메서드 실행 후, View페이지 랜더링 이전
- afterCompletion(): view 페이지가 렌더링 된 후

## AOP

- OOP를 보완하기 위해 나온 개념으로, 중복되는 부분을 줄이기 위한 관점 지향 프로그래밍
- 주로 로깅, 트랜젝션, 에러처리 등 비즈니스단의 메서드를 조금 더 세밀하게 조정하기 위해 사용
- Interceptor와 Filter는 주소로 대상을 구분하지만, AOP는 주소, 파라미터, 애노테이션 등 다양한 방법으로 대상 지정 가능

### AOP의 포인트컷

- @Before: 대상 메서드의 수행 전
- @After: 대상 메서드의 수행 후
- @After-returning: 대상 메서드의 정상적인 수행 후
- @After-throwing: 예외발생 후
- @Around: 대상 메서드의 수행 전/후

> 출처
>
> - https://goddaehee.tistory.com/154
