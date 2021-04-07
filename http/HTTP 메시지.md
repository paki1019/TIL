# HTTP 메시지

HTTP 메시지는 서버와 클라이언트 간에 데이터가 교환되는 방식

- 요청(request)은 클라이언트가 서버로 전달해서 서버의 액션이 일어나게끔 하는 메시지
- 응답(response)은 요청에 대한 서버의 답변

HTTP 메시지는 ASCII로 인코딩된 텍스트 정보이며 여러 줄로 구성됨.
HTTP 요청과 응답의 구조는 서로 닮았으며, 그 구조는 다음과 같음

1. 시작 줄(start-line)에는 실행되어야 할 요청, 또은 요청 수행에 대한 성공 또는 실패가 기록되어 있음
2. 옵션으로 HTTP 헤더 세트가 들어갑니다. 여기에는 요청에 대한 설명, 혹은 메시지 본문에 대한 설명이 들어감
3. 요청에 대한 모든 메타 정보가 전송되었음을 알리는 빈 줄(blank line)이 삽입됨
4. 요청과 관련된 내용(HTML 폼 콘텐츠 등)이 옵션으로 들어가거나, 응답과 관련된 문서(document)가 들어감. 본문의 존재 유무 및 크기는 첫 줄과 HTTP 헤더에 명시됨.
   ![이미지](https://mdn.mozillademos.org/files/13827/HTTPMsgStructure2.png)

# HTTP 요청

## 시작 줄

1. HTTP 메서드로, 영어 동사(GET, PUT, POST) 혹은 명사(HEAD, OPTIONS)를 사용해 서버가 수행해야 할 동작을 나타냄. GET은 리소스를 클라이언트로 가져다 달라는 것을 뜻하며, POST는 데이터가 서버로 들어가야 함을 의미
2. 두번째로 오는 요청 타겟은 주로 URL, 또는 프로토콜, 포트, 도메인의 절대 경로로 나타낼 수도 있으며 이들은 요청 컨텍스트에 의해 특정지어 짐. 포맷에는 다음과 같은 것들이 있음.
   - origin 형식: 끝에 '?'와 쿼리 문자열이 붙는 절대 경로, GET, POST, HEAD, OPTIONS 메서드와 함께 사용됨
     ```
     POST / HTTP 1.1
     GET /background.png HTTP/1.0
     HEAD /test.html?query=alibaba HTTP/1.1
     OPTIONS /anypage.html HTTP/1.0
     ```
   - absolute 형식: 완전한 URL 형식. 프록시에 연결하는 경우 대부분 GET과 함께 사용됩니다.
     ```
     GET http://developer.mozilla.org/en-US/docs/Web/HTTP/Messages HTTP/1.1
     ```
   - authority 형식: 도메인 이름 및 옵션 포트(':'가 앞에 붙음)로 이루어진 URL의 authority component 입니다.​​​​ HTTP 터널을 구축하는 경우에만 CONNECT와 함께 사용할 수 있음
     ```
     CONNECT developer.mozilla.org:80 HTTP/1.1
     ```
3. 마지막으로 ​​​​HTTP 버전이 들어감. 메시지의 남은 구조를 결정하기 때문에, 응답 메시지에서 써야 할 HTTP 버전을 알려주는 역할을 합

## 헤더

다양한 종류의 요청 헤더가 있는데, 이들은 다음과 같이 몇몇 그룹으로 나눌 수 있음

- General 헤더: Via와 같은 헤더는 메시지 전체에 적용됨
- Request 헤더: User-Agent (en-US), Accept-Type와 같은 헤더는 요청의 내용을 좀 더 구체화 시키고(Accept-Language), 컨텍스를 제공하기도 하며(Referer), 조건에 따른 제약 사항을 가하기도 하면서(If-None) 요청 내용을 수정함
- Entity 헤더: Content-Length와 같은 헤더는 요청 본문에 적용됨. 당연히 요청 내에 본문이 없는 경우 entity 헤더는 전송되지 않음
  ![이미지](https://mdn.mozillademos.org/files/13821/HTTP_Request_Headers2.png)

## 본문

모든 요청에 본문이 들어가지는 않습니다. GET, HEAD, DELETE , OPTIONS처럼 리소스를 가져오는 요청은 보통 본문이 필요가 없음. 일부 요청은 업데이트를 하기 위해 서버에 데이터를 전송함.(보통 POST)

넓게 보면 본문은 두가지 종류

- 단일-리소스 본문(single-resource bodies): 헤더 두 개(Content-Type와 Content-Length)로 정의된 단일 파일
- 다중-리소스 본문(multiple-resource bodies): 멀티파트 본문으로 구성되는 다중 리소스 본문에서는 파트마다 다른 정보를 지니게 됨. 보통 HTML 폼과 관련이 있음

# HTTP 응답

## 상태 줄

HTTP 응답의 시작 줄은 상태 줄(status line)이라고 불리며, 다음과 같은 정보를 가지고 있음

1. 프로토콜 버전: 보통 HTTP/1.1
2. 상태 코드: 요청의 성공 여부를 나타냅니다. 200, 404 혹은 302
3. 상태 텍스트: 짧고 간결하게 상태 코드에 대한 설명을 글로 나타내어 HTTP 메시지를 이해할 때 도움

```
HTTP/1.1 404 Not Found.
```

## 헤더

응답에 들어가는 HTTP 헤더는 다른 헤더와 동일한 구조를 따릅니다. 대소문자를 구분하지 않는 문자열 다음에 콜론(':')이 오며, 그 뒤에 오는 값은 구조가 헤더에 따라 달라짐

- General 헤더: Via와 같은 헤더는 메시지 전체에 적용됨
- Response 헤더: Vary와 Accept-Ranges와 같은 헤더는 상태 줄에 미처 들어가지 못했던 서버에 대한 추가 정보를 제공
- Entity 헤더: Content-Length와 같은 헤더는 요청 본문에 적용됨. 당연히 요청 내에 본문이 없는 경우 entity 헤더는 전송되지 않음
  ![이미지](https://mdn.mozillademos.org/files/13823/HTTP_Response_Headers2.png)

## 본문

본문은 응답의 마지막 부분에 들어감. 모든 응답에 본문이 들어가지는 않음. 201, 204과 같은 상태 코드를 가진 응답에는 보통 본문이 없음

넓게 보면 본문은 세가지 종류로 나뉨

- 이미 길이가 알려진 단일 파일로 구성된 단일-리소스 본문: 헤더 두개(Content-Type와 Content-Length)로 정의함
- 길이를 모르는 단일 파일로 구성된 단일-리소스 본문: Transfer-Encoding가 chunked로 설정되어 있으며, 파일은 청크로 나뉘어 인코딩 되어 있음
- 서로 다른 정보를 담고 있는 멀티파트로 이루어진 다중 리소스 본문: 이 경우는 상대적으로 위의 두 경우에 비해 보기 힘듬
