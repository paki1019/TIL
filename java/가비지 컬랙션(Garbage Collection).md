# 가비지 컬랙션(Garbage Collection)

프로그램을 개발 하다 보면 유효하지 않은 메모리인 가바지(Garbage)가 발생하게 됨. Java나 Kotlin에서는 JVM의 가비지 컬렉터(Garbage Collector, GC)가 불필요한 메모리를 알아서 정리해주는데 이를 가비지 컬랙션이라고 함.

```
Person person = new Person();
person.setName("Mang");
person = null; // 가비지 발생, 가비지 컬랙터에 의해 정리됨.

person = new Person();
person.setName("MangKyu");
```

## Minor GC와 Major GC

JVM의 Heap영역은 다음의 2가지를 전제(Weak Generational Hypothesis)로 설계됨.

- 대부분의 객체는 금방 접근 불가능한 상태(Unreachable)가 된다.
- 오래된 객체에서 새로운 객체로의 참조는 아주 적게 존재한다.

즉, 객체는 대부분 일회성되며, 메모리에 오랫동안 남아있는 경우는 드물다는 것. 그렇기 때문에 객체의 생존 기간에 따라 물리적인 Heap 영역을 나누게 되었는데, 이에 따라 Young, Old 총 2가지 영역으로 설계되었다.

![Minor GC와 Major GC](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fva8qQ%2FbtqUSpSocbS%2FkxTvtnmrdhf4bnVPXth0UK%2Fimg.png)

### Young 영역(Young Generation)

- 새롭게 생성된 객체가 할당(Allocation)되는 영역
- 대부분의 객체가 금방 Unreachable 상태가 되기 때문에, 많은 객체가 Young 영역에 생성되었다가 사라짐.
- Young 영역에 대한 가비지 컬렉션(Garbage Collection)을 Minor GC라고 부름.

### Old 영역(Old Generation)

- Young영역에서 Reachable 상태를 유지하여 살아남은 객체가 복사되는 영역.
- 복사되는 과정에서 대부분 Young 영역보다 크게 할당되며, 크기가 큰 만큼 가비지는 적게 발생.
- Old 영역에 대한 가비지 컬렉션(Garbage Collection)을 Major GC 또는 Full GC라고 부름.

Old 영역에 있는 객체가 Young 영역의 객체를 참조하는 경우도 드물게 존재하는데, 이러한 경우를 대비하여 Old 영역에는 512 bytes의 덩어리(Chunk)로 되어 있는 카드 테이블(Card Table)이 존재.

카드 테이블에는 Old 영역에 있는 객체가 Young 영역의 객체를 참조할 때 마다 그에 대한 정보가 표시됨. Young 영역에서 가비지 컬렉션(Minor GC)가 실행될 때 모든 Old 영역에 존재하는 객체를 검사하여 참조되지 않는 Young 영역의 객체를 식별하는 것이 비효율적이기 때문에 Young 영역에서 가비지 컬렉션이 진행될 때는 카드 테이블만 조회하여 GC의 대상인지 식별함.

![Old 영역(Old Generation)](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FFOLU3%2FbtqUOBF35cJ%2FBMKuD1iqfq6R0lAqMlfkC0%2Fimg.png)

## 가비지 컬렉션의 동작 방식

### 1.Stop The World

GC가 실행될 때는 GC를 실행하는 쓰레드를 제외한 모든 쓰레드들의 작업이 중단되고, GC가 완료되면 작업이 재개됨.

### 2.Mark and Sweep

- Mark: 사용되는 메모리와 사용되지 않는 메모리를 식별하는 작업.
- Sweep: Mark 단계에서 사용되지 않음으로 식별된 메모리를 해제하는 작업.

## Minor GC의 동작 방식

Young 영역은 1개의 Eden 영역과 2개의 Survivor 영역, 총 3가지로 나뉘어진다.

- Eden 영역: 새로 생성된 객체가 할당(Allocation)되는 영역.
- Survivor 영역: 최소 1번의 GC 이상 살아남은 객체가 존재하는 영역.

1. 새로 생성된 객체는 Eden 영역에 할당됨.
2. 객체가 계속 생성되어 Eden 영역이 꽉차게 되고 Minor GC가 실행됨.
3. Eden 영역에서 사용되지 않는 객체의 메모리가 해제됨.
4. Eden 영역에서 살아남은 객체는 1개의 Survivor 영역으로 이동.
5. 1~2번의 과정이 반복되다가 Survivor 영역이 가득 차게 되면 Survivor 영역의 살아남은 객체를 다른 Survivor 영역으로 이동.(1개의 Survivor 영역은 반드시 빈 상태가 됨.)
6. 이러한 과정을 반복하여 계속해서 살아남은 객체는 Old 영역으로 이동(Promotion).

