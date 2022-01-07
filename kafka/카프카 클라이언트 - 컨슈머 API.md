# 카프카 클라이언트 - 컨슈머 API

프로듀서가 전송한 데이터는 카프카 브로커에 적재. 컨슈머는 적재된 데이터를 사용하기 위해 브로커로부터 데이터를 가져와서 필요한 처리를 함.

## 컨슈머 예제

```
public class SimpleConsumer {
    private final static Logger logger = LoggerFactory.getLogger(SimpleConsumer.class);
    private final static String TOPIC_NAME = "test"; // 토픽명
    private final static String BOOTSTRAP_SERVERS = "my-kafka:9092"; // 카프카 클러스터 서버
    private final static String GROUP_ID = "test-group";

    public static void main(String[] args) {
        // 컨슈머 옵션
        Properties configs = new Properties();
        configs.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, BOOTSTRAP_SERVERS);
        configs.put(ConsumerConfig.GROUP_ID_CONFIG, GROUP_ID); // 컨슈머 그룹 이름 선언
        configs.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName()); // 메시지 키 직렬화 옵션
        configs.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName()); // 메시지 값 직렬화 옵션

        // 인스턴스 생성
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(configs);
        consumer.subscribe(Arrays.asList(TOPIC_NAME));

        while (true) {
            ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(1));
            for (ConsumerRecord<String, String> record : records) {
                logger.info("{}", record);
            }
        }
    }
}
```

## 컨슈머 중요 개념

토픽과 파티션으로부터 데이터를 가져가기 위해 컨슈머를 운영하는 방법은 크게 2가지. 첫 번째는 1개 이상의 컨슈머로 이루어진 컨슈머 그룹을 운영하는 것. 두 번째는 토픽의 특정 파티션만 구독하는 컨슈머를 운영하는 것.

컨슈머 그룹으로 운영하는 방법은 컨슈머를 각 컨슈머 그룹으로부터 격리된 한경에서 안전하게 운영할 수 있도록 도와주는 방식. 컨슈머 그룹으로 묶인 컨슈머들은 토픽의 1개 이상 파티션들에 할당되어 데이터를 가져갈 수 있음.

컨슈머 그룹으로 묶인 컨슈머가 토픽을 구독해서 데이터를 가져갈 때, 1개의 파티션은 최대 1개의 컨슈머에 할당 가능. 1개 컨슈머는 여러 개의 파티션에 할당될 수 있음. 컨슈머 그룹의 컨슈머 개수는 가져가고자 하는 토픽의 파티션 개수보다 같거나 작아야 함.

파티션을 할당받지 못한 컨슈머는 스레드만 차지하고 실질적인 데이터 처리를 하지 못함.

컨슈머 그룹은 다른 컨슈머 그룹과 격리되는 특징을 가짐. 따라서 카프카 프로듀서가 보낸 데이터를 각기 다른 역할을 하는 컨슈머 그룹끼리 영향을 받지 않게 처리할 수 있다는 장점을 가짐.

각기 다른 저장소에 저장하는 컨슈머를 다른 컨슈머 그룹으로 묶음으로써 각 저장소의 장애에 격리되어 운영할 수 있음.

컨슈머 그룹으로 이루어진 컨슈머 중 일부 컨슈머에 장애가 발생할 경우, 장애가 발생한 컨슈머에 할당된 파티션은 장애가 발생하지 않은 컨슈머에 소유권이 넘어감. 이러한 과정을 리밸런싱(rebalancing)라고 부름. 리밸런싱은 크게 두 가지 상황에서 일어나는데, 첫 번째는 컨슈머가 추가되는 상황이고 두 번째는 컨슈머가 제외되는 상황. 컨슈머 중 1개에 이슈가 발생하여 더는 동작을 안 하고 있다면 이슈가 발생한 컨슈머에 할당된 파티션은 더는 데이터 처리를 하지 못하고 있으므로 데이터 처리에 지연이 발생할 수 있음. 이를 해소하기 위해 이슈가 발생한 컨슈머를 컨슈머 그룹에서 제외하여 모든 파티션이 지속적으로 데이터를 처리할 수 있도록 가용성을 높여줌. 리밸런싱은 컨슈머가 데이터를 처리하는 도중에 언제든지 발생할 수 있으므로 데이터 처리 중 발생한 리밸런싱에 대응하는 코드를 작성해야 함.

리밸런싱은 유용하지만 자주 일어나서는 안 됨. 리밸런싱이 발생할 때 파티션의 소유권을 컨슈머로 재할당하는 과정에서 해당 컨슈머 그룹의 컨슈머들이 토픽의 데이터를 읽을 수 없기 때문. 그룹 조정자(group coordinator)는 리밸런싱을 발동시키는 역할을 하는데 컨슈머 그룹의 컨슈머가 추가되고 삭제될 때를 감지함. 카프카 브로커 중 한 대가 그룹 조정자의 역할을 수행함.

