# AWS QuickSight (AWS Certified Solutions Architect – Professional 기준)

* **AWS QuickSight는 완전관리형 BI/BA(비즈니스 인텔리전스/비즈니스 애널리틱스) 서비스**입니다.
* **대시보드/리포트 시각화**와 **애드혹(Ad-hoc) 분석**에 사용합니다.
* **서버를 직접 운영하지 않는 온디맨드 서비스**라서, 사용량/구독 형태로 비용을 최적화하기 좋습니다.
* AWS 내부 데이터 소스뿐 아니라 **외부(SaaS/DB/엔진) 데이터 소스와 연동**할 수 있습니다.
* 다양한 데이터 소스를 “발견/연결”하고, 시각화에 맞게 분석용 데이터셋으로 구성해 사용할 수 있습니다.

## 데이터 소스(예시) 정리

* **AWS**: Athena, Aurora, Redshift, Redshift Spectrum, S3, AWS IoT
* **SaaS**: Jira, GitHub, Twitter, Salesforce
* **DB/엔진**: Microsoft SQL Server, MySQL, PostgreSQL, Apache Spark, Snowflake, Presto, Teradata

## SAP 관점에서 자주 같이 나오는 포인트(암기용)

* 대용량/고속 대시보드: **SPICE(QuickSight 인메모리 엔진)** 고려
* 중앙 데이터 레이크/웨어하우스와 조합: **S3/Athena/Redshift** 패턴이 자주 출제됨
* 보안/거버넌스: 사용자/그룹 권한, 데이터 접근 제어(행 수준 보안 같은 것) 관점으로 묶어서 기억해두면 좋습니다.
