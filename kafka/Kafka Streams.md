# kafka Streams

![Kafka Streams](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FuF8L1%2FbtqEmp9yJ41%2FRSrS24sAhlRy1KxLpl8431%2Fimg.jpg)

Kafka는 본래 메시지를 다른 프로세스나 애플리케이션에 전달하기 위해 사용되지만, 카프카의 강력한 성능으로 인해 연속된 메세지를 처리하는데도 점차 사용되기 시작함.

컨슈머로 받아서 처리하는 것보다 더 빠르고 안전하게 실시간으로 처리하기 위해 카프카에서 클라이언트 라이브러리를 제공하기 시작했는데 이것이 바로 Kafka Streams이다.

## Kafka Streams의 기능

스트림즈는 다음과 같은 파이프라인 구성에 고려될 수 있음.

- 토픽 내부의 민감 데이터 마스킹
- 다양한 Source로부터 수집된 메시지 규격화
- 5분 간격으로 토픽 내부의 특정 이벤트 감지
- 특정 필드를 이용한 파이프라인 조인

위와 같은 기능을 구현할 수 있도록 Streams API는 filter(), map(), groupBy() 등 다양한 처리, 집계 함수를 제공하고 있음. 특히 집계 함수의 경우 메시지 키 별, 시간 간격 별 등으로 집계할 수 있으며, 집계 상태 정보를 저장하는 상태 저장소(State Store)를 사용할 수 있음.

## Kafka Streams의 구성

![Kafka Streams의 구성](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F8LbaE%2FbtqEnHHVSWu%2FlHjGQAX7OwUk2Qko28iG0k%2Fimg.png)

카프카 스트림즈는 Streams API로 구축된 애플리케이션으로, 브로커와 별도로 구성됨, 그리고 라이브러리로 제공되므로 단순히 main 함수 내에서도 구현 가능하며, 특정 프레임워크에 종속되지 않음.

카프카 스트림즈 애플리케이션은 확장에 유연하고, 장애 수용성(fault-tolerant)을 가지며, 분산 형태로 구성할 수 있음. 스트림 애플리케이션이 이렇게 안정적이고 확장적인 구성을 할 수 있는 이유는 내부적으로 Consumer API를 사용하기 때문입니다. Consumer는 consumer group 기능을 통해 자동적으로 토픽 파티션 소유권을 관
리함. 스트림즈 애플리케이션 또한 인스턴스의 증감에 따라 토픽 파티션에 대한 소유권을 자동적으로 관리하며, 그렇기 때문에 효과적이고 안정적인 파이프라인을 구성할 수 있음.

![컨슈머 그룹](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbwgmMe%2FbtqEoABAnWq%2FkkInIUvrGMrndHSZKxvPrk%2Fimg.png)

## Kafka Streams의 구현

카프카 스트림즈는 Streams API 라이브러리. 그렇기 때문에 프레임워크에 종속되지 않고, 라이브러리 의존성을 추가하여 몇 가지 설정만 해주면 알아서 파이프라인이 구성됨.

카프카 스트림즈는 Streams API를 이용해서 메시지 파이프라인 로직(토폴로지)을 구현함. 구현 방법에는 Stream DSL과 Processor API 2가지 방식이 존재하는데, Stream DSL이 Processor API 보다 비교적 추상적이며 사용하기 쉬우므로 아래에선 Stream DSL만 다룸.

```
@Configuration
@EnableKafka
@EnableKafkaStreams
public static class KafkaStreamsConfig {

    ...

    @Bean
    public KStream<Integer, String> kStream(StreamsBuilder kStreamBuilder) {
        KStream<Integer, String> stream = kStreamBuilder.stream("streamingTopic1"); // kStreams 메소드는 StreamBuilder 객체를 이용하여 메시지 파이프라인(스트림)을 구성하여 반환

        // streamingTopic1 토픽에 저장된 데이터들을 기점으로 스트림을 구성.
        stream
            .mapValues(String::toUpperCase)
            .groupByKey()
            .reduce((String value1, String value2) -> value1 + value2,
                    TimeWindows.of(1000), "windowStore")
            .toStream()
            .map((windowedId, value) -> new KeyValue<>(windowedId.key(), value))
            .filter((i, s) -> s.length() > 40)
            .to("streamingTopic2");  // 스트림의 결과를 StreaminigTopic2 토픽으로 흘려보냅니다.

        stream.print();

        return stream;
    }
}
```

위와 같이 스트림은 브로커의 특정 토픽을 Sub 하여 일련의 로직을 처리한 뒤, 다시 다른 토픽으로 Pub 합니다. 즉, 카프카 내부 토픽을 기준으로 메시지 파이프라인을 구성함. 이는 일반적으로 Pub/Sub하는 모습과 다른 모습. 이처럼 카프카 스트림즈는 카프카 내부 파이프라인을 더욱 쉽게 구성할 수 있도록 하는 API 라이브러리임.

![Kafka Streams](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fo0bae%2Fbtq7o2YZcfu%2F8kw3w5ILPa2KVjCJLp2r7K%2Fimg.png)

> 출처
>
> - https://always-kimkim.tistory.com/entry/kafka101-streams
> - https://marrrang.tistory.com/63