컨슈머는 카프카 브로커로부터 데이터를 어디까지 가져갔는지 커밋(commit)을 통해 기록함. 특정 토픽의 파티션을 어떤 컨슈머 그룹이 몇 번째 가져갔는지 카프카 브로커 내부에서 사용되는 내부 토픽(**consumer_offsets)에 기록됨. 컨슈머 동작 이슈가 발생하여 **consumer_offsets 토픽에 어느 레코드까지 읽어갔는지 오프셋 커밋이 기록되지 못했다면 데이터 처리의 중복이 발생할 수 있음. 데이터 처리의 중복이 발생하지 않게 하기 위해서는 컨슈머 애플리케이션이 오프셋 커밋을 정상적으로 처리했는지 검증해야만 함.

오프셋 커밋은 컨슈머 애플리케이션에서 명시적, 비명시적으로 수행할 수 있음. 기본 옵션은 poll() 메서드가 수행될 때 일정 간격마다 오프셋을 커밋하도록 enable.auto.commit=true로 설정되어 있음. 이렇게 일정 간격마다 자동으로 커밋되는 것을 비명시 '오프셋 커밋'이라고 부름. 이 옵션은 설정된 값 이상이 자낫을 때 그 시점까지 읽은 레코드의 오프셋을 커밋하는 auto.commit.interval.ms 설정정과 함께 사용됨. 비명시 오프셋 커밋은 편리하지만 poll() 메서드 호출 이후에 리밸런싱 또는 컨슈머 강제종료 발생 시 컨슈머가 처리하는 데이터가 중복 또는 유실될 수 있는 가능성이 있는 취약한 구조를 가지고 있음.

명시적 오프셋을 커밋하려면 poll() 메서드 호출 이후에 반환받은 데이터의 처리가 완료되고 commitSync() 메서드를 호출하면 됨. commitSync() 메서드는 poll() 메서드를 통해 반환된 레코드의 가장 마지막 오프셋을 기준으로 커밋을 수행함. commitSync() 메서드는 브로커에 커밋 요청을 하고 커밋이 정상적으로 처리되는지 응답하기까지 기다리기 때문에 동일 시간당 데이터 처리량이 줄어드는 단점이 존재. 이를 해결하기 위해 commitAsync() 메서드를 사용하여 커밋 요청을 전송하고 응답이 오기 전까지 데이터 처리를 수행하는 비동기 커밋을 사용할수도 있음. 하지만 비동기 커밋은 커밋 요청이 실패했을 경우 현재 처리 중인 데이터의 순서를 보장하지 않으며 데이터의 중복 처리가 발생할 수도 있음.

컨슈머는 poll() 메서드를 통해 레코드들을 반환받지만 poll() 메서드를 호출하기 이전에 내부 Fetcher 인스턴스에서 미리 레코드를 내부 큐로 가져옴.

## 컨슈머 주요 옵션

### 필수 옵션

- bootstrap.servers: 컨슈머가 데이터를 받을 카프카 클러스터에 속한 브로커 호스트 이름:포트를 1개 이상 작성. 2개 이상 입력하여 일부 브로커에 이슈가 발생하더라도 접속하는 데에 이슈가 없도록 설정 가능.
- key.deserializer: 레코드의 메시지 키를 역직렬화하는 클래스를 지정.
- value.deserializer: 레코드의 메시지 값를 역직렬화하는 클래스를 지정.

### 선택 옵션

