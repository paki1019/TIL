# HTTPS

## HTTP

- Hypertext Transfer Protocol
- 서로 다른 시스템들 사이에서 통신을 주고받게 하는 가장 기본적인 프로토콜.
- 서버에서 브라우저로 데이터를 전송하는 용도로 많이 사용함.
- 전송되는 정보가 암호화되지 않기 때문에 데이터가 쉽게 도난당할 수 있음.

## HTTPS

- Hypertext Transfer Protocol Secure
- HTTP에 SSL(보안 소켓 계층)을 적용한 것.
- 서버와 브라우저 사이에 안전하게 암호화된 연결을 만들 수 있게 도와주고, 서버와 브라우저가 민감한 정보를 주고받을 때 해당 정보가 도난당하는 것을 막아줌.
- HTTP 자체를 암호화하는 것은 아니고, HTTP 바디를 암호화 시켜줌.

## HTTPS를 사용해야 하는 이유

### 보안성

![HTTP, HTTPS](https://seopressor.com/wp-content/uploads/2017/07/Difference-Between-HTTP-and-HTTPS.png)

데이터 전송을 해커가 가로챘을 때 HTTP는 데이터가 그대로 노출되지만 HTTPS는 데이터가 암호화 되어 있어 보안성이 확보됨.

### (SEO)검색 엔진 최적화

검색엔진은 HTTPS 웹 사이트에 가산점을 줘 더 잘 노출되도록 함.

## SSL

- Secure Socket Layer
- Netscape Communications Corporation에서 웹 서버와 웹 브라우저간의 보안을 위해 만든 프로토콜.
- 공개키/개인키 대칭키 기반으로 사용.
- TLS: SSL의 업그레이드 버전, 일반적으로 두 단어를 동일한 의미로 사용.

### 대칭키 암호화

- 대칭키(Symmetric Key) 암호화 방식은 암호화/복호화에 같은 암호 키(대칭키)를 사용하는 암호화 알고리즘.
- 공개키 암호화에 비해 암호화 및 복호화가 빠름.
- 암호화 통신을 하는 사용자끼리 같은 대칭키를 공유해야만 한다는 단점 존재.

![대칭키 암호화](https://t1.daumcdn.net/cfile/tistory/2168D83F587B369B26)

### 공개키 암호화

- 공개키(Public Key) 암호화 방식은 암호화와 복호화에 사용하는 암호키를 분리한 방식.
- 자신이 가지고 있는 고유한 암호키(비밀키, Private Key)로만 복호화할 수 있는 암호키(공개키, Public Key)를 대중에 공개.
- 대칭키 암호화의 단점을 해결.
- 암호화하는 키와 복호화하는 키가 서로 다르기 때문에 암복호화가 매우 복잡하다는 단점 존재.

![공개키 암호화](https://t1.daumcdn.net/cfile/tistory/242B243B587B395323)

### SSL 통신 과정

1. 사이트(특히 전자상거래 기관)은 인증기관에 자신의 정보와 공개키를 제출.
2. 인증기관은 정보를 면밀히 검토한뒤, 사이트의 정보와 공개키를 자신의 비밀키로 암호화.
3. 인증기관은 인증기관의 비밀키로 암호화된 사이트의 정보와 공개키를 사이트에 송신.
4. 개인이 브라우저를 통해 사이트에 접속하면, 암호화된 사이트의 정보와 공개키를 사이트로 부터 받음.
5. 브라우저는 인증기관의 공개키(이 공개키는 브라우저에게만 제공된다.)로 이를 복호화하여 사이트의 공개키를 얻음.
6. 브라우저가 대칭키를 사이트의 공개키로 암호화하여 사이트에 보냄.
7. 사이트는 자신의 개인키로 암호화된 대칭키를 복호화.
8. 개인과 사이트는 대칭키로 통신 가능.

![SSL의 암호화](https://user-images.githubusercontent.com/12606910/145701685-b20e600e-7b7b-4eff-80de-f4e69e33df46.png)

> 참조
>
> - https://www.youtube.com/watch?v=wPdH7lJ8jf0
> - https://preamtree.tistory.com/38