객체의 생존 횟수를 카운트하기 위해 Minor GC에서 객체가 살아남은 횟수를 의미하는 age를 Object Header에 기록, Minor GC 때 Object Header에 기록된 age를 보고 Promotion 여부를 결정.

Survivor 영역 중 1개는 반드시 사용이 되어야 함. 만약 두 Survivor 영역에 모두 데이터가 존재하거나, 모두 사용량이 0이라면 현재 시스템이 정상적인 상황이 아닌걸로 판단됨.

![Minor GC의 동작 방식](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FCyho2%2FbtqURvZRql6%2F4a7u6mMGofkpuURKQz0RT1%2Fimg.png)

HotSpot JVM에서는 Eden 영역에 객체를 빠르게 할당(Allocation)하기 위해 bump the pointer와 TLABs(Thread-Local Allocation Buffers)라는 기술을 사용함.

### bump the pointer

- Eden 영역에 마지막으로 할당된 객체의 주소를 캐싱해두는 것이다.
- bump the pointer를 통해 새로운 객체를 위해 유효한 메모리를 탐색할 필요 없이 마지막 주소의 다음 주소를 사용하게 함으로써 속도를 높일 수 있음.
- 새로운 객체를 할당할 때 객체의 크기가 Eden 영역에 적합한지만 판별하면 됨.

### TLABs(Thread-Local Allocation Buffers)

- 멀티쓰레드 환경이라면 객체를 Eden 영역에 할당할 때 락(Lock)을 걸어 동기화를 해주어야 함.
- TLABs(Thread-Local Allocation Buffers)는 각각의 쓰레드마다 Eden 영역에 객체를 할당하기 위한 주소를 부여함으로써 동기화 작업 없이 빠르게 메모리를 할당하도록 하는 기술.
- 각각의 쓰레드는 자신이 갖는 주소에만 객체를 할당함으로써 동기화 없이 bump the poitner를 통해 빠르게 객체를 할당.

## Major GC의 동작 방식

- Major GC는 객체들이 계속 Promotion되어 Old 영역의 메모리가 부족해지면 발생.
- Young 영역은 일반적으로 Old 영역보다 크키가 작기 때문에 GC가 보통 0.5초에서 1초 사이에 끝나지만, Old 영역은 Young 영역보다 크며 Young 영역을 참조하기도 하기 때문에 Minor GC보다 시간이 오래걸리며, 10배 이상의 시간을 사용함.

![Garbage Collection(가비지 컬렉션) 내용 요약](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdM4wqf%2FbtqUWs2lW8H%2FGvRECmsUIfZ2jhDoKhSCD0%2Fimg.png)

## 가비지 컬렉션 알고리즘

GC를 수행하기 위해 Stop The World에 의해 애플리케이션이 중지되는데, Heap의 사이즈가 커지면서 애플리케이션의 지연(Suspend) 현상이 두드러지게 됨, 이를 막기 위해 다양한 Garbage Collection(가비지 컬렉션) 알고리즘을 지원함.

### Serial GC

- Young 영역은 Mark Sweep 알고리즘이 수행됨.
- Old 영역은 Mark Sweep Compact 알고리즘이 수행됨.
  - 기존의 Mark Sweep에 Compact라는 작업이 추가
  - Compact는 Heap 영역을 정리하기 위한 단계로 유효한 객체들이 연속되게 쌓이도록 힙의 가장 앞 부분부터 채워서 객체가 존재하는 부분과 객체가 존재하지 않는 부분으로 나누는 것.
- Serial GC는 서버의 CPU 코어가 1개일 때 사용하기 위해 개발되었으며, 모든 가비지 컬렉션 일을 처리하기 위해 1개의 쓰레드만을 이용함. CPU의 코어가 여러 개인 운영 서버에서 Serial GC를 사용하는 것은 부적절.

```
java -XX:+UseSerialGC -jar Application.java
```

### Parallel GC

- Throughput GC로도 알려져 있음.
- 기본적인 처리 과정은 Serial GC와 동일, 그러나 Parallel GC는 여러 개의 쓰레드를 통해 Parallel하게 GC를 수행함으로써 GC의 오버헤드를 상당히 줄여줌.
- 옵션을 통해 애플리케이션의 최대 지연 시간 또는 GC를 수행할 쓰레드의 갯수 등을 설정해줄 수 있음.
- Java8까지 기본 가비지 컬렉터(Default Garbage Collector)로 사용됨.