- group.id: 컨슈머 그룹 아이디를 지정. subscribe() 메서드로 토픽을 구독하여 사용할 때는 이 옵션을 필수로 넣어야 함. 기본값은 null.
- auto.offset.reset: 컨슈머 그룹이 특정 파티션을 읽을 때 저장된 컨슈머 오프셋이 없는 경우 어느 오프셋부터 읽을지 선택하는 옵션. 이미 컨슈머 오프셋이 있다면 이 옵션값은 무시됨. latest, earliest, none 중 1개를 설정할 수 있으며 latest로 설정하면 가장 최근에 넣은 오프셋부터 읽기 시작. earliest로 설정하면 가장 오래전 오프셋부터 읽기 시작. none으로 설정하면 컨슈머 그룹이 커밋한 기록이 있는지 찾아보고 기록이 없으면 오류를 반환, 기록이 있으면 기존 커밋 이후 오프셋부터 읽기 시작. 기본값은 latest.
- enable.auto.commit: 자동 커밋으로 할지 수동 커밋으로 할지 선택. 기본값은 true
- auto.commit.interval.ms: 자동 커밋일 경우 오프셋 커밋 간격을 지정. 기본값은 5초
- max.poll.records: poll() 메서드를 통해 반환되는 레코드 개수를 지정. 기본값은 5000
- session.timeout.ms: 컨슈머가 브로커와 연결이 끊기는 최대 시간. 이 시간 내에 하트비트(heartbeat)를 전송하지 않으면 브로커는 컨슈머에 이슈가 발생했다고 가정하고 리밸런싱을 시작. 보통 하트비트 시간 간격의 3배로 설정. 기본값은 10초
- heartbeat.interval.ms: 하트비트를 전송하는 시간 간격. 기본값은 3초.
- max.poll.interval.ms: poll() 메서드를 호출하는 간격의 최대 시간을 지정. poll() 메서드를 호출한 이후에 데이터를 처리하는 데에 시간이 너무 많이 걸리는 경우 비정상으로 판단하고 리밸런싱을 시작. 기본값은 5분.
- isolation.level: 트랜잭션 프로듀서가 레코드를 트랜잭션 단위로 보낼 경우 사용. read_committed, read_uncommitted로 설정할 수 있음. read_committed로 설정하면 커밋이 완료된 레코드만 읽음. read_uncommitted로 설정하면 커밋 여부와 관계없이 파티션에 있는 모든 레코드를 읽음. 기본값은 read_uncommitted.

## 동기 오프셋 커밋

poll() 메서드가 호출된 이후 commitSync() 메서드를 호출하여 오프셋 커밋을 명시적으로 수행할 수 있음.

```
configs.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);

...

while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(1));
    for (ConsumerRecord<String, String> record : records) {
        logger.info("{}", record);
    }
    consumer.commitSync();
}
```

commitSync()는 poll() 메서드로 받은 가장 마지막 레코드의 오프셋을 기준으로 커밋함. 그렇기 떄문에 동기 오프셋 커밋을 사용할 경우 poll() 메서드로 받은 레코드의 처리가 끝난 이후 commitSync() 메서드를 호출해야 함. 동기 커밋의 경우 브로커로 커밋을 요청한 이후에 커밋이 완료되기까지 기다리기 때문에 자동 커밋이나 비동기 오프셋 커밋보다 동일 시간당 데이터 처리량이 적다는 특징이 있음.

commitSync()에 파라미터가 들어가지 않으면 poll()로 반환된 가장 마지막 레코드의 오프셋을 기준으로 커밋. 개별 레코드 단위로 매번 오프셋을 커밋하고 싶다면, commitSync() 메서드에 Map<TopicPartition, OffsetAndMetadata> 인스턴스를 파라미터로 넣으면 됨.

```
while (true) {
ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(1));
Map<TopicPartition, OffsetAndMetadata> currentOffset = new HashMap<>();
for (ConsumerRecord<String, String> record : records) {
    logger.info("{}", record);
    currentOffset.put(
            new TopicPartition(record.topic(), record.partition()),
            new OffsetAndMetadata(record.offset() + 1, null));
}
consumer.commitSync(currentOffset);
```

### 비동기 오프셋 커밋

동기 오프셋 커밋을 사용할 경우 커밋 응답을 기다리는 동안 데이터 처리가 일시적으로 중단되기 때문에 더 많은 데이터를 처리하기 위해 비동기 오프셋 커밋을 사용할 수 있음. 비동기 오프셋 커밋은 commitAsync() 메서드를 호출하여 사용할 수 있음.

```
while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(1));
    for (ConsumerRecord<String, String> record : records) {
        logger.info("{}", record);
    }
    consumer.commitAsync();
}
```

비동기 오프셋 커밋도 동기 커밋과 마찬가지로 poll() 메서드로 리턴된 가장 마지막 레코드를 기준으로 오프셋을 커밋함. 단 커밋이 완료될 때까지 응답을 기다리지 않음. 이 때문에 동기 오프셋 커밋을 사용할 때보다 동일 시간당 데이터 처리량이 더 많음. 비동기 오프셋 커밋을 사용할 경우 비동기로 커밋 응답을 받기 때문에 callback 함수를 파라미터로 받아서 결과를 얻을 수 있음.

```
consumer.commitAsync(new OffsetCommitCallback() {
    @Override
    public void onComplete(Map<TopicPartition, OffsetAndMetadata> offsets, Exception e) {
        if (e != null) {
            System.out.println("Commit failled");
        } else {
            System.out.println("Commit succeeded");
        }
        if (e != null) {
            logger.error("Commit failed for offsets {}", offsets, e);
        }
    }
});
```

