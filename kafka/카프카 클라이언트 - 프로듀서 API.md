# 카프카 클라이언트 - 프로듀서 API

프로듀서 애플리케이션은 카프카에 필요한 데이터를 선언하고 브로커의 특정 토픽의 파티션에 전송함. 프로듀서는 데이터를 전송할 때 리더 파티션을 가지고 있는 카프카 브로커와 직접 통신함.

프로듀서는 데이터를 직렬화하여 바이트 형태의 데이터를 카프카 브로커로 보냄.

## 프로듀서 예제

```
public class SimpleProducer {
    private final static Logger logger = LoggerFactory.getLogger(SimpleProducer.class);
    private final static String TOPIC_NAME = "test"; // 토픽명
    private final static String BOOTSTRAP_SERVERS = "my-kafka:9092"; // 카프카 클러스터 서버

    public static void main(String[] args) {

        // 프로듀서 옵션
        Properties configs = new Properties();
        configs.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, BOOTSTRAP_SERVERS);
        configs.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName()); // 메시지 키 직렬화 옵션
        configs.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName()); // 메시지 값 직렬화 옵션

        // 인스턴스 생성
        KafkaProducer<String, String> producer = new KafkaProducer<>(configs);


        String messageValue = "testMessage";
        ProducerRecord<String, String> record = new ProducerRecord<>(TOPIC_NAME, messageValue);
        producer.send(record); // 메시지값 전송, 즉각적인 전송은 아니고 record를 프로듀서 내부에 가지고 있다가 배치 형태로 묶어서 브로커에 전송.
        logger.info("{}", record);
        producer.flush(); // 내부 버퍼에 가지고 있던 레코드 배치를 브로커로 전송
        producer.close(); // 인스턴스 리소스 종료
    }
}
```

## 프로듀서 중요 개념

프로듀서는 카프카 브로커로 데이터를 전송할 때 내부적으로 파티셔너, 배치 생성 단계를 거침.

ProducerRecord 인스턴스 생성시 필수 파라미터인 토픽과 메시지 값 뿐만 아니라 추가 파라미터를 사용하여 파티션 번호를 직접 지정하거나 타임스탬프, 메시지 키를 설정할 수도 있음. 레코드의 타임스탬프는 기본적으로 브로커 시간을 기준으로 설정되지만 필요에 따라 레코드 생성 시간 또는 그 전후로 설정할 수도 있음.

KafkaProducer 인스턴스가 send() 메서드를 호출하면 ProducerRecord는 파티셔너(partitioner)에서 토픽의 어느 파티션으로 전송될 것인지 정해짐. KafkaProducer 인스턴스를 생성할 때 파티셔너를 따로 설정하지 않으면 기본값인 DefaultPartitioner로 설정되어 파티션이 정해짐. 파티셔너에 의해 구분된 레코드는 데이터를 전송하기 전에 어큐뮬레이터(accumulator)에 데이터를 버퍼로 쌓아놓고 발송함. 버퍼로 쌓인 데이터는 배치로 묶어서 전송함으로써 카프카의 프로듀서 처리량을 향상시킴.

프로듀서 API에서는 'UniformStickyPartitioner'와 'RoundRobinPartitioner' 2개의 파티셔너를 제공함. 파티셔너를 지정하지 않은 경우 'UniformStickyPartitioner'로 기본 설정됨. 둘 다 메시지 키가 있을 때는 메시지 키의 해시값과 파티션을 매칭하여 데이터를 전송함. 메시지 키가 없을 때는 파티션에 최대한 동일하게 분배.

UniformStickyPartitioner는 프로듀서 동작에 특화되어 높은 처리량과 낮은 리소스 사용률을 가짐. RoundRobinPartitioner는 ProducerRecord가 들어오는 대로 파티션을 순회하면서 전송하기 때문에 배치로 묶이는 빈도가 적음. UniformStickyPartitioner는 어큐뮬레이터에 데이터가 배치로 모두 묶일 때까지 기다렸다가 배치로 묶인 데이터는 모두 동일한 파티션에 전송함으로써 더 향상된 성능을 가짐.

Partitioner 인터페이스를 상송받아 사용자 정의 파티셔너를 생성할 수도 있음.

추가적으로 압축 옵션을 통해 브로커로 전송시 압축 방식을 정할 수도 있음. 압축 옵션을 정하지 않으면 압축이 되지 않은 상태로 전송됨.

## 프로듀서 주요 옵션

### 필수 옵션

- bootstrap.servers: 프로듀서가 데이터를 전송할 대상 카프카 클러스터의 보로커 호스트 이름:포트. 2개 이상 브로커 정보를 입력하여 일부 브로커에 이슈가 발생하더라도 접속하는 데에 이슈가 없도록 설정 가능.
- key.serializer: 레코드 메시지 키를 직렬화하는 클래스 지정.
- value.serializer: 레코드 메시지 값을 직렬화하는 클래스 지정.

### 선택옵션

