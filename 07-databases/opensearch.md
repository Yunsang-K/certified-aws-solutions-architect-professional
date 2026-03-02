## AWS OpenSearch (Amazon OpenSearch Service)

* **Amazon OpenSearch Service**는 **OpenSearch(오픈소스)** 및 **Elasticsearch 계열**을 AWS에서 **관리형(Managed)**으로 운영해주는 서비스입니다.

  * 시험에서는 “(과거) Amazon Elasticsearch Service → (현재) Amazon OpenSearch Service”로 자주 나옵니다.
* **서버리스가 기본 전제는 아님**: 일반적으로 **VPC 안에서 EC2 기반 노드(데이터/마스터 등)**로 클러스터를 구성해 운영합니다.

  * 다만 “서버리스/온디맨드” 같은 표현이 문제에 나오면 **OpenSearch Serverless**(별도 형태)인지 구분을 요구할 수 있습니다. (서비스명이 비슷해서 함정)
* **주요 활용**

  * **로그 분석 / 모니터링**(예: 애플리케이션 로그, 인프라 메트릭 보조 분석)
  * **보안 분석**(이상 징후 탐지, 보안 이벤트 상관 분석)
  * **풀텍스트 검색 & 인덱싱**(검색 기능, 자동완성, 필터링)
  * **클릭스트림 분석**(사용자 행동 이벤트 기반 분석)

## “대체재/비교 대상”으로 자주 등장하는 포인트

문제에서 OpenSearch는 보통 아래 서비스들과 비교로 나옵니다.

* **CloudWatch Logs Insights**: “로그를 간단히 쿼리/분석”이면 Insights가 더 가볍고 관리 부담이 적음.
* **Athena + S3**: “S3에 쌓인 데이터를 SQL로 애드혹 분석(스키마 온 리드)”이면 Athena.
* **Kinesis / MSK**: “실시간 스트리밍 파이프라인”이면 Kinesis/MSK가 앞단, OpenSearch는 종착지/서빙(검색·대시보드)로 붙는 패턴이 많음.
* **RDS/DynamoDB**: “정확한 키 조회/트랜잭션”은 DB, “검색/랭킹/텍스트”는 OpenSearch.

## ELK(혹은 OpenSearch) 스택 구성 요소 정리

* **(ElasticSearch / OpenSearch)**: 검색 엔진 핵심. **인덱싱 + 검색 + 집계(Aggregation)** 제공
* **Kibana (또는 OpenSearch Dashboards)**: **시각화/대시보드**
* **Logstash**: 로그/이벤트를 **수집·변환·전송(ETL)** 하는 파이프라인 컴포넌트

  * 일반적으로 각 소스에서 데이터를 보내려면 에이전트/수집 구성이 필요

## 네 문장만 더 정확히 다듬으면 좋은 부분

* “ELK 스택(ElasticSearch, Logstash, Kibana)”은 **ElasticSearch 기준** 표현이고, AWS에서는 보통

  * **OpenSearch + OpenSearch Dashboards**로 말하는 게 더 정확합니다.
* “Logstash = CloudWatch Logs”는 역할이 완전히 동일하진 않고,

  * Logstash는 **수집·가공·전달 파이프라인**,
  * CloudWatch Logs는 **로그 저장/조회/Insights 분석 플랫폼**에 가깝습니다.
