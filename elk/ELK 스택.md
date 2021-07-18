# ELK 스택
![ELK 스택](https://media.vlpt.us/images/eunoia/post/a9b8da0a-cbf1-4efc-9a70-9125cddc20d2/image.png)

ELK는 데이터 수집 및 분석 툴로 로그분석이나 데이터분석에 쓰이는 오픈소스 프로젝트의 약자. 각각 검색 및 분석엔진 ElasticSearch, 수집 로그 가공 Logstash, 데이터 시각화 Kibana의 약자이고 데이터 수집의 Filebeat가 추가되면서 ELK Stack이라고 부름.

![ELK 스택2](https://media.vlpt.us/images/eunoia/post/34667f04-d001-4de6-b22b-6eea0a18548c/image.png)

## Elastic Search

Elasticsearch는 텍스트, 숫자, 위치 기반 정보, 정형 및 비정형 데이터 등 모든 유형의 데이터를 위한 Apache Lucene( 아파치 루씬 ) 기반의 Java 오픈소스 분산 검색 엔진

루씬 라이브러리를 단독으로 사용할 수 있게 되었으며, 방대한 양의 데이터를 신속하게, 거의 실시간( NRT, Near Real Time )으로 저장, 검색, 분석할 수 있음.

> 아파치 루씬: 루씬은 검색 어플리케이션을 만드는데 사용하는 검색 라이브러리

### Elasticsearch와 관계형 DB 비교
아래 이미지와 같이 Elastic Search는 흔히 사용되는 관계형 DB와 비슷하게 대응될 수 있음.

![relational database, elastic search](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F998444375C98CC021F2221)

![relational database, elastic search2](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile22.uf.tistory.com%2Fimage%2F99E0A9425C98CF7A0A1B94)

### Elasticsearch 아키텍쳐 / 용어 정리

![Elasticsearch 아키텍쳐 / 용어 정리](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile27.uf.tistory.com%2Fimage%2F99A97A355C98D42D2E5196)

1. 클러스터(cluseter)
    - 클러스터는 Elasticsearch에서 가장 큰 시스템 단위를 의미하며, 최소 하나 이상의 노드로 이루어진 노드들의 집합.
    - 서로 다른 클러스터는 데이터의 접근, 교환을 할 수 없는 독립적인 시스템으로 유지됨.
    - 여러 대의 서버가 하나의 클러스터를 구성할 수 있고, 한 서버에 여러 개의 클러스터가 존재할수도 있음.
2. 노드(node)
    - Elasticsearch를 구성하는 하나의 단위 프로세스
    - 역할에 따라 Master-eligible, Data, Ingest, Tribe 노드로 구분할 수 있음.
3. 인덱스(index) / 샤드(Shard) / 복제(Replica)
    - Elasticsearch에서 index는 RDBMS에서 database와 대응하는 개념
    - 샤딩(sharding)은 데이터를 분산해서 저장하는 방법을 의미하며, 스케일 아웃을 위해 index를 여러 shard로 쪼갠 것
    - replica는 shard의 또다른 한 형태로 노드를 손실했을 경우를 대비, 데이터의 신뢰성을 위해 샤드들을 복제하는 것
4. Elasticsearch 특징
    - Scale out: 샤드를 통해 규모가 수평적으로 늘어날 수 있음
    - 고가용성: Replica를 통해 데이터의 안정성을 보장
    - Schema Free: Json 문서를 통해 데이터 검색을 수행하므로 스키마 개념이 없음
    - Restful: 데이터 CURD 작업은 HTTP Restful API를 통해 수행됨.

## Logstash

Logstash는 원래 Elasticsearch와 별개로 다양한 데이터 수집과 저장을 위해 개발된 프로젝트였음. 데이터의 색인, 검색 기능만을 제공하던 Elasticsearch는 데이터 수집을 위한 도구가 필요했는데, 때마침 Logstash가 출력 API로 Elasticsearch를 지원하기 시작하면서 많은 곳에서 Elasticsearch의 입력 수단으로 Logstash를 사용하기 시작

Logstash는 JRuby로 되어 있습니다. 루비 코드로 개발되어 자바의 런타임 머신 위에서 동작.

Logstash는 데이터 처리를 위해서 크게 다음과 같은 과정들을 거침.

> 입력(Inputs) -> 필터(Filters) -> 출력(Outputs)

1. 입력 기능에서 다양한 데이터 저장소로부터 데이터를 입력받음.
2. 필터 기능을 통해 데이터를 확장, 변경, 필터링 및 삭제 등의 처리를 통해 가공
3. 출력 기능을 통해 다양한 데이터 저장소로 데이터를 전송

## kibana
Kibana는 Elasticsearch를 가장 쉽게 시각화 할 수 있는 도구. 검색, aggregation의 집계 기능을 이용해 Elasticsearch로 부터 문서, 집계 결과 등을 불러와 웹 도구로 시각화함. Discover, Visualize, Dashboard 3개의 기본 메뉴와 다양한 App 들로 구성되어 있고, 플러그인을 통해 App의 설치가 가능.

### Discover
- Discover는 Elasticsearch에 색인된 소스 데이터들의 검색을 위한 메뉴. 
- 검색 창에 질의문을 통해 데이터를 간편하게 검색, 필터링 할 수 있으며, 검색된 데이터의 원본 문서를 확인하거나 보고 싶은 필드만 선택해서 테이블 형태로 조회가 가능.
- 시계열(time series) 기반의 로그 데이터인 경우 시간 히스토그램 그래프를 통해 시간대별 로그 수도 표시됨.

![Discover](https://gblobscdn.gitbook.com/assets%2F-Ln04DaYZaDjdiR_ZsKo%2F-LnUs7k4juqOieCcJjHy%2F-Ln0a0NvU10Mb6Rbk-Hs%2Fimage.png?alt=media&token=9187d728-493b-470e-9156-4ed3b9b9f420)

### Visualize
- aggregation 집계 기능을 통해 조회된 데이터의 통계를 다양한 차트로 표현할 수 있는 패널을 만드는 메뉴
- 영역차트, 바차트, 파이차트, 라인차트 등 다양한 시각화 도구들의 사용이 가능하며 여기서 만들어진 패널들을 조합해서 대시보드를 만듦

![Visualize](https://gblobscdn.gitbook.com/assets%2F-Ln04DaYZaDjdiR_ZsKo%2F-LnUs7k4juqOieCcJjHy%2F-Ln0aPn0YSjTxnokTxKc%2Fimage.png?alt=media&token=152479dd-2141-48d5-a70c-aea3db3ac764)

![Visualize2](https://gblobscdn.gitbook.com/assets%2F-Ln04DaYZaDjdiR_ZsKo%2F-LnUs7k4juqOieCcJjHy%2F-Ln0alkzB9B9OgG9Thby%2Fimage.png?alt=media&token=6fdb04bd-f371-4af8-ba22-6f91c3691845)

### Dashboard
- Visualize 메뉴에서 만들어진 시각화 도구들을 조합해서 대시보드 화면을 만들고 저장, 불러오기 등을 할 수 있는 메뉴
- 검색 창에 쿼리를 입력하거나 시각화 도구들을 클릭해서 조회할 데이터들의 필터링이 가능하고, URL로 대시보드를 다른 사람들과 공유하거나 json 형식으로 내보내고 불러오기 등이 가능

![Dashboard](https://gblobscdn.gitbook.com/assets%2F-Ln04DaYZaDjdiR_ZsKo%2F-LnUs7k4juqOieCcJjHy%2F-Ln0bNhB9DKRNGdbC8a9%2Fimage.png?alt=media&token=a7dda686-ee1f-4455-97f8-ecbe222bf68a)

## Beats

Elasticsearch 클러스터로의 대용량 데이터 전송은 보통 하나의 소스가 아닌 다양한 시스템들로부터 수집을 하였기에 그 모든 단말 시스템에 Logstash를 설치하는 것은 부담됨.

단말 시스템으로 부터 데이터를 수집하고 필터기능 없이 가볍게 Elasticsearch 또는 Logstash로 데이터를 전송하는 포워더 성격의 원격 수집기가 바로 Beats

Beats는 구글에서 개발된 Go 언어로 개발됨.

### Libbeat
PacketBeat 프로그램에서 파생됨. 특정한 데이터를 수집하는 부분만 코딩 하고 나면 데이터를 JSON 문서로 변환하고, 데이터가 유실되지 않게 관리하고, Elasticsearch로 전송하는 역할을 담당

### Packetbeat
설치된 시스템에 유통되는 패킷들을 스니핑 해서 Elasticsearch에 적재.

### Filebeat
파일의 내용을 수집하는 기능. Web log 또는 machine log 등이 저장되는 파일 경로를 지정하기만 하면 Filebeat은 해당 경로에 적재되는 파일을 읽어들이며 새로운 내용이 추가될 때 마다 그 내용을 Elasticsearch로 색인함.

### Metricbeat
시스템에서 실행중인 프로세스들의 정보와 이 프로세스들이 소모중인 CPU, Memory 등에 대한 상태들을 수집해서 Elasticsearch에 적재하고 모니터링 할 수 있는 시스템.

### Winlogbeat
Microsoft Windows 기반 시스템에서 시스템에 적재되는 Windows event 들을 수집해 Elasticsearch로 색인하여 모니터링 할 수 있는 시스템.

### Auditbeat
리눅스 시스템의 사용자 접속과 실행 이벤트 로그들과 같은 감사 데이터를 수집.

### Heartbeat
다른 프로세스들의 가동 시간 등을 모니터링. ICMP, TCP, HTTP 프로토콜 등을 통해 Ping 명령으로 원격의 프로세스의 가동 여부를 확인, 다양한 시스템을 동시에 모니터링 할 때 매우 유용.

### Functionbeat
마이크로 서비스 아키텍쳐(MSA)와 같은 FaaS 클라우드 기반의 시스템에서 서버리스 프레임워크를 이용하여 클라우드 인프라를 모니터링. 