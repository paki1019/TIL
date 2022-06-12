# WebSocket

WebSocket은 웹 페이지의 한계에서 벗어나 실시간으로 상호작용하는 웹 서비스를 만드는 표준 기술.

## 웹 소켓(Web Socket)이 있기까지

전형적인 브라우저 렌더링 방식은 HTTP 요청(HTTP Request)에 대한 HTTP 응답(HTTP Response)을 받아서 브라우저의 화면을 깨끗하게 지우고 받은 내용을 새로 표시하는 방식. 내용을 지우고 다시 그리면 브라우저의 깜빡임이 생기게 됨. 이러한 깜빡임 없이 원하는 부분만 다시 그리며 실시간으로 사용자와 상호작용하는 방식이 나타나고 사용자와 상호작용하는 웹 서비스를 선호하는 사용자가 증가하면서 RIA(Rich Internet Application) 기술의 발달이 촉진 됨.

상호작용하는 웹 서비스를 위해 숨겨진 프레임(Hidden Frame)을 이용한 방법이나 Long Polling, Stream 등 다양한 방법을 사용했음. 그러나 이러한 방식은 브라우저가 HTTP 요청를 보내고 웹 서버가 이 요청에 대한 HTTP 응답를 보내는 단방향 메세지 교환 '규칙'을 변경하지 않고 구현한 방식으로 상호작용하는 웹 페이지를 복잡하고 어려운 코드로 구현해야 했음.

보다 쉽게 상호작용하는 웹 페이지를 만들려면 브라우저와 웹 서버 사이에 더 자유로운 양방향 메시지 송수신(bi-directional full-duplex communication)이 필요하다. 그래서 HTML5 표준안의 일부로 WebSocket API(이후 WebSocket)가 등장.

![WebSocket과 일반적인 Ajax Long Polling 방식 비교](https://d2.naver.com/content/images/2015/06/helloworld-1336-1-1.png)

WebSocket은 그 이름에서 알 수 있듯이 소켓을 이용하여 자유롭게 데이터를 주고 받을 수 있음. 기존의 요청-응답 관계 방식보다 더 쉽게 데이터를 교환할 수 있음.

## WebSocket 프로토콜

표준 WebSocket의 API는 W3C에서 관장하고, 프로토콜은 IETF(Internet Engineering Task Force)에서 관장. WebSocket은 다른 HTTP 요청과 마찬가지로 80번 포트를 통해 웹 서버에 연결. HTTP 프로토콜의 버전은 1.1이지만 아래 예에서 볼 수 있듯이, Upgrade 헤더를 사용하여 웹 서버에 요청. 클라이언트인 브라우저와 마찬가지로 웹 서버도 WebSocket 기능을 지원해야함.

```
GET /... HTTP/1.1
Upgrade: WebSocket
Connection: Upgrade
```

브라우저는 "Upgrade: WebSocket" 헤더 등과 함께 랜덤하게 생성한 키를 서버에 보낸다. 웹 서버는 이 키를 바탕으로 토큰을 생성한 후 브라우저에 돌려줌. 이런 과정으로 WebSocket 핸드쉐이킹이 이루어짐.

그 뒤 Protocol Overhead 방식으로 웹 서버와 브라우저가 데이터를 주고 받음. Protocol Overhead 방식은 여러 TCP 커넥션을 생성하지 않고 하나의 80번 포트 TCP 커넥션을 이용하고, 별도의 헤더 등으로 논리적인 데이터 흐름 단위를 이용하여 여러 개의 커넥션을 맺는 효과를 내는 방식.

이런 방식을 사용하기 때문에 방화벽이 있는 환경에서도 무리 없이 WebSocket을 사용할 수 있음.

## WebSocket API 예제

```
if ('WebSocket' in window) {
    var oSocket = new WebSocket(“ws://localhost:80”);

    oSocket.onmessage = function (e) {
        console.log(e.data);
    };

    oSocket.onopen = function (e) {
        console.log(“open”);
    };

    oSocket.onclose = function (e) {
        console.log(“close”);
    };

    oSocket.send(“message”);
    oSocket.close();
}
```

WebSocket 프로토콜을 나타내는 ws://는 URI 스키마(Scheme)를 사용함. 암호화 소켓은 https:// 처럼 wss://를 사용.

위의 코드를 실행하면 먼저 new WebSocket() 메서드로 웹서버와 연결. 그리고 생성된 WebSocket 인스턴스를 이용하여 소켓에 연결할 때(onopen), 소켓 연결을 종료할 때(onclose), 메시지를 받았을 때(onmessage) 등의 이벤트를 각각 정의할 수 있음. 서버에 메시지를 보내고 싶을 때에는 send() 메서드를 이용.

> 출처
>
> - https://d2.naver.com/helloworld/1336