- acks: 프로듀서가 전송한 데이터가 브로커들에 정상적으로 저장되었는지 전송 성공 여부를 확인하는 데에 사용하는 옵션. 0, 1, -1(all) 중 하나로 설정할 수 있음. 설정값에 따라 데이터의 유실 가능성이 달라짐. 기본값 1은 리더 파티션에 데이터가 저장되면 전송 성공으로 판단. 0은 프로듀서가 전송한 즉시 브로커에 데이터 저장 여부와 상관 없이 성공으로 판단. -1 또는 all은 토픽의 min.insync.replicas 개수에 해당하는 리더 파티션과 팔로워 파티션에 데이터가 저장되면 성공으로 판단.
- buffer.memory: 브로커로 전송할 데이터를 배치로 모으기 위해 설정할 버퍼 메모리양을 지정. 기본값은 33554432(32MB)
- retries: 프로듀서가 브로커로부터 에러를 받고 난 뒤 재전송을 시도하는 횟수. 기본값은 2147483647
- batch.size: 배치로 전송할 레코드 최대 용량. 너무 작게 설정하면 프로듀서가 브로커로 더 자주 보내기 때문에 네트워크에 부담, 너무 크게 설정하면 메모리를 과다하게 사용. 기본값은 16384
- linger.ms: 배치를 전송하기 전까지 기다리는 최소 시간. 기본값은 0
- partitioner.class: 레코드를 파티션에 전송할 때 적용하는 파티셔너 클래스를 지정.
- enable.idempotence: 멱등성 프로듀서로 동작할지 여부를 설정. 기본값은 false
- transactional.id: 프로듀서가 레코드를 전송할 때 레코드를 트랜잭션 단위로 묶을지 여부를 설정. 프로듀서의 고유한 트랜잭션 아이디를 설정할 수 있음.

## 메시지 키를 가진 데이터를 전송하는 프로듀서

토픽 이름, 파티션 번호, 메시지 키, 메시지 값을 순서대로 파라미터로 넣고 생성.

```
ProducerRecord<String, String> record = new ProducerRecord<>(TOPIC_NAME, messageKey, messageValue);
```

## 파티션 직접 지정

토픽 이름, 파티션 번호, 메시지 키, 메시지 값을 순서대로 파라미터로 넣고 생성.

```
ProducerRecord<String, String> record = new ProducerRecord<>(TOPIC_NAME, partitionNo, messageKey, messageValue);
```

## 커스텀 파티셔너를 가지는 프로듀서

```
public class CustomPartitioner implements Partitioner {

    @Override
    public int partition(String topic, Object key, byte[] keyBytes, Object value, byte[] valueBytes, Cluster cluster) {
        if (keyBytes == null) {
            throw new InvalidRecordException("Need message key");
        }
        if (key.equals("Pangyo")) { // key 이름이 Pangyo일 경우 0번 파티션
            return 0;
        }

        List<PartitionInfo> partitions = cluster.partitionsForTopic(topic);
        int numPartitions = partitions.size();
        return Utils.toPositive(Utils.murmur2(keyBytes)) % numPartitions; // 이외에는 해시값으로 파티션 매칭
    }

    @Override
    public void close() {

    }

    @Override
    public void configure(Map<String, ?> configs) {

    }
}
```

커스텀 파티셔너를 지정한 경우 ProducerConfig의 PARTITIONER_CLASS_CONFIG 옵션을 사용자 생성 파티셔너로 설정해서 KafkaProducer 인스턴스를 생성해야 함.

## 브로커 정상 전송 여부를 확인하는 프로듀서

KafkaProducer의 send() 메서드는 Future 객체를 반환. 이 객체는 RecordMetadata의 비동기 결과를 표현하는 것으로 ProducerRecord가 카프카 브로커에 정상적으로 적재되었는지에 대한 데이터가 포함되어 있음. get() 메서드를 사용하여 프로듀서로 낸 데이터의 결과를 동기적으로 가져올 수 있음.

```
ProducerRecord<String, String> record = new ProducerRecord<>(TOPIC_NAME, messageValue);
RecordMetadata metadata = producer.send(record).get();
logger.info(metadata.toString());
```

send()의 결과값은 카프카 브로커로부터 응답을 기다렸다가 브로커로부터 응답이 오면 RecordMetadata 인스턴스를 반환함. RecordMetadata에는 토픽 이름, 파티션 번호, 오프셋 번호가 포함됨.

동기로 프로듀서의 전송 결과를 확인하는 것은 브로커로부터 전송에 대한 응답 값을 받기 전까지 대기해야하기 때문에 빠른 전송에 허들이 됨. 이에 대응해 프로듀서는 비동기로 결과를 확인할 수 있도록 Callback 인터페이스를 제공하고 있음.

```
public class ProducerCallback implements Callback {
    private final static Logger logger = LoggerFactory.getLogger(ProducerCallback.class);

    @Override
    public void onCompletion(RecordMetadata recordMetadata, Exception e) {
        if (e != null) {
            logger.error(e.getMessage(), e);
        } else {
            logger.info(recordMetadata.toString());
        }
    }
}
```

```
producer.send(record, new ProducerCallback());
```
