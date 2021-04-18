# OLTP (Online Transaction Processing)

OLTP는 네트워크 상의 온라인 사용자들의 Database 에 대한 일괄 트랜잭션 처리를 의미(흔히 말하는 "트랜잭션(Transaction) 처리")

그루핑된 연산의 실패시, Rollback 이 지원됨.

주로 대규모의 처리보다는 소규모의 정교한 데이터 구성이 필요한 데이터를 처리하는데 적합.

# OLAP (Online Analytical Processing)

OLAP는 데이터 웨어하우스(DW) 등의 시스템과 연관되어 Data 를 분석하고 의미있는 정보로 치환하거나, 복잡한 모델링을 가능하게끔 하는 분석 방법.

기능 자체에 중심을 두는 OLTP 와는 다르게 사용하는 목적과 주제에 보다 중점을 둠.

주로 대용량의 데이터에 대해 처리하고 보다 복잡한 Data processing 으로 의미를 추출하는데 중점을 둠
