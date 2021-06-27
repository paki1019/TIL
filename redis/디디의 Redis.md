# Redis 개요
- Redis는 Remote Dictionary Server의 약자로 외부 서버에 dictionary 자료구조를 사용하는 서버를 의미
- Database, Cache, Message broker 역할을 할 수 있음
- In-memory Data Structure Store로 메모리 상에 데이터를 저장하는 서버
- 다양한 자료구조를 제공

# Cache 개념
- 나중의 요청에 대한 결과를 미리 저장했다가 빠르게 사용하는 것
- 메모리 구조상 Storage 영역보다 Main Memory 영역이 빠른 대신 용량이 적음
- Database(Storage)보다 더 빠른 Memory에 더 자주 접근하고 덜 자주 바뀌는 데이터를 저장하는 것이 바로 Redis(In-memory Database)

# Redis 자료구조
- Collection 자료구조를 제공
- String, List, Set, Sorted Set, Hash와 같은 자료구조 제공
- Race Condition: 여러 개의 Thread가 경합하는 것, Context Switching에 다라 원하지 않는 결과가 발생함.
  - Redis는 기본적으로 Single Threaded이며 Atomic Critical Section에 대한 동기화를 제공하고 서로 다른 Transaction Read/Write를 동기화 시킴
- 이러한 강점을 기반하여 여러 서버에서 같은 데이터를 공유할 때, Atomic 자료구조와 Cache를 사용하는 경우 Redis가 적절함.

# Redis 주의사항
- Single Thread 서버 이므로 시간 복잡도를 고려해야 함.
  - Event Driven(비동기)
  - IO-based Process
  - Context Switching의 효율이 적음

- In-memory 특성상 메모리 파편화, 가상 메모리등의 이해가 필요
  - 메모리 파편화 때문에 실제 사용하는 메모리보다 더 많은 메모리를 사용하는 것처럼 인식 될수 있음
  - 가상 메모리의 Swapping 때문에 오버 헤드가 발생 할 수 있음.
  - 프로세스 Replication-Fork 과정에 메모리가 가득 차 있다면 복사에 실패하고 서버가 죽을 수 있음.

> ### 출처
> - https://www.youtube.com/watch?v=Gimv7hroM8A&ab_channel=%EC%9A%B0%EC%95%84%ED%95%9CTech