```
java -XX:+UseParallelGC -jar Application.java

// 사용할 쓰레드의 갯수
-XX:ParallelGCThreads=<N>

// 최대 지연 시간
-XX:MaxGCPauseMillis=<N>
```

### CMS(Concurrent Mark Sweep) GC

- CMS(Concurrent Mark Sweep) GC는 Parallel GC와 마찬가지로 여러 개의 쓰레드를 이용.
- 기존의 Serial GC나 Parallel GC와는 다르게 Mark Sweep 알고리즘을 Concurrent하게 수행.

![CMS(Concurrent Mark Sweep) GC](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FexiT4S%2FbtqURuUWjwY%2FWsh133bXvARXfGMunpvSh1%2Fimg.png)

- CMS GC가 수행될 때에는 자원이 GC를 위해서도 사용되므로 응답이 느려질 순 있지만 응답이 멈추지는 않음.
- 하지만 이러한 CMS GC는 다른 GC 방식보다 메모리와 CPU를 더 많이 필요로 하며, Compaction 단계를 수행하지 않는다는 단점이 존재.
- 이 때문에 시스템이 장기적으로 운영되다가 조각난 메모리들이 많아 Compaction 단계가 수행되면 오히려 Stop The World 시간이 길어지는 문제가 발생할 수 있음.
- CMS GC는 Java9 버젼부터 deprecated 되었고 결국 Java14에서는 drop됨.

```
// deprecated in java9 and finally dropped in java14
java -XX:+UseConcMarkSweepGC -jar Application.java
```

### G1(Garbage First) GC

- 장기적으로 많은 문제를 일으킬 수 있는 CMS GC를 대체하기 위해 개발 됨.
- Heap 영역을 물리적으로 Young 영역, Old 영역으로 나누어쓰는 기존의 GC와는 달리 1 GC는 Eden 영역에 할당하고, Survivor로 카피하는 등의 과정을 사용하지만 물리적으로 메모리 공간을 나누지 않음.
- 대신 Region(지역)이라는 개념을 새로 도입하여 Heap을 균등하게 여러 개의 지역으로 나누고, 각 지역을 역할과 함께 논리적으로 구분하여(Eden 지역인지, Survivor 지역인지, Old 지역인지) 객체를 할당함.

![G1(Garbage First) GC](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdHxPiT%2FbtqU0xWGaDI%2FwriFcFKPHND5pTAsyn47X1%2Fimg.png)

- Eden, Survivor, Old 역할에 더해 Humongous와 Availabe/Unused라는 2가지 역할을 추가함.
  - Humonguous: Region 크기의 50%를 초과하는 객체를 저장하는 Region.
  - Availabe/Unused는 사용되지 않은 Region을 의미.
- G1 GC의 핵심은 Heap을 동일한 크기의 Region으로 나누고, 가비지가 많은 Region에 대해 우선적으로 GC를 수행하는 것.
  - 한 지역에 객체를 할당하다가 해당 지역이 꽉 차면 다른 지역에 객체를 할당하고, Minor GC가 실행 됨.
  - Eden 지역에서 GC가 수행되면 살아남은 객체를 식별(Mark)하고, 메모리를 회수(Sweep)함. 그리고 살아남은 객체를 다른 지역으로 이동시킴. 복제되는 지역이 Available/Unused 지역이면 해당 지역은 이제 Survivor 영역이 되고, Eden 영역은 Available/Unused 지역이 됨.
  - 시스템이 계속 운영되다가 객체가 너무 많아 빠르게 메모리를 회수 할 수 없을 때는 Major GC(Full GC)가 실행 됨. 기존의 다른 GC 알고리즘은 모든 Heap의 영역에서 GC를 수행하지만, G1 GC는 어느 영역에 가비지가 많은지를 알고 있기 때문에 GC를 수행할 지역을 조합하여 해당 지역에 대해서만 GC를 수행함.
- G1 GC는 다른 GC 방식에 비해 잦게 호출되는 대신, 작은 규모의 메모리 정리 작업을 Concurrent하게 수행되기 때문이 지연이 크지 않고 훨씬 효율적.
- Java9부터 기본 가비지 컬렉터(Default Garbage Collector)로 사용됨.

```
java -XX:+UseG1GC -jar Application.java
```

> 출처
>
> - https://mangkyu.tistory.com/119
