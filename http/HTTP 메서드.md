# HTTP 메서드

## HTTP 메서드 종류

- GET: 리소스 조회
- POST: 요청 데이터 처리, 주로 등록에 사용
- PUT: 리소스를 대체, 해당 리소스가 없으면 생성
- PATCH: 리소스 부분 변경
- DELETE: 리소스 삭제

### 기타 메서드

- HEAD: GET과 동일하지만 메시지 부분을 제외하고, 상태 줄과 헤더만 반환
- OPTIONS: 대상 리소스에 대한 통신 가능 옵션(메서드)을 설명해줌(주로 CORS에서 사용)
- TRACE:
- CONNECT:

## GET

```
GET /search?q=hello&hl-ko
Host: www.google.com
```

- 리소스 조회
- 서버에 전달하고 싶은 데이터는 query(쿼리 파라미터, 쿼리 스트링)을 통해 전달
- 메시지 바디를 사용해서 데이터를 전달할 수 있지만, 지원하지 않는 곳이 많아서 비권장.

## POST

```
POST /members HTTP/1.1
Content-Type: application/json

{
    "username": "hello"
    "age": 20
}
```

- 요청 데이터 처리
- 메시지 바디를 통해 서버로 요청 데이터 전달
- 서버는 요청 데이터를 처리
  - 메시지 바디를 통해 들어온 데이터를 처리하는 모든 기능을 수행.
- 주로 전달된 데이터로 신규 리소스 등록, 프로세스 처리에 사용
- 어떤 리소스인가에 따라 요청 데이터를 처리하는 방법이 다름 -> 처리의 의미
  1. 새 리소스 생성(등록)
     - 서버가 아직 식별하지 않은 새 리소스 생성
  2. 요청 데이터 처리
     - 단순히 데이터를 생성하거나, 변경하는것을 넘어서 프로세스를 처리하는 경우.
     - 주문 -> 결제완료 -> 배달시작 -> 배달완료 와 같이 프로세스 상태 변경
     - POST의 결과로 새로운 리소스가 생성되지 않을 수도 있음.
  3. 다른 메서드로 처리하기 애매한 경우
     - JSON으로 조회 데이터를 넘겨야하는데, GET 메서드를 사용하기 어려운 경우

## PUT

```
PUT /members/100 HTTP/1.1
Content-Type: application/json

{
    "username": "hello",
    "age": 20
}
```

- 리소스를 대체
  - 리소스가 있으면 대체
  - 리소스가 없으면 생성
  - 덮어씌우기
- 클라이언트가 리소스를 식별한다.
  - 클라이언트가 리소스 위치를 알고 URI 지정
  - POST와의 가장 큰 구분점

## PATCH

```
PATCH /members/100 HTTP/1.1
Content-Type: application/json

{
    "age": 50
}
```

- 리소스 부분 변경

## DELETE

```
DELETE /members/100 HTTP/1.1
Host: localhost:8080
```

- 리소스 제거

# HTTP 메서드의 속성

- 안전(Safe Methods)
- 멱등(idempotent Methods)
- 캐시가능(Cacheable Methods)

## 안전(Safe)

- 호출해도 리소스를 변경하지 않음.
- GET, HEAD, OPTIONS, TRACE
- 호출해서 로그가 쌓인다.. 이런 경우는 고려 X, 해당 리소스만 고려.

## 멱등(Idempotent)

- f(f(x)) = f(x)
- 한 번 호출하든 두 번 호출하든 100번 호출하든 결과가 동일
- 멱등 메서드

  - GET: Safe한 메서드는 멱등.
  - PUT: 결과를 대체함. 여러번 호출하더라도 최종 결과는 동일.
  - DELETE: 결과를 삭제. 같은 요청을 여러번 하더라도 삭제된 결과는 동일.
  - POST: 멱등하지 않음. 여러번 처리하면 다른 결과가 나타날 수 있음.(ex: 결제)

- 활용:

  - 자동 복구 메커니즘
  - 서버가 TIMEOUT 등으로 정상 응답을 못 주엇을 때, 클라이언트가 같은 요청을 다시 해도 괜찮은가?

- 멱등은 외부 요인으로 중간 리소스가 변경되는 경우는 고려X

## 캐시가능(Cacheable)

- 응답 결과 리소스를 캐시해서 사용해도 되는가?
- GET, HEAD, POST, PATCH 캐시가능
- 실제로는 GET, HEAD 정도만 캐시로 사용
  - POST, PATCH는 본문 내용까지 캐시 키로 고려해야하는데, 구현이 어려움.
