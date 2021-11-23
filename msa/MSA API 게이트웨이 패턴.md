# MSA API 게이트웨이 패턴

## 서비스 단일 진입을 위한 API 게이트웨이 패턴

- 여러 클라이언트가 여러 개의 서버 서비스를 각각 호출하게 되는 경우, 매우 복잡한 호출 관계가 형성됨.
- 이러한 복잡성을 통제하기 위한 방법으로 API 게이트웨이(Gateway) 패턴을 사용.

![API 게이트웨이](https://engineering-skcc.github.io/assets/images/MSA2.16.png)

- API 게이트웨이 제공 기능
  - 레지스트리 서비스와 연계한 동적 라우팅, 로드 밸런싱
  - 보안: 권한 서비스와 연계한 인증/인가
  - 로그 집계 서비스와 연계한 로깅. 예: API 소비자 정보, 요청/응답 데이터
  - 메트릭(Metrics). 예: 에러율, 평균/최고 지연시간, 호출 빈도 등
  - 트레이싱 서비스와 연계한 서비스 추적. 예: 트래킹 ID 기록
  - 모니터링 서비스와 연계한 장애 격리(서킷 브레이커 패턴)
- 이러한 API 게이트웨이 패턴은 스프링 클라우드의 API 게이트웨이 서비스(Spring API Gateway Service) 제품으로 구현 가능.
- 쿠버네티스의 경우 자체 기능인 쿠버네티스 서비스(Kubernetes Service)와 인그레스 리소스(ingress resource)로 제공.

## BFF 패턴

- BFF 패턴: API 게이트웨이와 같은 진입점을 하나로 두지 않고 프런트엔드 유형에 따라 각각 두는 패턴. 프런트앤드를 위한 백엔드라는 의미로 BFF(Backend For Frontend)라고 부름.

![BFF](https://engineering-skcc.github.io/assets/images/MSA2.17.png)
