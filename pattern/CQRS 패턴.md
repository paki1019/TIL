# CQRS

CQRS는 Command Query Responsibility Segregation 의 약자로 단어 그대로 해석하면 `명령 조회 책임 분리`. 애플리케이션을 구현함에 있어 명령과 조회에 대한 책임을 분리하는 것.

## 기존 아키텍처

![기존 아키텍처](https://docs.microsoft.com/ko-kr/azure/architecture/patterns/_images/command-and-query-responsibility-segregation-cqrs-tradition-crud.png)

일반적인 애플리케이션은 쿼리하고 명령을 동일한 데이터 모델을 사용

간단한 애플리케이션에서는 이것이 적합하나 애플리케이션이 복잡해질수록 위 방법은 어려워짐.

- 애플리케이션의 읽기쪽에서 다른 쿼리를 실행할 수 있고, 모양이 다른 DTO 들을 반환하기 때문에 객체 매핑이 복잡해짐.
- 동일한 데이터 집합에서 작업을 병렬로 수행할 때 데이터 경합이 발생 할 수 있음.
- 데이터 저장소 및 데이터 액세스 계층에 대한 로드로 인해 성능에 부정적인 영향을 미칠 수 있으며, 정보를 검색하는데 필요한 쿼리가 복잡해짐
- 읽기 쓰기 작업의 대상인 엔티티가 잘못된 컨텍스트에서 데이터를 노출할 수도 있음.

## CQRS

![CQRS](https://docs.microsoft.com/ko-kr/azure/architecture/patterns/_images/command-and-query-responsibility-segregation-cqrs-basic.png)

CQRS는 명령(command, DB에 대한 업데이트)과 조회(query, DB에 대한 읽기)의 책임을 분리해서 성능, 확장성, 보안을 극대화 시킴

- 비즈니스 로직에 기반해서 명령을 작성.
- 비동기 처리(큐)로 명령을 처리할 수도 있음.
- 쿼리는 데이터베이스를 수정하지 않으며, 도메인 정보를 캡슐화하지 않은 DTO를 반환함.

![읽기 쓰기 분리](https://docs.microsoft.com/ko-kr/azure/architecture/patterns/_images/command-and-query-responsibility-segregation-cqrs-separate-stores.png)

더 높은 격리 수준을 위해 쓰기 데이터에서 읽기 데이터를 물리적으로 분리할 수도 있으며, 이 경우 데이터베이스는 두 모델 간의 통신 역할을 함

> 출처
>
> - https://always-kimkim.tistory.com/entry/cqrs-pattern
> - https://code-masterjung.tistory.com/80
