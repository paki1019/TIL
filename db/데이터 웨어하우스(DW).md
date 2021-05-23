# 데이터 웨어하우스(DW)
데이터 웨어하우스는 사용자의 의사 결정에 도움을 주기 위하여 분석 가능한 형태로 정보들이 저장되어 있는 중앙 저장소. 최근에 빅데이터 시대가 되면서 주목을 받게 된 솔루션

데이터는 트랜잭션 시스템, 관계형 데이터베이스(RDMS) 및 기타 소스로부터 보통 정기적으로 데이터 웨어하우스로 들어감. 비즈니스 애널리스트, 데이터 엔지니어, 데이터 사이언티스트들은 비즈니스 인텔리전스(BI) 도구, SQL 클라이언트 및 기타 분석 응용 프로그램을 통해 데이터에 액세스함.

데이터웨어 하우스(DW)는 기존 정보를 활용해 더 나은 정보를 제공하고, 데이터의 품질을 향상시키며, 조직의 변화를 지원하고 비용과 자원관리의 효율성을 향상시키는 것이 목적
![데이터 웨어하우스(DW)](https://1.bp.blogspot.com/-i6AUByv5TzU/X0tedTuZq-I/AAAAAAAArXk/qtDh8e7khwwRplvKxttaFJzBTD-5JBsugCPcBGAsYHg/s1600/Data_warehouse_architecture.jpg)

# 데이터 웨어하우스의 4가지 특성
## 주체지향(Subject Oriented)
기존의 데이터베이스가 대출, 예금, 재고관리 등과 같은 '기능'이나 '업무' 처리를 중심으로 설계되는 것에 비해 데이터웨어 하우스(DW)는 고객, 거래처, 공급자, 상품 등과 같은 '주제' 중심으로 구성됨.

## 통합(Integrated)
기존의 운영시스템은 부서나 부문, 혹은 기관별로 일관성 없는 다량의 데이터를 중복 관리하지만, 데이터 웨어하우스(DW)는 데이터 속성의 이름, 코드의 구조, 도량형 단위 등의 일관성을 유지하며 전사적 관점에서 하나로 통합함.

## 시계열(Time Variant)
기존의 데이터베이스는 사용자가 사용하는 현재 시간을 기준으로 최신의 값을 유지하지만, 데이터웨어 하우스(DW)는 일정 기간 수집된 데이터를 갱신 없이 보관하며 일, 월, 분기, 년 등과 같은 기간 관련 정보를 함께 저장함.

시계열성은 어떤 자료가 시간에 따라 변경되어야 하는 것이 아니고, 시간에 따른 변경을 항상 반영하고 있어야 함을 의미

## 비휘발성(Nonvolatile)
기존의 데이터베이스에서는 추가나 삭제, 변경 등과 같은 갱신 작업이 레코드 단위로 지속적으로 발생하지만, 데이터 웨어하우스(DW) 내의 데이터는 일단 적재(loading)가 완료되면 읽기 전용 형태의 스냅 샷 데이터로 존재함.

# 데이터 웨어하우징(Data warehousing)
데이터 웨어하우징(data warehousing)이란 데이터의 수집 및 처리에서 도출되는 정보의 활용에 이르는 일련의 프로세스
![데이터 웨어하우징(Data warehousing)](https://1.bp.blogspot.com/-y82CuMpJask/X0te6E5UQdI/AAAAAAAArXs/MOTN5LIZ9EsJYSkJ6qmZp92c5xMo-PukgCPcBGAsYHg/s640/Data%2BWarehousing.jpg)

# ETL(Extract, Transform, Load)
ETL은 데이터 웨어하우스 구축 시 데이터를 운영 시스템에서 추출하여 가공(변환, 정제)한 후 데이터 웨어하우스(DW)에 적재하는 모든 과정. 일반적으로 발생하는 데이터 변환에는 필터링, 정렬, 집계, 데이터 조인, 데이터 정리, 중복 제거 및 데이터 유효성 검사 등의 다양한 작업이 포함됨.

- Extract: 하나 또는 그 이상의 데이터 원천들로 부터 데이터 획득
- Transform: 데이터 클렌징, 형식 변환 및 표준화, 통합 또는 다수 애플리케이션에 내장된 비즈니스룰 적용 등
- Load: 변형 단계의 처리가 완료된 데이터를 특정 목표 시스템에 적재

# 데이터 웨어하우스 아이텍처
![데이터 웨어하우스 아이텍처](https://1.bp.blogspot.com/-l1mxs4Dz9B0/X0tfmnwPmiI/AAAAAAAArYE/f0kPU7C9ZeAYvmPzbVBoA_ZI1dRKICCegCPcBGAsYHg/s640/Data%2BWarehouse_three_tiers.png)

## 상단 티어(Top Tier)
통계, 분석, 데이터마이닝, AI 등을 통해 분석한 결과를 리포팅하는 프런트 엔드 티어이다. 가시성을 제공하는 티어

## 중간 티어(Middle Tier)
데이터를 액세스하고 분석하는데 사용하는 분석 엔진으로 구성

## 하단 티어(Bottom Tier)
데이터가 로드되고 저장되는 데이터베이스 서버 티어

# 데이터 웨어하우스의 이점
- 정보에 기반한 의사 결정을 할 수 있다.
- 여러 소스의 데이터를 통합할 수 있다.
- 과거 데이터 분석
- 데이터 품질, 일관성, 정확성을 보장할 수 있다.
- 트랜잭션 데이터베이스와 분석 처리를 분리하여 두 시스템의 성능을 모두 향상시킬 수 있음.

# 데이터베이스(DB), 데이터 웨어하우스(DW), 데이터 레이크(Data Lake), 데이터 마트(Data Mart)
- 데이터베이스(DB)는 트랜잭션의 세부 사항을 기록하는 것과 같이 데이터를 캡처하고 저장하는 데 사용.
- 데이터 웨어하우스(DW)는 데이터 분석을 위해 특별히 설계되었으며, 여기에는 대량의 데이터를 읽어 데이터 전반에 걸친 관계와 추세를 파악하는 작업이 포함
-  데이터 레이크(Data Lake)는 정형, 반정형 및 비정형 데이터를 비롯한 모든 가공되지 않은 다양한 종류의 데이터를 한 곳에 모아둔 중앙 리포지토리. 빅데이터를 효율적으로 분석하고 사용하고자 다양한 영역의 Raw 데이터를 한 곳에 모아서 관리하고자 하는 목적
-  데이터 마트(Data Mart)는 금융, 마케팅 또는 영업과 같은 특정 팀 또는 사업 단위의 요구를 충족시키는 데이터 웨어하우스이다. 규모가 더 작고, 집중적이며 사용자 커뮤니티에 가장 잘 맞는 데이터 요약을 포함할 수 있다. 데이터 마트는 데이터 웨어하우스의 일부일 수도 있음.
![데이터베이스(DB), 데이터 웨어하우스(DW), 데이터 레이크(Data Lake), 데이터 마트(Data Mart)](https://1.bp.blogspot.com/-m6YHd8GtIRo/X0tf9_oCw4I/AAAAAAAArYQ/oJFS2Ix37O0xBDQo3eaAWBKOzT08rqXrgCPcBGAsYHg/s640/Snowflake_Modern_Data_Architectures.png)

# 데이터 웨어하우스(DW) 벤더
클라우드 데이터 플랫폼(Cloud Data Platform, CDP)는 아마존, 마이크로소프트, 구글과 같은 클라우드 서비스 제공자와 기업의 프로그램 사이에 가상의 데이터 레이크(Data Lake)를 제공해, 고객이 쉽게 데이터를 저장하고, 여러 클라우드 서비스 간에 쉽게 데이터를 활용하게 함으로써 대기업 입장에서는 매우 유용한 서비스를 제공. 또한, 파트너나 다른 사업자와도 물리적인 이동 없이 데이터를 공유할 수 있도록 하는 역할을 함.

## 스노우플레이크(Snowflake)
![스노우플레이크(Snowflake)](https://1.bp.blogspot.com/-5RJmf7Klnd8/X0tgIw50hXI/AAAAAAAArYU/UpP30epralI_8-1TLR6a1yyAXsCy-sdGQCPcBGAsYHg/s640/Snowflake%2BCloud%2BData%2BPlatform.png)

## 클라우데라(Cloudera)
![클라우데라(Cloudera)](https://1.bp.blogspot.com/-geO7Oio2Huo/X0tgSZ4PGII/AAAAAAAArYc/PbOkquMFhfQHnJ5smYKHO9OY9zvKGNaaQCPcBGAsYHg/s640/Cloudera%2BData%2BPlatform.png)