# Arcus

- Arcus는 memcached와 ZooKeeper를 기반 네이버 서비스의 요구사항을 반형해 개발한 메모리 캐시 클라우드.
- Memcached와 마찬가지로 데이터베이스 부하를 완화 및 동적 웹 응용 프로그램의 속도기 위해 사용.
- 데이터베이스 호출, API 호출 또는 페이지 렌더링 결과에서 임의의 작은 데이터 청크(문자열, 객체)를 위한 메모리 내 키-값 저장소

- memcached 프로토콜을 지원하고 다음의 memcached 기본 성능 혜택은 그대로 유지.
  - 백엔드 저장소인 데이터베이스의 앞단에 위치, hot-spot 성격의 데이터를 캐싱, 서비스 응용에게 빠른 응답성 제공하고 데이터베이스 부하 감소.
  - 복잡한 계산에 의한 결과물 또는 웹 처리상의 중간 데이터 등을 신속히 저장, 조회.
  - 캐시를 통하여 여러 프로세스들 간에 데이터 공유.
- memcached를 확장해서 다음의 추가 기능을 제공.
  - ZooKeeper 기반의 cache cloud 관리.
  - Collection 자료구조 (List, Set, B+tree) 지원
  - B+tree의 다양한 기능들 제공
    - 다양한 크기의 B+tree key (bkey)
    - Element flag 및 filtering
    - Bkey 기반의 range scan
    - 여러 B+tree들에 대한 sort-merge-get (smget)
    - B+tree position 기반의 range scan
  - Item attibute 조회 및 설정 기능.
  - Sticky item(not evicted & oot expired) 지원.
  - Collection 자료구조를 위한 small memory allocator.

## 출처

> - https://naver.github.io/arcus/
> - https://github.com/naver/arcus
