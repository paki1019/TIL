# Srping Security

- Spring Security는 Spring 기반의 애플리케이션의 보안(인증과 권한, 인가 등)을 담당하는 스프링 하위 프레임워크
- Spring Security는 '인증'과 '권한'에 대한 부분을 Filter 흐름에 따라 처리함.
  > Filter는 Dispatcher Servlet으로 가기 전에 적용되므로 가장 먼저 URL 요청을 받음
  > Interceptor는 Dispatcher와 Controller사이에 위치
- Spring Security는 보안과 관련해서 체계적으로 많은 옵션을 제공해주기 때문에 개발자 입장에서는 일일이 보안관련 로직을 작성하지 않아도 된다는 장점이 있음
  ![Spring Security](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FSvk8p%2FbtqEIKlEbTZ%2FvXKzokudAYZT9kRGXNHJe1%2Fimg.png)

## 인증(Authorizatoin)과 인가(Authentication)

- 인증(Authentication): 해당 사용자가 본인이 맞는지를 확인하는 절차
- 인가(Authorization): 인증된 사용자가 요청한 자원에 접근 가능한지를 결정하는 절차

![인증(Authorizatoin)과 인가(Authentication)](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FoMnop%2FbtqEJh4jxCX%2FPhClhzrEpVOCRK7wYM5R4k%2Fimg.png)

- Spring Security는 기본적으로 인증 절차를 거친 후에 인가 절차를 진행, 인가 과정에서 해당 리소스에 대한 접근 권한이 있는지 확인.
- Spring Security에서는 이러한 인증과 인가를 위해 Principal을 아이디로, Credential을 비밀번호로 사용하는 Credential 기반의 인증 방식을 사용.
