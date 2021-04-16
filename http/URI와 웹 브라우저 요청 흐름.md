# URI

Uniform Resource Identifier 리소스를 식별하는 통합된 방법

## URI, URL, URN

`URI는 로케이터를(locator), 이름(name) 또는 둘 다 추가로 분류될 수 있다.`
![URI, URL, URN](../static/image/0CCF153E2FB5303AFDFC8053B3DEF4138E2287200D230ACD040E11574711FD0B.JPG)
![URL과 URN](../static/image/57EF84C01B76DC897E6A1D6B53165D9605C1778F1487A5676EC9F58863262186.JPG)
URN은 쓰기 힘들어 거의 URL만 사용함.

### URI

- Uniform: 리소스 식별하는 통일된 방식
- Resource: 자원, URI로 식별할 수 있는 모든 것(제한 없음)
- Identifier: 다른 항목과 구분하는데 필요한 정보

### URL, URN

- URL - Locator: 리소스가 있는 위치를 지정
- URN - Name: 리소스에 이름을 부여
- 위치는 변할 수 있지만, 이름은 변하지 않는다.
- urn:isbn:8960777331 (어떤 책의 isbn URN)
- URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음
- 앞으로는 URI를 URL과 같은 의미로 이야기

## URL 전체 문법

- scheme://[userinfo@]host[:port}[/path][?query][#fragment]
- https://www.google.com:443/search?q=hello&hl=ko

### scheme

- 주로 프로토콜 사용
- 프로토콜: 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙
  - http, https, ftp 등등
- http는 80 포트, https는 443 포트를 사용, 포트는 생략 가능
- https는 http에 보안 추가 (HTTP Secure)

### userinfo

- URL에 사용자정보를 포함해서 인증
- 거의 사용하지 않음

### host

- 호스트명
- 도메인명 또는 IP 주소를 직접 사용가능

### PORT

- 포트(PORT)
- 접속 포트
- 일반적으로 생략, 생략시 http는 80, https는 443

### path

- 리소스 경로(path), 계층적 구조

### query

- key=value 형태
- ?로 시작, &로 추가 가능 ? keyA=valueA&keyB=valueB
- query parameter, query string 등으로 불림, 웹서버에 제공하는 파라미터, 문자 형태

### fragment

- html 내부 북마크 등에 사용
- 서버에 전송하는 정보 아님

# 웹 브라우저 요청 흐름

1. 웹 브라우저에서 DNS 서버를 조회, IP와 PORT정보를 찾아서 HTTP 요청 메시지를 생성
   ![HTTP 요청 메시지 생성](../static/image/DF53395B3993D62D77FD65D7496206BCF28990FFFC4316032EA08607273C6A9C.JPG)
   ![HTTP 요청 메시지](../static/image/37CE95FD78B20B15B43A115E7270445E638F0726273C3EB6B2C351745FB67BD3.JPG)
2. HTTP 메시지를 포함한 TCP/IP 패킷을 생성하여 인터넷으로 전송
   ![HTTP 메시지 전송](../static/image/4EF89C37CC9751E0906D2D1E62CC0919F637804B371FE968ED6FC43F381AB5BB.JPG)
   ![패킷 생성](../static/image/82629313E2C21DDA1A431D845654871FC5EE3F6188EBAFBBDD54A391A29559C5.JPG)
3. 요청 패킷 도착시 HTTP 응답 패킷(메시지)을 전달
   ![HTTP 요청 패킷 도착](../static/image/B92D29169F7F62E1A9476BB3C5B0B3CE12C33C11525FED39AD6B702387A55D43.JPG)
   ![HTTP 응답 메시지](../static/image/7A31E2A61EF2408115BBF4DE12CB8704EF80015D671442FA8C3DD0A12478AAD7.JPG)
4. 브라우저는 HTTP 응답 메시지를 확인하여 웹 페이지를 랜더링함.
   ![응답 패킷 전달](../static/image/D6F480C5A250FA690BF763B8C0D6621BFA816243431CCFD02C0DB2F3B913DF54)
   ![웹 브라우저 HTML 랜더링](../static/image/F54231E1A5742AB0E6B6AD25E6E102940CF63EA14459A9317C52892773870034.JPG)
