# 로드밸런서(Load Balancer)

![로드밸런서](https://post-phinf.pstatic.net/MjAxOTEyMTBfMjE3/MDAxNTc1OTU0ODk1ODQ3.-GJxkoK7Apn4l0K5L1OXN4NFGsseRoaNhW2r0KIQJdog.0BchcWEI-WS-uEb3iRRrD0JyO_6eZoIWh7xf4f4J2fMg.JPEG/%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%84%9C_%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98.jpg?type=w1200)

- 서버에 가해지는 부하를 분산해주는 장치 또는 기술
- 클라이언트와 서버풀(Server Pool, 분산 네트워크를 구성하는 서버들의 그룹) 사이에 위치, 한 대의 서버로 부하가 집중되지 않도록 트래픽을 관리해 각각의 서버가 최적의 퍼포먼스를 보일 수 있게 함.

## Scale-up Scale-out

![Scale-up Scale-out](https://post-phinf.pstatic.net/MjAxOTEyMTBfMjk1/MDAxNTc1OTU1MDI2NTY4.Zxj8nWGb6G6jtHDAZPPDf-dPZnpb_hsd7ydWw5lW7vAg.AucOXPJnmLyGiHr8KpVD9Dsy59FsWv5p7qJnSyW_YFAg.JPEG/%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%8B%B1_%EC%8A%A4%EC%BC%80%EC%9D%BC.jpg?type=w1200)

- Scale-up은 서버 자체의 성능을 확장하는 것.
- Scale-ou은 기존 서버와 동일하거나 낮은 성능의 서버를 두 대 이상 증설하여 운영하는 것.
- Scale-out의 방식으로 서버를 증설하는 경우 여러 대의 서버로 트래픽을 균등하게 분산해주는 로드밸런싱이 반드시 필요

## 로드밸런싱 알고리즘

### 라운드로빈 방식(Round Robin Method)

- 서버에 들어온 요청을 순서대로 돌아가며 배정하는 방식
- 여러 대의 서버가 동일한 스펙을 갖고 있고, 서버와의 연결(세션)이 오래 지속되지 않는 경우에 활용하기 적합

### 가중 라운드로빈 방식(Weighted Round Robin Method)

- 각각의 서버마다 가중치를 매기고 가중치가 높은 서버에 클라이언트 요청을 우선적으로 배분
- 서버의 트래픽 처리 능력이 상이한 경우 사용되는 부하 분산 방식

### IP 해시 방식(IP Hash Method)

- 클라이언트의 IP 주소를 특정 서버로 매핑하여 요청을 처리하는 방식
- 사용자의 IP를 해싱해(Hashing, 임의의 길이를 지닌 데이터를 고정된 길이의 데이터로 매핑하는 것, 또는 그러한 함수) 로드를 분배하기 때문에 사용자가 항상 동일한 서버로 연결되는 것을 보장

### 최소 연결 방식(Least Connection Method)

- 요청이 들어온 시점에 가장 적은 연결상태를 보이는 서버에 우선적으로 트래픽을 배분
- 자주 세션이 길어지거나, 서버에 분배된 트래픽들이 일정하지 않은 경우에 적합한 방식

### 최소 리스폰타임(Least Response Time Method)

- 서버의 현재 연결 상태와 응답시간(Response Time, 서버에 요청을 보내고 최초 응답을 받을 때까지 소요되는 시간)을 모두 고려하여 트래픽을 배분
- 가장 적은 연결 상태와 가장 짧은 응답시간을 보이는 서버에 우선적으로 로드를 배분하는 방식

## L4 로드밸런싱과 L7 로드밸런싱

### L4 로드밸런싱

![L4 로드밸런싱](https://post-phinf.pstatic.net/MjAxOTEyMTBfNCAg/MDAxNTc1OTU1MzY3OTM2.nG91HOEOh6Sc1AuUgbN3O4pcnEI-rh24UKSrrrjkrcsg.VcG18MidW4az7Oh0RQfRPLDBHNRyGayE1BsQxDImL3Ig.JPEG/L4-%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%8B%B1.jpg?type=w1200)

- 네트워크 계층(IP, IPX)이나 트랜스포트 계층(TCP, UDP)의 정보를 바탕으로 로드를 분산
- IP주소나 포트번호, MAC주소, 전송 프로토콜에 따라 트래픽을 나누는 것이 가능
- 데이터 안을 들여다보지 않고 패킷 레벨에서만 로드를 분산하기 때문에 속도가 빠르고 효율이 높음.
- 데이터의 내용을 복호화할 필요가 없기에 안전함.
- 패킷의 내용을 살펴볼 수 없기 때문에 섬세한 라우팅이 불가능.
- 사용자의 IP가 수시로 바뀌는 경우라면 연속적인 서비스를 제공하기 어려움.

### L7 로드밸런싱

![L7 로드밸런싱](https://post-phinf.pstatic.net/MjAxOTEyMTBfMjA1/MDAxNTc1OTU1MzgxODY5.odnG4CRES0e5bH7sOKyWRP1c8uO_XC4VX9A3HPeI1JQg.lNL2eJYbMz6NX1e5YFzfHDMQHn4YrdOJR2VYHmq5e1Ig.JPEG/L7-%EB%A1%9C%EB%93%9C%EB%B0%B8%EB%9F%B0%EC%8B%B1.jpg?type=w1200)

- 애플리케이션 계층(HTTP, FTP, SMTP)에서 로드를 분산
- HTTP 헤더, 쿠키 등과 같은 사용자의 요청을 기준으로 특정 서버에 트래픽을 분산하는 것이 가능
- 상위 계층에서 로드를 분산하기 때문에 훨씬 더 섬세한 라우팅이 가능함.
- 캐싱 기능 제공.
- 비정상적인 트래픽을 사전에 필터링할 수 있어 서비스 안정성이 높음.
- 패킷의 내용을 복호화해야 하기에 더 높은 비용을 지불해야 함.
