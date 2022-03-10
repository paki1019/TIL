# CQRS 패턴과 오해

CQRS는 Command Query Responsibility Segregation 의 약자로 단어 그대로 해석하면 `명령 조회 책임 분리`. 애플리케이션을 구현함에 있어 명령과 조회에 대한 책임을 분리하는 것. CQRS에서 명령은 시스템의 상태를 변경하는 작업을 의미하며, 조회는 시스템의 상태를 반환하는 작업을 의미.

.... 끝. 사실 이것이 CQRS의 전부. 너무 단순하다고 생각할 수 있지만 이 단순한 규칙이 전부가 맞다. 사람들은 CQRS에 대해 많은 오해를 하곤 하는데 실제 CQRS는 규칙 자체는 위의 내용이 전부로 매우 단순하다. 다만 이 단순한 규칙을 몇 가지 응용기술을 조합하여 시스템에 적용하면서 다양한 모습의 구현체를 창출하게 되기 떄문에 우리는 CQRS가 어렵다는 오해를 하게 되는것.

핵심은 데이터 모델을 분리하는 것. 이 분리된 데이터 모델은 동일 어플리케이션 - 동일 DB를 바라보는 것일 수도 있으며, 동일 DB - 밸겨의 테이블 일 수도 있고, 또 메시지 큐를 두어 이벤트 소싱으로 QueryDB를 동기화 시키는 등 여러 가지 형태가 가능함.

여러 구현 예시는 아래의 블로그 및 유튜브들을 살펴보는것이 좋을것이다.

> 예시
>
> - https://justhackem.wordpress.com/2016/09/17/what-is-cqrs/
> - https://freedeveloper.tistory.com/399
> - https://brunch.co.kr/@springboot/423
> - https://www.youtube.com/watch?v=fg5xbs59Lro&t=369s&ab_channel=%EC%9A%B0%EC%95%84%ED%95%9CTech
> - https://www.youtube.com/watch?v=BnS6343GTkY

## CQRS의 장점/단점

### 장점

- 심플한 읽기 모델, 최적화된 데이터 스키마.
- 개발/운영에 유연한 NoSQL
- 읽기 (조회, Query) 성능 개선
- 독립적으로 확장 가능한 데이터베이스

### 단점

- 최종 데이터 일관성
- 트랙잭션 범위
- 시스템 복잡도 증가
- 메시징 패턴
