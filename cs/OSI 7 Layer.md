# OSI 7 Layer

![OSI 7 Layer](https://t1.daumcdn.net/cfile/tistory/995EFF355B74179035)

- OSI 7 계층은 네트워크 통신이 일어나는 과정을 7 단계로 나눈 것.
- 계층을 나눈 이유는 통신이 일어나는 과정을 단계별로 파악할 수 있기 때문
- 7단계 중 특정한 곳에 이상이 생기면 다른 단계의 장비 및 소프트웨어를 건드리지 않고도 이상이 생긴 단계만 고칠 수 있음.

## 1계층 - 물리계층(Physical Layer)

- 전기적, 기계적, 기능적인 특성을 이용해서 통신 케이블로 데이터를 전송하는 계층
- 사용되는 통신 단위는 비트이며 1과 0으로 나타내지는 ON/OFF 상태만을 나타냄.
- 단지 데이터를 전달만 할뿐 전송하려는(또는 받으려는)데이터가 무엇인지, 어떤 에러가 있는지 등에는 전혀 신경 쓰지 않음.
- 대표적인 장비는 통신 케이블, 리피터, 허브등이 있음.
  ![물리계층](https://t1.daumcdn.net/cfile/tistory/990DA13D5B73B8C615)
  ![물리계층](https://t1.daumcdn.net/cfile/tistory/9927F73D5B73B8C735)

## 2계층 - 데이터 링크 계층(DataLink Layer)

- 물리계층을 통해 송수신되는 정보의 오류와 흐름을 관리하여 안전한 정보의 전달을 수행할 수 있도록 도와주는 역할을 함.
- 통신에서의 오류도 찾아주고 재전송도 하는 기능을 가짐.
- 맥 주소를 가지고 통신
- 이 계층에서 전송되는 단위를 프레임이라고 함.
- 대표적인 장비로는 브리지, 스위치등이 있음.
  ![데이터 링크 계층](https://t1.daumcdn.net/cfile/tistory/9931734D5B73BA3D2F)
- 브리지나 스위치를 통해 맥 주소를 기준으로 물리계층에서 받은 정보를 전달함.
- 포인트 투 포인트(Point to Point) 간 신뢰성있는 전송을 보장하기 위한 계층으로 네트워크 위의 개체들 간 데이터를 전달하고, 물리 계층에서 발생할 수 있는 오류를 찾아 내고, 수정하는 데 필요한 기능적, 절차적 수단을 제공한다.

## 3 계층 - 네트워크 계층(Network Layer)

- 데이터를 목적지까지 가장 안전하고 빠르게 전달하는 기능(라우팅) 제공.
- 경로를 선택하고 주소를 정하고 경로에 따라 패킷을 전달.
- 대표적인 장비는 라우터 이며, 요즘은 2계층의 장비 중 스위치라는 장비에 라우팅 기능을 장착한 Layer 3 스위치도 존재.
- 데이터를 연결된 다른 네트워크를 통해 전달함으로써 인터넷이 가능하게 만드는 계층.
- 논리적인 주소 구조(IP), 곧 네트워크 관리자가 직접 주소를 할당하는 구조를 가지며, 계층적(hierarchical).

### IP

- 네트워크 계층에서의 주소를 의미, IP를 기준으로 패킷을 전달하고 라우팅함.
- 하위계층인 데이터링크 계층의 하드웨어적인 특성과는 독립적으로 역할

## 4 계층 - 전송 계층(Transper Layer)

- 통신을 활성화하기 위한 계층. 보통 TCP프로토콜을 사용하며, 포트를 열어서 응용 프로그램이 데이터 전송을 가능하게 함.

### TCP 프로토콜(Transmission Control Protocol)

- 양종단 호스트 내 프로세스 상호 간에 신뢰적인 연결지향성 서비스를 제공
- 패킷 손실, 중복, 순서바뀜 등이 없도록 보장(IP 계층의 단점 보완)
- 연결지향적 (Connection-oriented)

### UDP 프로토콜(User Datagram Protocol)

- 신뢰성이 낮은 프로토콜로써 완전성을 보증하지 않으나, 가상회선을 굳이 확립할 필요가 없고 유연하며 효율적 응용의 데이타 전송에 사용
- 비연결성이고, 신뢰성이 없으며, 순서화되지 않은 Datagram 서비스 제공
- 실시간 응용 및 멀티캐스팅 가능
- 헤더가 단순함

## 5계층 - 세션 계층(Session Layer)

- 데이터가 통신하기 위한 논리적인 연결을 의미
  - 4계층에서도 연결을 맺고 종료할 수 있기 때문에 어느 계층에서 통신이 끊어졌는지 명확하게 확인하는데는 어려움이 있음.
  - 세션 계층은 4 계층과 무관하게 응용 프로그램 관점에서 봐야함.
- 양 끝단의 응용 프로세스가 통신을 관리하기 위한 방법을 제공함.
- 동시 송수신 방식(duplex), 반이중 방식(half-duplex), 전이중 방식(Full Duplex)의 통신이 존재.
- 이 계층은 TCP/IP 세션을 만들고 없애는 책임을 맡음.
- 보통 운영체제에서 제공.

## 6계층 - 표현 계층(Presentation Layer)

- 데이터 표현이 상이한 응용 프로세스의 독립성을 제공하고, 암호화 제공
- 코드 간의 번역을 담당하여 사용자 시스템에서 데이터의 형식상 차이를 다루는 부담을 응용 계층으로부터 덜어 줌.
- 예시로 EBCDIC로 인코딩된 문서 파일을 ASCII로 인코딩된 파일로 바꿔 주는 것
- 해당 데이터가 TEXT인지, 그림인지, GIF인지 JPG인지의 구분 등이 표현 계층의 몫

## 7계층 - 응용 계층(Application Layer)

- 응용 프로세스와 직접 관계하여 일반적인 응용 서비스를 수행함.
- HTTP, FTP, SMTP, POP3, IMAP, Telnet 등과 같은 프로토콜이 존재.

### 번외: 로드밸런싱과 L4/L7 스위치

- 로드밸런싱은 여러개의 서버 or 프로세스를 분산운용하는 것
- L4/L7 스위치의 숫자는 OSI 7 Layer의 Layer 단계를 의미
  - 즉 L4는 전송 계층 L7은 응용 계층을 기준으로 로드밸런싱
  - L4 로드밸런싱은 TCP가 아예 죽어야 장애를 인식.
  - L7 로드밸런싱은 응용프로세스(일반적으로 apache tomcat)에서의 응답이 죽어야 장애로 인식됨.
