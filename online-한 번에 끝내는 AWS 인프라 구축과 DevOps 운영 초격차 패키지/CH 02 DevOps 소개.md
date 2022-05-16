# DevOps 소개

## DevOps 개요

### DevOps 정의

> 제품의 변경사항을 품질을 보장함과 동시에 프로덕션에 반영하는데 걸리는 시간을 단축하기 위한 실천 방법의 모음

- 개발(Dev)과 운영(Ops)의 합성어
- 개발과 운영의 경계를 허물고 통합하고자 하는 문화 혹은 철학

### DevOps가 필요한 이유

> SDLC (Software Development LifeCycle)
>
> - Design
> - Develop
> - Test
> - Deploy
> - Operate
> - Support

- 소프트웨어는 위와 같은 라이프 사이클을 가짐, 조직의 규모가 크다면 각 단계 전문가로 구성된 기능 조직을 운영할 수도 있지만, 그만큼 의사소통이 많아지기 때문에 커뮤니케이션 문제가 발생하고, 병목구간이 생기기 쉬워짐.
- 데브옵스가 조직에 정착되고 나면 개발자는 작성한 코드에 대해 스스로 소통하고, 배포하고, 운영에 참여할 수 있음.

### 데브옵스는 어떻게 하는 것인가?

- 데브옵스는 패러다임이고 문화이기 떄문에 명확한 방법을 제시하지는 않음.
- 다만 개발과 운영의 벽을 허물어 더 빨리 자주 배포하자는 명확한 목표를 가짐.

### 데브옵스 실천방법: AWS

- 지속적 통합(Continuous Integration)
- 지속적 배포(Continuous Deplivery)
- 마이크로서비스(Micro-services)
- Iac(Infrastructure as Code)
- 모니터링과 로깅(Monitoring & Logging)
- 소통 및 협업(Communication & Collaboration)

### 데브옵스 실천방법: Flickr

- 변화에 대응하기 위한 도구

  - 자동화된 인프라(Automated Infrastructure)
  - 버전관리 공유(Shared Version Control)
  - 쉬운 빌드 및 배포(One-step Build and Deploy)
  - 기능 활성화 스위치(Feature Flag)
  - 메신저 봇 (IRC and IM Robot)

- 변화에 대응하기 위한 문화
  - 존중(Respect)
  - 신뢰(Trust)
  - 실패에 대한 긍정적인 자세(Healthy Attitude about Failure)
  - 비난하지 않기(Avoiding Blame)

## DevOps 엔지니어의 역할

### DevOps vs DevOps 엔지니어

- 데브옵스 엔지니어는 조직에 데브옵스 문화를 정착시키는데 도움을 주는 역할. 개발자가 개발 뿐만 아니라 운영에도 참여할 수 있는 환경을 만들어줌.
- 하는 일이 많아 보이기도 하지만, 조직이 성장하면서 업무 도메인이 더 세분화된 팀들로 구성될 수도 있음.

### 데브옵스 팀의 고객

- 데브옵스 팀에서 구축하고 운영하는 많은 시스템들의 주 사용자는 개발자.

### 데브옵스 팀의 업무 도메인(문제 단위)

- 네트워크(Network)
  - 가상 네트워크 및 물리 네트워크 구성
  - 프록시 / VPN 서버 운영
  - DNS 서버 운영
- 개발 및 배포 플랫폼 (Development & Deploymet Platform)
  - Github / GitLab 같은 버전관리 및 개발 협업 플랫폼 운영
  - CI/CD 파이프라인 시스템 구축 및 운영
  - QA 테스트 및 성능 테스트를 위한 환경 제공
  - 패키지 저장소 운영 및 배포 산출물 관리
- 오케스트레이션 플랫폼 (Orchestration Platform)
  - 쿠버네티스 / ECS / Nomad 와 같은 오케스트레이션 시스템 구축 및 운영
  - Airflow / Argo Workflows 와 같은 워크플로우 엔진 구축 및 운영
- 관측 플랫폼 (Observability Platform)
  - 로그 / 매트릭 / 업타임 / APM 정보를 관측할 수 있는 중앙화된 시스템 구축 및 운영
  - 주요 이벤트에 대한 알림 시스템 구축
- 클라우드 플랫폼(Cloud Platform)
  - 개발자들이 활용할 수 있도록 클라우드 환경 운영 (자체 클라우드, 퍼블릭 클라우드 등)
- 보안 플랫폼 (Security Platform)
  - LDAP / AD / SAML 등을 활용하여 통합된 임직원 계정계 운영
  - 서버 및 데이터베이스 접근제어 시스템 구축 및 운영
  - 네트워크 방화벽 정책 관리
- 데이터 플랫폼 (Data Platform)
  - MySQL / DynamoDB / Redis 와 같은 데이터베이스 구축 및 운영
  - RabbitMQ / Kafka / SQS 등과 같은 메시지 플랫폼 구축 및 운영
  - 데이터 웨어하우스 / BI 대시보드 구축 및 운영
- 서비스 운영 (Service Operations)
  - 개발자들과 협업하여 서비스 공동 운영

### 데브옵스 팀의 업무 도메인(행위)

- 구축(Provisoining)
- 설정(Configuration)
- 운영(Operation)
- 사용(Usage)
- 교육(Training)
- 문서화(Documentation)

### 데브옵스 팀의 핵심 지표

- 장애복구 시간, MTTR (Mean Time To Recovery)
- 변경으로 인한 결함률 (Change Failure Rate)
- 배포 빈도 (Deployment Frequency)
- 변경 적용 소요 시간 (Lead Time for Changes)
