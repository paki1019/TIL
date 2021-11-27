# Origin, SOP, CORS

## Origin(출처)

URL의 Protocol, Host, Port를 통해 같은 출처인지 다른 출처인지 판단 가능.(IE는 Port 구분 X)
![Origin](https://evan-moon.github.io/static/e25190005d12938c253cc72ca06777b1/21b4d/uri-structure.png)

## SOP (Same Origin Policy)

다른 출처의 리소스를 사용하는 것을 제한 하는 보안 방식, 같은 Origin 출처의 서버로만 요청을 주고 받을 수 있음.

### SOP 미적용 보안 공격 사례

1. 유저가 페이스북 서비스에 로그인 이후 사용. (페이스북으로 부터 인증 토근을 받음)
2. 해커가 등록해둔 링크 클릭(http://hacker.org)
3. 해당 링크 페이지 접속, 해당 페이지에서 공격 스크립트가 동작(facebook으로 의도치 않은 API 호출)
   - 앞서 1에서 받은 인증 토큰을 이용해 공격

- SOP가 적용되어있을 경우, hacker.org의 origin과 facebook.com의 origin이 달라 공격이 불가능(API 호출이 거부됨)

## CORS(교차 출처 리소스 공유)

브라우저에서 다른 출처의 리소스를 공유하는 방법.

Cross-Origin Resource Sharing의 약자로 HTTP 헤더를 사용해서, 한 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 알려주는 체제

cors가 적용되있을 경우 다른 origin에서도 API 호출이 가능

## CORS 접근 제어 시나리오

### 프리플라이트 요청 (Preflight Request)

![프리플라이트 요청](https://evan-moon.github.io/static/c86699252752391939dc68f8f9a860bf/21b4d/cors-preflight.png)

1. OPTIONS 메서드를 통해 다른 도메인의 리소스 요청이 가능한지 먼저 확인
2. 요청이 가능하면 실제 요청(Actual Request)를 보냄.

- Preflight Request 예시
  ```
  OPTIONS /resource/foo
  Access-Control-Request-Method: DELETE # 실제 요청 메서드
  Access-Control-Request-Headers: origin, x-requested-with # 실제 요청의 추가 헤더
  Origin: https://foo.bar.org # 요청 출처
  ```
- Preflight Response 예시

  ```
  HTTP/1.1 204 No Content
  Access-Control-Allow-Origin: https://foo.bar.org # 서버 측 허가 출처
  Access-Control-Allow-Methods: POST, GET, OPTIONS, DELETE # 서버 측 허가 메서드
  Access-Control-Allow-Headers: X-PINGOTHER, Content-Type, DELETE # 서버 측 허가 헤더
  Access-Control-Max-Age: 86400 # Preflight 응답 캐시 기간
  ```

- Preflight Response가 가져야 하는 특징
  - 응답 코드는 200대.
  - 응답 바디는 비어있어야 함.

### 단순 요청 (Simple Request)

![단순 요청](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/simple-req-updated.png)

- Preflight 요청 없이 바로 요청.
- GET, POST, HEAD 메서드만 가능.
- Content-Type이 아래와 같을 경우만 가능
  - application/x-www-form-urlencoded
  - multipart/form-data
  - text/plain
- 헤더가 Accept, Accept-Language, Content-Language, Content-Type 만 허용

### 사전요청(Preflight)가 필요한 이유

- cors는 브라우저에서 제공하는 보안 정책
- 만약 Preflight가 없을 경우 아래와 같이 서버에서 request가 처리가 되었지만, 브라우저의 cors에 걸려 client가 response를 받지 못하는 상황이 생길 수 있음.
  ![no Preflight](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2Fc8zxWZ%2FbtrgOulF1h9%2F2kw1reQOx4zJhDl7ogodAK%2Fimg.png)
- Preflight를 사용하는 경우 아래와 같이 브라우저가 본 요청을 날리기 전에 교차 출처 에러를 발생시킴
  ![Preflight](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2FU2BMh%2FbtrgXhq6z51%2FHS4xNjJLJm1agjfYYWGAg1%2Fimg.png)

### Credentialed Request

- 인증 관련 헤더를 포함할 때 사용하는 요청.
- 클라이언트측: `credentials: include`
- 서버측: `Access-Control-Allow-Credentials: true`
  - `Access-Control-Allow-Credentials: *`은 불가

> 출처

- https://developer.mozilla.org/ko/docs/Web/HTTP/CORS
- https://developer.mozilla.org/ko/docs/Glossary/Preflight_request
