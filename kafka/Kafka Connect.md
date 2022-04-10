# Kafka Connect

![Kafka Connect](https://blog.kakaocdn.net/dn/8yIfT/btqSXj8q4P9/YtyvFrCBepgoRo1UBzusk0/img.png)

Kafka는 아키텍처 중심에서 다양한 외부 시스템과 메시지 파이프라인을 구성함. 일반적으로 메시지를 송수신하기 위해 외부 시스템에 producer, consumer를 구현하게 되는데, 이때 외부 시스템의 수가 많아지면 구현 반복 작업이 많아지고 개발 비용이 증가하게 됨.

Kafka Connect는 이러한 개발 비용을 없애고, 쉽고 간단하게 메시지 파이프라인을 구성할 수 있도록 도와주는 아파치 카프카 프로젝트이자 프레임워크.

Kafka Connect는 카프카 브로커 클러스터와 별도로 구성됨.

## Connect와 Connector

Kafka Connect 공부를 시작하면서 가장 헷갈리게 되는 부분, Connect와 Connector를 구분하는 것이 중요.

### Connect

![Connect](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcl7rAm%2FbtqD2caZxZC%2FJX2fkIeQxps2lCGyaFETzK%2Fimg.png)

Connector를 등록하는 프레임워크이자 서버, 모듈들의 실행 순서를 관장함.

Connect에 Connector에 관련된 설정(property) 명세를 전달하면 Connector가 생성되어 메시지 파이프라인이 구성됨. 단일 모드일 경우 파일 형태로, 분산 모드일 경우 REST API를 통해 명세를 전달함.

Connect는 단일 모드(Standalone)와 분산 모드(Distributed)로 구성할 수 있음. 일반적으로 단일 모드는 개발 환경에서, 분산 모드는 운영 환경에서 사용. 분산 모드를 사용하게 되면 커넥트 클러스터 내부에서 작업이 자동으로 분산 동작하게 되고, 장애가 발생했을 경우 자동으로 노드 간 작업 이관이 됨.

### Connector

![Connector](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FHV5O3%2FbtqD1NoZJ2c%2FtDiOw4RnJfW2VnHXe8FPbk%2Fimg.png)

Connect 내부의 실제 메시지 파이프라인. Connect에서 설정 명세를 전달받아 생성됨. 구성된 Connector는 주기적으로 메시지를 확인하고, 새로운 메시지가 있으면 파이프라인을 통해 흘려보냄.

Connector는 역할에 따라 Source Connector, Sink Connector 2가지 형태로 구분함. Source Connector는 producer 역할을 하며 외부 시스템으로부터 메시지를 전달받아 카프카로 전달함. Sync Connector는 반대로 consumer 역할을 하며 카프카로부터 메시지를 전달받아 외부 시스템으로 전달함.

- Source Connector: 외부 시스템 -> 커넥트 -> 카프카
- Sync Connector: 카프카 -> 커넥트 -> 외부 시스템

## 정리

정리를 하자면 Kafka Connect는 일종의 템플릿이 구현되어 있고, 그 템플릿과 설정값을 기준으로 인스턴스를 생성하는 방식. 그 템플릿은 플러그인(plugin)이라고 정의하며 실제로 Connector에 해당함.

이외에도 상세하게 파고들면 아래와 같이 다양한 내부 구성요소들이 존재함.

> - 커넥터 (Connector) : 파이프라인에 대한 추상 객체. task들을 관리
> - 테스크 (Task) : 카프카와의 메시지 복제에 대한 구현체. 실제 파이프라인 동작 요소
> - 워커 (Worker) : 커넥터와 테스크를 실행하는 프로세스
> - 컨버터 (Converter) : 커넥트와 외부 시스템 간 메시지를 변환하는 객체.
> - 트랜스폼 (Transform) : 커넥터를 통해 흘러가는 각 메시지에 대해 간단한 처리
> - 데드 레터 큐 (Dead Letter Queue) : 커넥트가 커넥터의 에러를 처리하는 방식

![Source 파이프라인의 추상화된 흐름](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeGJSL6%2FbtqD3JeCclB%2F4TZZQQf4zRJdJMf3Jrk1zk%2Fimg.png)

Connector, 즉 외부 시스템에 대한 plugin은 [confluent.io/hub](confluent.io/hub)에서 오픈소스로 제공됨. Source/Sink 따로 제공되는 경우가 많으므로 파이프라인 구조를 잘 고려해야 하며, 업체 혹은 커뮤니티에 따라 라이센스가 각각 다르기 때문에 이를 확인해야함.

![JDBC Connector](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FboG2oH%2FbtqD2bQH5Ft%2FdORzZMdANyNsaPjlgbGVvK%2Fimg.png)

![plugin](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbuPBX7%2FbtqD2cWorp7%2FkRdy1nHeeKYKiTfAkGnAw1%2Fimg.png)

### 오픈소스 커넥터

- 오픈소스 커넥터는 직접 커넥터를 만들 필요가 없으며 jar파일을 다운로드 하여 사용할 수 있다는 장점이 있음.
- 종류로는 HDFS, AWS S3, JDBC 커넥터, 엘라스틱서치 커넥터 등이 존재.
- 자료는 [컨플루언트 허브](https://www.confluent.io/hub/)에서 구할 수 있다.

> 출처
>
> - https://cjw-awdsd.tistory.com/53
> - https://always-kimkim.tistory.com/entry/kafka101-connect
