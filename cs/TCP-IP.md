# TCP/IP

- 인터넷에서 컴퓨터들이 서로 정보를 주고 받는데 쓰이는 프로토콜의 집합.
- TCP/IP는 패킷 통신 방식의 인터넷 프로토콜인 IP와 전송 조절 프로토콜인 TCP로 구성됨.
- IP는 패킷 전달 여부를 보증하지 않고, 패킷을 보낸 순서와 받는 순서가 다를 수 있음.
- TCP는 이를 보완해 데이터의 전달을 보증하고, 순서대로 받게 해줌.

## TCP/IP의 계층

![TCP/IP의 계층](https://lh3.googleusercontent.com/proxy/KsYTiggWW9sMlg8aUly1nJXGhE77YZjJepd0DPZ_MExV2gaGj3vAKFos_Wa08AAOxaZ7zU8Ls_UnKaxsvWfKb6gxjUw0unXB_vgN1db-mCeTUA)

- OSI 7 Layers와 비교했을때 이론보다 실용성에 중점을 둠.

### Application Layer

- 특정 서비스를 제공하기 위해 애플리케이션 끼리 정보를 주고 받을 수 있음.
- FTP, HTTP, SSH, Telnet, DNS, SMTP 등

### Transport Layer

- 송신된 데이터를 수신측 애플리케이션에게 확실히 전달하게 함.
- 포트번호를 식별해서 애플리케이션을 찾아주는 역할을 함.
- TCP, UDP, RTP, RTCP 등.

### Internet Layer

- 수신 측까지 데이터를 전달하기 위해 사용.
- IP 주소를 바탕으로 올바른 목적지로 데이터를 전달.
- IP, ARP, ICMP, RARP, OSPF 등.

### Network Access Layer

- 네트워크에 직접 연결된 기기 간 전송을 할 수 있도록 함.
- 물리적 주소인 MAC 주소를 사용.
- Ethernet, PPP, Token Ring 등.

## TCP/IP 흐름

### 3-Way-Handshaking

- TCP에서 통신을 하는 장치간 서로 연결이 잘 되어있는지 확인하는 과정.
- 양쪽 모두 데이터를 전송할 준비가 되었다는 것을 보장하고, 실제로 데이터 전달이 시작하기전에 한쪽이 다른 쪽이 준비되었다는 것을 알수 있도록 함.

![3-Way-Handshaking](https://t1.daumcdn.net/cfile/tistory/225A964D52F1BB6917)

1. 클라이언트에서 서버에 접속을 요청하는 SYN 패킷을 보냄. 클라이언트는 SYN 을 보내고 SYN/ACK 응답을 기다리는SYN_SENT 상태가 됨.
2. 서버는 SYN요청을 받고 클라이언트에게 요청을 수락한다는 ACK 와 SYN flag 가 설정된 패킷을 발송, 클라이언트가 다시 ACK으로 응답하기를 대기. 이때 서버는 SYN_RECEIVED 상태가 됨.
3. 클라이언트는 서버에게 ACK을 보내고 이후로부터는 연결이 이루어지고 데이터가 오감. 이때의 B서버 상태가 ESTABLISHED

### 4-Way-Handshaking

- 3-Way-Handshaking이 TCP의 연결을 초기화 할 때 사용한다면, 4-Way-Handshaking은 세션을 종료하기 위해 수행되는 과정.

![4-Way-Handshaking](https://t1.daumcdn.net/cfile/tistory/2152353F52F1C02835)

1. 클라이언트가 연결을 종료하겠다는 FIN플래그를 전송.
2. 서버는 일단 확인메시지 ACK 플래그를 보내고 자신의 통신이 끝날때까지 기다림. 이 상태가 CLOSE_WAIT상태
3. 서버가 통신이 끝났으면 연결이 종료되었다고 클라이언트에게 FIN플래그를 전송.
4. 클라이언트는 확인했다는 ACK 플래그를 보냄.

> 만약 Server에서 FIN을 전송하기 전에 전송한 패킷이 Routing 지연이나 패킷 유실로 인한 재전송 등으로 인해 FIN패킷보다 늦게 도착하는 상황이 발생하면?
> Client는 Server로부터 FIN을 수신하더라도 일정시간(디폴트 240초) 동안 세션을 남겨놓고 잉여 패킷을 기다리는 과정을 거침. 이를 TIME_WAIT라고 함.

## 신뢰 할 수 있는 TCP

- 요즘 세상에 인터넷 사용간 엄청나게 큰 데이터를 주고 받게 되는데, 이 데이터를 여러개의 패킷으로 쪼개서 보내곤 함.
- 이러한 패킷들은 엄청 복잡한 인터넷을 통해서 전송됨.
- 이러한 복잡한 환경에서 유실되지 않고, 올바른 순서로 도착하도록 하는 것이 TCP
- TCP는 흐름제어, 오류제어, 혼잡제어를 통해 신뢰성있는 데이터 전송을 보장함.

> 출처
>
> - http://www.incodom.kr/Internet_Protocol_Suite
> - https://www.youtube.com/watch?v=BEK354TRgZ8
