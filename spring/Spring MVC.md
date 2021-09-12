# Spring MVC

- Spring 프레임워크에서 제공하는 웹 모듈
- MVC 는 Model-View-Controller 의 약자로, 기본 시스템 모듈을 MVC 로 나누어 구현되어 있음.
  - Model은 데이터 모델
  - View는 렌더링되어 보이는 페이지
  - Controller는 사용자의 요청을 받고, 응답을 주는 로직
    ![Spring MVC](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdJDooL%2FbtqBpP4NxVG%2Fi9C3OlKdgILgixFKny52EK%2Fimg.png)

## 기본 동작 흐름

![기본 동작 흐름](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcAkzFN%2FbtqBp4AIlD3%2FmE8PbHZQh0WtvB0wqULb3k%2Fimg.png)

> 요청 -> 프론트 컨트롤러 -> 핸들러 매핑 -> 핸들러 어댑터 -> 컨트롤러 -> 로직 수행(서비스) -> 컨트롤러 -> 뷰 리졸버 -> 응답(jsp, html)

- 맨 앞단에서 유저의 유청을 받는 컨트롤러를 프론트 컨트롤러라고 함.
  - DispatcherServlet 객체가 이 역할을 함.
  - 본격적으로 로직에 들어오기 전에, 요청에 대한 선처리 작업을 수행
- 프론트 컨트롤러는 요청을 Handler Mapping을 통해 해당 요청을 어떤 핸들러가 처리해야하는지를 매핑
- 매핑된 핸들러를 Handler Adapter가 실제로 처리(Controller 전달)
- Controller, Service, Repositry에서 로직 처리 후, 결과를 View Resolver를 거쳐 뷰 파일을 렌더링하여 내보냄.

## Layerd 아키텍처

![Layerd 아키텍처](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcD6DU2%2FbtqBrA6ikOt%2FLMxy9IPLtBicl9ZOKAD691%2Fimg.png)

- Presentation Layer
  - 화면 조작 또는 사용자 입력을 처리하는 레이어
- Service Layer (Domain)

  - 비즈니스와 관련된 도메인 로직을 처리하는 레이어
  - 하나의 비즈니스 로직은 하나의 트랜잭션 단위로 동작.

- Repository Layer (Data source)
  - 도메인에서 필요로 하는 데이터를 조작하기 위한 레이어
