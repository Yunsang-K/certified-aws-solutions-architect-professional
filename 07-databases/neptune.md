# Amazon Neptune (AWS Certified Solutions Architect – Professional 기준 한국어 번역)

* **Neptune는 AWS의 관리형 그래프 데이터베이스** 서비스이다.
* **그래프 데이터베이스**는 데이터 자체만큼이나 **데이터 간 관계(relationship)** 가 중요한 DB 유형이다.
* Neptune는 **VPC 내부에서 실행**되며, 기본적으로 **프라이빗(Private)** 환경으로 구성된다.
* **내결함성(Resilient)** 이 높고, **멀티 AZ 배포**가 가능하며 **리드 리플리카(Read Replica)** 로 읽기 확장(scale)이 가능하다.
* **지속적 백업(continuous backups)** 을 수행하며, **시점 복구(Point-in-time recovery)** 를 지원한다.
* 그래프 기반 데이터 모델의 대표적인 사용 사례:

  * **소셜 미디어** 등 관계가 자주/유동적으로 변하는 데이터(팔로우, 친구, 연결 등)
  * **부정/사기 탐지(Fraud prevention)**
  * **추천 시스템(Recommendation engines)**
  * **네트워크 및 IT 운영(Network & IT Operations)** (의존 관계, 토폴로지 분석 등)
  * **생물학/생명과학(Biology & life sciences)** (유전자/단백질/상호작용 네트워크 등)
