# AWS Glue

* **AWS Glue**는 **서버리스 ETL(Extract, Transform, Load) 서비스**이다.
* 데이터를 **추출, 변환, 적재**하는 작업을 수행하며, 데이터 소스와 대상 간에 데이터를 이동하고 가공할 때 사용한다.
* **AWS Data Pipeline**도 ETL 작업이 가능하지만, 해당 방식은 서버 리소스(예: EMR 클러스터)를 활용하는 반면, **Glue는 서버리스 방식**으로 동작한다.
* Glue는 데이터 소스를 크롤링하여 **AWS Glue Data Catalog**도 생성할 수 있다.
* 지원하는 데이터 소스는 다음과 같다.

  * **S3**
  * **RDS**
  * **JDBC 호환 데이터 소스**
  * **DynamoDB**
* 또한 다음과 같은 **스트리밍 데이터 소스**도 지원한다.

  * **Kinesis Data Streams**
  * **Apache Kafka**
* 지원하는 데이터 대상은 다음과 같다.

  * **S3**
  * **RDS**
  * **JDBC 데이터 소스**

---

## Data Catalog

* **Data Catalog**는 리전 내 데이터 소스에 대한 **영구적 메타데이터 저장소**이다.
* **계정별로 리전마다 하나의 고유한 Data Catalog**를 가진다.
* 조직 내 여러 곳에 흩어진 데이터를 한곳에서 식별하고 탐색할 수 있게 해 주므로, **데이터 사일로(Data Silo)** 문제를 줄이는 데 도움이 된다.
* Data Catalog는 다음 서비스들과 연계해서 사용할 수 있다.

  * **Amazon Athena**
  * **Amazon Redshift Spectrum**
  * **Amazon EMR**
  * **AWS Lake Formation**
* 데이터는 **Crawler**를 설정하고, 해당 데이터 소스에 접근할 수 있는 **권한(자격 증명)**을 제공하여 자동으로 발견할 수 있다.

---

## Glue Job

* **Glue Job**은 **ETL 작업**을 수행한다.
* 사용자가 작성한 스크립트를 이용해 데이터를 변환할 수 있다.
* Glue Job은 **서버리스**이므로, AWS가 필요한 실행 리소스 풀을 관리한다.
* 사용자는 **실제로 사용한 리소스만큼만 과금**된다.

---

## 시험 포인트

* **Glue = 서버리스 ETL**
* **Glue Crawler = 데이터 탐색 및 Data Catalog 생성**
* **Data Catalog = 메타데이터 저장소**
* **Athena / Redshift Spectrum / EMR / Lake Formation**과 연동 가능
* 정형 데이터뿐 아니라 **스트리밍 소스(Kinesis, Kafka)**도 지원
* 직접 서버를 운영하지 않고 **사용량 기반 과금**이라는 점이 핵심
