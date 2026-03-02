# DMS (Database Migration Service) - 데이터베이스 마이그레이션 서비스

* 데이터베이스 마이그레이션은 수행 난이도가 높은 작업이다.
* DMS는 **관리형(Managed) 데이터베이스 마이그레이션 서비스**다.
* 일반적으로 **EC2 기반의 Replication Instance(복제 인스턴스)** 로 시작한다.
* 이 인스턴스는 하나 이상의 **Replication Task(복제 작업)** 를 실행한다.
* 각 Task에는 **Source Endpoint(소스 엔드포인트)** 와 **Target Endpoint(대상 엔드포인트)** 를 지정해야 한다.

  * **엔드포인트 중 최소 1개는 AWS 상에 있어야 한다.**
  * 즉, **온프레미스↔온프레미스만의 마이그레이션 용도로는 사용할 수 없다.**
* 지원 소스 DB 예시: MySQL, Aurora, Microsoft SQL Server, MariaDB, MongoDB, PostgreSQL, Oracle, Azure SQL 등
* DMS는 **Job(작업)** 형태로 마이그레이션을 수행하며, 유형은 3가지다.

  * **Full load(전체 로드)**: 기존 데이터를 소스→타깃으로 일괄 이관. 소스 DB에 **다운타임(중단)** 을 허용할 수 있을 때 적합.
  * **Full load + CDC(Change Data Capture)**: 기존 데이터 이관 후, 이후 발생하는 변경분까지 지속 복제.
  * **CDC only**: 변경분만 복제. 경우에 따라 전체 이관은 다른 방식으로 처리하고, 이후 동기화만 CDC로 하는 편이 효율적일 수 있다.
* DMS는 **스키마 변환을 지원하지 않는다.**

  * 엔진 변경 등 스키마 변환이 필요하면 **SCT(Schema Conversion Tool)** 를 사용해야 한다.

---

## SCT (Schema Conversion Tool) - 스키마 변환 도구

* SCT는 **독립 실행형(Standalone) 애플리케이션**으로, 서로 다른 DB 엔진 간 마이그레이션 시 **스키마/코드 변환**을 수행한다.

  * DB 스키마를 **S3로 변환/내보내기** 같은 작업도 포함 가능.
* **같은 DB 타입 간 마이그레이션에서는 SCT를 사용하지 않는다.**
* SCT는 다음 계열을 지원한다.

  * **OLTP DB**: MySQL, Oracle, Aurora 등
  * **OLAP DB**: Teradata, Oracle, Vertica, Greenplum 등
* 사용 예:

  * 온프레미스 **MSSQL → RDS MySQL** (엔진 변경)
  * **Oracle → Aurora** (엔진/호환 계열 변경)

---

## DMS와 Snowball 조합

* 수 TB 이상(**multi-TB**) 규모 마이그레이션은 네트워크 전송만으로는 시간이 오래 걸리고, 회선/대역폭을 많이 소모한다.
* DMS는 **Snowball 계열**을 활용한 대용량 마이그레이션 시나리오를 지원한다.
* 일반적인 절차:

  1. **SCT로 로컬에서 데이터 추출** 후 Snowball로 이동
  2. Snowball을 AWS로 배송 → AWS가 데이터를 **S3 버킷으로 적재**
  3. DMS가 **S3 → 타깃 DB** 로 마이그레이션 수행
  4. **CDC**로 변경분을 캡처하고, **S3를 중간 매개로** 타깃 DB에 지속 반영 가능
