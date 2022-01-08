# 어드민 API

실제 운영환경에서는 프로듀서와 컨슈머를 통해 데이터를 주고받는 것만큼 카프카에 설정된 내부 옵션을 설정하고 확인하는 것이 중요함. 내부 옵션을 확인하는 가장 확실한 방법은 브로커 중 한 대에 접속하여 카프카 브로커 옵션을 확인하는 것. 그러나 이것은 매우 번거로운 작업임. 카프카 커맨드 라인 인터페이스로 명령을 내려 확인하는 방법도 있지만 일회성 작업에 한정됨.

카프카 클라이언트에서는 내부 옵션들을 설정하거나 조회하기 위해 AdminClient 클래스를 제공함. AdminClient 클래스를 활용하면 클러스터의 옵션과 관련된 부분을 자동화할 수 있다.

- 카프카 컨슈머를 멀티 스레드로 생성할 때, 구독하는 토픽의 파티션 개수만큼 스레드를 생성하고 싶을 때, 스레드 생성 전에 해당 토픽의 파티션 개수를 어드민 API를 통해 가져올 수 있음.
- AdminClient 클래스로 구현한 웹 대시보드를 통해 ACL(Access Control List)이 적용된 클러스터의 리소스 접근 권한 규칙 추가를 할 수 있음.
- 특정 토픽의 데이터량이 늘어남을 감지하고 AdminCllient 클래스로 해당 토픽의 파티션을 늘릴 수 있음.

## 어드민 예제

어드민 API 선언

```
Properties configs = new Properties();
configs.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "my-kafka:9092");
AdminClient admin = AdminClient.create(configs);
```

프로듀서 API 또는 컨슈머 API와 다르게 추가 설정 없이 클러스터 정보에 대한 설정만 하면 됨. create() 메서드로 KafkaAdminClient를 반환받음.

### KafkaAdminClient 주요 메서드

| KafkaAdminClient 메서드명                                                                   | 설명                |
| ------------------------------------------------------------------------------------------- | ------------------- |
| describeCluster(DescribeClusterOptions options)                                             | 브로커의 정보 조회  |
| listTopics(ListTopicsOptions options)                                                       | 토픽 리스트 조회    |
| listConsumerGroups(ListConsumerGroupsOptions options)                                       | 컨슈머 그룹 조회    |
| createTopics(Collection<NewTopic> newTopics, CreateTopicsOptions options)                   | 신규 토픽 생성      |
| createPartitions(Map<String, NewPartitions> newPartitions, CreatePartitionsOptions options) | 파티션 개수 변경    |
| createAcls(Collection<AclBinding> acls, CreateAclsOptions options)                          | 접근 제어 규칙 생성 |

### 브로커 정보 조회

```
Properties configs = new Properties();
configs.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "my-kafka:9092");

AdminClient admin = AdminClient.create(configs);

logger.info("== Get broker information");
for (Node node : admin.describeCluster().nodes().get()) {
    logger.info("node : {}", node);
    ConfigResource cr = new ConfigResource(ConfigResource.Type.BROKER, node.idString());
    DescribeConfigsResult describeConfigs = admin.describeConfigs(Collections.singleton(cr));
    describeConfigs.all().get().forEach((broker, config) -> {
        config.entries().forEach(configEntry -> logger.info(configEntry.name() + "= " + configEntry.value()));
    })
}
```

### 토픽 정보 조회

```
Map<String, TopicDescription> topicInformation = admin.describeTopics(Collections.singletonList("test")).all().get();
logger.info("{}", topicInformation);
```

파티션 개수, 파티션 위치, 리더 파트션의 위치 등을 확인할 수 있음.

### 어드민 API 종료

어드민 API는 사용하고 나면 명시적으로 종료 메서드를 호출하여 리소스가 낭비되지 않도록 함.

```
admin.close();
```

어드민 API를 활용할 때는 클러스터의 버전과 클라이언트의 버전을 맞춰서 사용해야 함. 어드민 API의 많은 부분이 버전과 함께 바뀌기 떄문.
