# Kafka Replication

## Kafka Replication factor

kafka에서는 replication factor 수를 조정하여, replication의 수를 몇 개로 할지 관리자가 조정할 수 있음.

replication 수가 많을수록 broker 장애 발생 시 topic에 저장된 데이터 안전성이 보장되기 때문에 중요 데이터의 경우 replication factor 수를 3으로 사용하는 것를 권장

topic01, topic02가 모두 replication factor가 1인 경우. 아래 그림과 같이 토픽이 할당됨.

![1](http://www.popit.kr/wp-content/uploads/2017/07/replication.001-600x450.jpeg)


여기서 topic01에 대해서 replication factor를 2로 변경할 시 

![2](http://www.popit.kr/wp-content/uploads/2017/07/replication.002-600x450.jpeg)

위의 그림처럼 topic01이 2개 브로커에 할당 됨.

이어서 topic02를 3으로 변경하면

![3](http://www.popit.kr/wp-content/uploads/2017/07/replication.003-600x450.jpeg)

위 그림처럼 topic02가 3개 브로커에 할당됨.

## leader & follower

rabbitmq의 경우 복제본이 2개 있는 경우 하나는 master, 나머지는 mirrored라고 표현함.

이러한 용어는 애플리케이션들마다 조금씩 다르게 표현하고 있는데, kafka에서는 이를 leader와 follower라고 표현함.

![leader & follower](http://www.popit.kr/wp-content/uploads/2017/07/replication.004-600x450.jpeg)

topic으로 통하는 모든 데이터의 read/write는 오직 leader와 이루어짐. 만약 leader가 down 된다면, follwer중의 하나가 leader가 됨.

## ISR(In Sync Replica)

![ISR detail](http://www.popit.kr/wp-content/uploads/2017/07/replication.012-600x450.jpeg)

ISR은 일종의 replication group이라 볼 수 있음.

topic이 생성되고 옵션으로 replication factor를 설정하면 replication factor 수에 맞추어 위와 같이 ISR이 구성되게 됨. ISR이 구성되면 leader와 follower는 아래 각자의 역할을 수행함.

- leader: follower 중에서 자신보다 일정기간 동안 뒤쳐지면 leader가 될 자격이 없다고 판단하여 뒤쳐지는 follower를 ISR에서 제외
- follower : leader와 동일한 데이터 내용을 유지하기 위해서 짧은 주기로 leader로부터 데이터를 가져옴.

![ISR](http://www.popit.kr/wp-content/uploads/2017/07/replication.006-600x450.jpeg)

ISR에는 하나의 leader와 n개의 follower이 존재함. ISR내의 모든 follower들은 누구라도 leader가 될 수 있으며, leader가 down 되거나 leader가 있는 broker가 down 되었을 경우 follower 중 하나가 new leader가 됨.

![new leader1](http://www.popit.kr/wp-content/uploads/2017/07/replication.007-600x450.jpeg)

위 그림처럼 만약 broker1이 down 되는 경우,

![new leader2](http://www.popit.kr/wp-content/uploads/2017/07/replication.008-600x450.jpeg)

broker2의 topic02 - follower는 new leader로 변하게 됨.

추가적으로 broker2가 down 되는 경우

![new leader3](http://www.popit.kr/wp-content/uploads/2017/07/replication.009-600x450.jpeg)

topic01의 경우 ISR 내 더 이상 남아 있는 follower가 없기 때문에 leader를 넘겨줄 수 없는 상황이 되었고, topic01는 leader가 없기 때문에 더 이상 read/write를 할 수 없음. 즉 topic01의 경우 장애상황이 됨.

## ALL down

모든 broker가 down 되는 시나리오

![ALL down1](http://www.popit.kr/wp-content/uploads/2017/07/replication.013-600x450.jpeg)

첫 번째 그림은 보내는 사람이 파란색 메시지를 보냈고, leader와 두 follower들에게 모두 전달되었음. 하지만 파란색 메시지가 전달되자마자 follower를 가진 broker3이 down 됨.

![ALL down2](http://www.popit.kr/wp-content/uploads/2017/07/replication.014-600x450.jpeg)

두 번째 그림은 보내는 사람이 연두색 메시지를 보냈고, leader와 하나의 follower에게 모두 전달되었음. 마찬가지로 연두색 메시지가 전달되자마자 follower를 가진 broker2가 down 됨.

![ALL down3](http://www.popit.kr/wp-content/uploads/2017/07/replication.015-600x450.jpeg)

세 번째 그림은 보내는 사람이 핑크색 메시지를 보냈고, leader에게만 전달되었음. 핑크색 메시지를 받자마자 마지막 broker1 마저 down 되면서, 결국 모든 broker가 down 상태가 되버림.

이러한 상황이 발생했을때, 대응할 수 있는 방법은 두 가지가 있음.

1. 마지막까지 leader였던 broker1번이 up이 되고 다시 leader가 될 때까지 기다람.(모든 데이터를 가지고 있을 가능성이 높음)
2. ISR과 상관없이 누구라도 가장 빨리 up이 되는 topic이 leader가 됨.(데이터 손실이 되더라도 장애 대응이 빠름)

1번 방법으로 대응할 경우, 모든 broker가 down 되는 장애 상황이라 할지라도 데이터 손실 가능성이 적기 때문에 좋은 대응방법일 수 있음. 

하지만 여기에는 마지막 leader인 broker 1번이 반드시 up이 되어야 하고, 또 가장 먼저 up이 되어야 한다는 조건이 있음.

만약 broker1이 기계적인 결함 등의 이유로 up이 안되거나 up이 되는데 오랜 시간이 필요한 경우 장애 상황인 상태로 막연하게 기다려야 하며, 오히려 해당 시간 동안 장애 시간이 길어지게 됨

2번 방법의 경우 비록 일부분의 데이터 손실이 발생할 수 있지만, 빠르게 장애 대응이 가능함.

![2-1](http://www.popit.kr/wp-content/uploads/2017/07/replication.015-600x450.jpeg)
> broker 1, 2, 3이 모두 다운됨

![2-2](http://www.popit.kr/wp-content/uploads/2017/07/replication.016-600x450.jpeg)
> broker 3이 복구되면서 leader 브로커가 됨

![2-3](http://www.popit.kr/wp-content/uploads/2017/07/replication.017-600x450.jpeg)
> broker 1, 2도 복구 되면서 새로운 메시지를 받음, 기존에 있던 초록색, 분홍색 메시지는 손절.

> 출처
>
> - https://www.popit.kr/kafka-%EC%9A%B4%EC%98%81%EC%9E%90%EA%B0%80-%EB%A7%90%ED%95%98%EB%8A%94-topic-replication/