OffsetCommitCallback 함수는 commitAsync()의 응답을 받을 수 있도록 도와주는 콜백 인터페이스. 비동기로 받은 커밋 응답은 onComplete() 메서드를 통해 확인할 수 있음.

## 리밸런스 리스너를 가진 컨슈머

컨슈머 그룹에서 컨슈머가 추가 또는 제거되면 파티션을 컨슈머에 재할당하는 과정인 리밸런스가 일어남. poll() 메서드를 통해 반환받은 데이터를 모두 처리하기 전에 리밸런스가 발생하면 데이터를 중복 처리할 수 있음.

리밸런스 발생 시 데이터를 중복 처리하지 않게 하기 위해서는 리밸런스 발생 시 처리한 데이터를 기준으로 커밋을 시도해야 함.

```
while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(1));
    for (ConsumerRecord<String, String> record : records) {
        logger.info("{}", record);
        currentOffsets.put(
                new TopicPartition(record.topic(), record.partition()),
                new OffsetAndMetadata(record.offset() + 1, null));
        consumer.commitSync(currentOffsets);
    }
}

...

private static class RebalanceListener implements ConsumerRebalanceListener {

    @Override
    public void onPartitionsRevoked(Collection<TopicPartition> partitions) {
        // 리밸런스가 시작되기 직전에 호출되는 메서드
        logger.warn("Partitions are revoked");
        consumer.commitSync(currentOffsets);
    }

    @Override
    public void onPartitionsAssigned(Collection<TopicPartition> partitions) {
        // 리밸런스가 끝난 뒤에 파티션이 할당완료되면 호출되는 메서드
        logger.warn("Partitions are assigned");
    }
}
```

## 파티션 할당 컨슈머

컨슈머를 운영할 때 subscribe() 메서드를 사용하여 구독 형태로 사용하는 것 외에도 직접 파티션을 컨슈머에 명시적으로 할당하여 운영할 수도 있음. 파티션을 명시적으로 선언할 때는 assign() 메서드를 사용하면 됨.

```
consumer.assign(Collections.singleton(new TopicPartition(TOPIC_NAME, PARTITION_NUMBER)));
```

subscribe() 메서드 대신 assign() 메서드가 사용되며, subscribe() 메서드를 사용할 때와 달리 직접 컨슈머가 특정 토픽, 특정 파티션에 할당되므로 리밸런싱 하는 과정이 없음.

## 컨슈머에 할당된 파티션 확인 방법

컨슈머에 할당된 토픽과 파티션에 대한 정보는 assignment() 메서드로 확인할 수 있음.

```
Set<TopicPartition> assignedTopicPartition = consumer.assignment();
```

## 컨슈머의 안전한 종료

컨슈머 애플리케이션은 안전하게 종료되어야 함. 정상적으로 종료되지 않은 컨슈머는 세션 타임아웃이 발생할때까지 컨슈머 그룹에 남게 됨. 이로 인해 실제로 종료되었지만 더는 동작을 하지 않는 컨슈머가 존재하기 떄문에 파티션의 데이터는 소모되지 못하고 컨슈머 랙이 늘어나게 되면서 지연이 발생함. 컨슈머를 안전하게 종료하기 위해 KafkaConsumer 클래스는 wakeup() 메서드를 지원함. wakeup() 메서드가 실행된 이후 poll() 메서드가 호출되면 WakeupException 예외가 발생함. WhakeupException() 예외를 받은 뒤에는 데이터 처리를 위해 사용한 자원들을 해제하면 됨. 마지막에는 close() 메서드를 호출하여 카프카 클러스터에 컨슈머가 안전하게 종료되었음을 명시적으로 알려주면 종료가 완료됨.

```
try {
    while (true) {
        ConsumerRecords<String, String> records = consumer.poll(Duration.ofSeconds(1));
        for (ConsumerRecord<String, String> record : records) {
            logger.info("{}", record);
        }
    }
} catch (WakeupException e) {
    logger.warn("Wakeup consumer");
} finally {
    consumer.close();
}
```

wakeup() 메서드는 자바 애플리케이션에 셧다운 훅(shutdown hook)을 구현하여 호출하면 용이함. 셧다운 훅은 사용자 또는 운영체제로부터 종료 요청을 받으면 실행하는 스레드를 의미.

```
static class ShutdownThread extends Thread {
    public void run() {
        logger.info("shutdown hook");
        consumer.wakeup();
    }
}
```

애플리케이션은 kill - TERM {프로세스 번호}로 종료하면 되며, 셧다운 훅이 발생하면 ShutdownThread 스레드가 실행되면서 컨슈머를 안전하게 종료할 수 있음.
