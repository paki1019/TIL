# CAP 정리
![CAP 정리](https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F121521254BCC1A2E24)
CAP 정리는 분산 컴퓨터 시스템에서는 위 세가지 조건을 모두 가지는 것이 불가능 함을 증명한 정리.

- 일관성(Consistency): 모든 노드가 같은 순간에 같은 데이터를 볼 수 있다.
- 가용성(Availability): 모든 요청이 성공 또는 실패 결과를 반환할 수 있다.
- 분할내성(Partition tolerance): 메시지 전달이 실패하거나 시스템 일부가 망가져도 시스템이 계속 동작할 수 있다.

## 일관성(Consistency)
- 일관성을 가진다는 것은 모든 데이터를 요청할 때 응답으로 가장 최신의 변경된 데이터를 리턴 또는 실패를 리턴한다는 것.
- 모든 읽기에 대해서 DB노드가 항상 동일한 데이터를 가지고 있어야한다는 의미.
> DB가 2개의 Instance를 유지하고 있다면 A에 요청하든 B에 요청하든 동일한 값을 반환해야 함.

## 가용성(Availability)
- 가용성은 모든 요청에 대해서 정상적인 응답을 하는 것.
- 클러스터의 노드 일부에서 장애가 발생하더라도 READ와 WRITE 등의 동작은 항상 성공적으로 리턴해야 함.

## 분할 내구성(Partition Tolerance)
- 분할 내구성이란 DB Node간의 통신 장애가 발생하더라도 동작해야하는 것.
> A와 B의 Instance간의 네트워크에 장애가 발생, 유저가 A DB에서 쿼리를 했을 때 A 자체만으로 동작 가능.
