# DNS(도메인 네임 시스템)

- 호스트의 도메인 이름을 호스트의 네트워크 주소로 바꾸거나 그 반대의 변환을 수행할 수 있도록 하기 위해 개발된 것.
- 특정 컴퓨터(또는 네트워크로 연결된 임의의 장치)의 주소를 찾기 위해, 사람이 이해하기 쉬운 도메인 이름을 숫자로 된 식별 번호(IP 주소)로 변환해줌.
- 인터넷 도메인 주소 체계로서 TCP/IP의 응용, www.example.com과 같은 주 컴퓨터의 도메인 이름을 192.168.1.0과 같은 IP 주소로 변환하고 라우팅 정보를 제공하는 분산형 데이터베이스 시스템

## Domain name의 구조

- 모든 Domain name은 아래의 그림과 같은 역트리(inverted tree) 구조에 따라서 지정해서 등록하게 됨.
- 1단계부터 차례대로 TLD(Top Level Domain), SLD(Second Level Domain), SubDomain이라고 함
- 등록된 Domain 은 Subdomain.SLD.TLD 또는 Subdomain.TLD의 구조를 갖게 됨.
  ![Domain name의 구조](https://media.vlpt.us/images/bbumjun/post/3595c2ac-5cb9-4d60-8e40-c87c771df939/image.png)

## DNS 동작 원리

![DNS 동작 원리](https://media.vlpt.us/images/bbumjun/post/7fb1b222-644f-4401-b9ea-511818f5b678/image.png)

1. 사용자가 브라우저에 www.naver.com 을 입력하는 순간 클라이언트에 저장된 Local DNS 서버 IP(etc/hosts, etc/resolv.conf)에게 www.naver.com 의 ip 주소를 물어봄.
2. Local DNS 서버는 우선 자신의 캐시에 해당 Domain의 ip 주소가 저장되어있는지 확인하고, 없다면 Root DNS 서버에 www.naver.com의 ip를 물어봄.
   Root DNS 서버는 TLD 서버들의 IP주소 테이블을 참조해, 이 중 com 네임서버의 ip주소를 Local DNS 서버에 보냄.
3. Local DNS는 응답받은 com 네임서버로 www.naver.com을 보내고, 응답받은 com 네임서버도 마찬가지로 com 네임서버를 사용하는 도메인들의 ip테이블을 참조해 naver.com 네임서버의 ip주소를 보냄.
4. 마지막으로 naver.com 네임서버에 www.naver.com 요청을 보냄. www.naver.com , d2.naver.com 등 naver.com을 사용하는 모든 도메인 중 www.naver.com에 대한 ip 주소를 찾아 Local DNS 서버에게 응답
5. Local DNS는 응답받은 ip주소를 캐시에 저장하고 클라이언트에게 보냄.

> 출처
>
> - https://ko.wikipedia.org/wiki/%EB%8F%84%EB%A9%94%EC%9D%B8_%EB%84%A4%EC%9E%84_%EC%8B%9C%EC%8A%A4%ED%85%9C
> - https://velog.io/@bbumjun/What-is-DNS
