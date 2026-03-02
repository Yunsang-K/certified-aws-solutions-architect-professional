# RDS - Relational Database Service

* RDS는 흔히 DBaaS(Database-as-a-Service)로 설명되지만, 엄밀히 말하면 완전한 의미의 DBaaS라기보다는 **Database Server as a Service(DBSaaS)**에 가깝습니다.
* RDS는 **관리형 데이터베이스 인스턴스**를 제공하며, 하나의 인스턴스 안에 **여러 개의 데이터베이스**를 생성해 운영할 수 있습니다.
* RDS의 장점은 **물리 하드웨어**, **서버 OS**, **DB 엔진 설치/유지보수**를 직접 관리하지 않아도 된다는 점입니다.
* RDS는 **MySQL, MariaDB, PostgreSQL, Oracle, Microsoft SQL Server**를 지원합니다.
* **Amazon Aurora**는 AWS가 만든 DB 엔진이며, RDS에서 엔진으로 선택해 사용할 수 있습니다.
* **RDS Subnet Group**: RDS DB가 배치될 수 있는 **서브넷 목록**입니다. 일반적으로 DB 배포 단위로 **Subnet Group을 1개씩** 두는 것이 베스트 프랙티스입니다.

---

## RDS Database Instance

* 위에 언급한 DB 엔진 중 하나로 실행됩니다.
* 하나의 인스턴스에 여러 개의 사용자 DB를 생성할 수 있습니다.
* 생성 후 인스턴스는 **호스트명(CNAME)** 으로 접속합니다.
* RDS 인스턴스는 EC2와 유사하게 다양한 타입이 있으며 예: **db.m5, db.r5, db.t3**
* RDS 인스턴스는 **Single-AZ** 또는 **Multi-AZ(Active-Passive 페일오버)** 로 구성할 수 있습니다.
* 인스턴스를 프로비저닝하면 **전용 스토리지(일반적으로 EBS)** 가 함께 할당됩니다.
* 스토리지는 **SSD(IO1, GP2)** 또는 **Magnetic(주로 호환성 목적)** 기반이 될 수 있습니다.
* RDS 과금:

  * 인스턴스 크기 기준 **시간당 과금**
  * Multi-AZ 구성 시 **추가 인스턴스 비용**
  * 스토리지 **(GB/월)** + 프로비저닝 IOPS(IO1) 사용 시 **IOPS 추가 과금**
  * 인터넷/타 리전으로의 데이터 전송 시 **데이터 전송 과금**
  * 백업/스냅샷 **(GB/월)** 과금
  * 라이선스(엔진에 따라) 비용도 포함/추가될 수 있음

---

## RDS Multi-AZ

* Multi-AZ 배포는 2가지 타입이 있습니다.

  * **Multi-AZ Instance** (과거 “Multi-AZ”라고 부르던 방식)
  * **Multi-AZ Cluster**

### Multi-AZ Instance

* RDS 인스턴스의 **내결함성/가용성**을 높이기 위한 구성입니다.
* 복제는 **스토리지 레벨**에서 발생합니다.
* Primary와 Standby 사이에 **동기식 복제(synchronous replication)** 를 제공합니다.
* Multi-AZ를 활성화하면 다른 AZ에 **대기(standby) 인스턴스**용 하드웨어가 할당됩니다.
* RDS는 제공되는 **엔드포인트(CNAME)** 로 접근합니다.

  * Single-AZ에서는 엔드포인트가 해당 인스턴스를 가리킵니다.
  * Multi-AZ에서는 기본적으로 엔드포인트가 **Primary**를 가리킵니다.
* Standby 인스턴스는 **직접 접근할 수 없습니다.**
* Primary에 장애가 나면 RDS가 자동으로 엔드포인트를 Standby로 전환합니다(대략 **60~120초**).
* 일반적으로 프리 티어에서는 Multi-AZ를 사용할 수 없고, 비용은 보통 **단일 AZ 대비 거의 2배**에 가깝습니다.
* 백업은 Standby에서 수행되어 Primary 성능 영향을 줄입니다.
* 페일오버 시 DNS가 Standby를 가리키도록 변경되며, DNS 특성상 반영에 **60~120초** 정도 걸릴 수 있습니다. 애플리케이션의 DNS 캐시를 줄이면 완화할 수 있습니다.
* Multi-AZ Instance는 **Standby 1개**만 가집니다. 이 Standby는 **읽기/쓰기 불가**이며 페일오버를 기다립니다.
* 페일오버 트리거 예:

  * AZ 장애
  * Primary 인스턴스 장애
  * 수동 페일오버
  * 인스턴스 타입 변경
  * 소프트웨어 패치 적용

### Multi-AZ Cluster

* **Writer 1개가 Reader 2개로 복제**하는 구조입니다. (Reader는 **최대 2개**)
* Reader는 Writer와 **다른 AZ**에 배치됩니다.
* Aurora Cluster와 비교:

  * RDS Multi-AZ Cluster는 Reader가 2개로 제한되지만, Aurora는 더 많이 둘 수 있습니다.
* Multi-AZ Instance와 달리, 복제된 인스턴스(Reader)는 **실제로 사용 가능**합니다.
* 커밋 관점에서, Reader 중 **하나가 기록 확인을 하면 커밋된 것으로 간주**합니다.
* Aurora와 비교 추가 포인트:

  * RDS Multi-AZ Cluster는 **각 인스턴스가 자체 스토리지**를 가집니다(반면 Aurora는 공유 스토리지 구조).
  * Aurora처럼 여러 엔드포인트로 접근할 수 있습니다.

    * **Cluster endpoint**: Writer를 가리키는 DB CNAME. 읽기/쓰기 및 관리 용도
    * **Reader endpoint**: 읽기 전용으로 사용. 보통 Reader 인스턴스를 가리키며, 특정 상황에서는 Writer로 갈 수도 있음
    * **Instance endpoints**: 각 인스턴스별 엔드포인트(일반적으로 권장하지 않음)
* 일반적으로 Multi-AZ Cluster는 더 빠른 하드웨어(예: **Graviton + 로컬 NVMe SSD**)를 사용합니다. 쓰기는 먼저 로컬의 매우 빠른 스토리지에 기록 후 EBS로 플러시됩니다.
* 복제는 **트랜잭션 로그 기반**으로 더 효율적이며, 이로 인해 페일오버가 더 빠릅니다(약 **35초 + 트랜잭션 로그 적용 시간**).

---

## RDS Backups and Restores

* **RPO(Recovery Point Objective)**: 마지막 정상 백업 시점과 장애 시점 사이의 데이터 손실 허용 범위. 낮출수록 보통 비용 증가.
* **RTO(Recovery Time Objective)**: 장애 발생부터 완전 복구까지 걸리는 시간. 예비 하드웨어/프로세스 준비로 줄일 수 있으며, 낮출수록 보통 비용 증가.

### RDS 백업 유형

* **수동 스냅샷(Manual snapshots)**

  * 수동 또는 스크립트로 실행
  * 첫 스냅샷은 Full, 이후는 Incremental
  * 스냅샷 시점에 컴퓨트-스토리지 데이터 흐름에 짧은 중단이 있을 수 있음(단, Multi-AZ에서는 Standby에서 수행되어 영향이 거의 없음)
  * 수동 스냅샷은 **만료되지 않음**
  * RDS 인스턴스 삭제 시 AWS가 **최종 스냅샷** 생성을 제안함

* **자동 백업(Automatic backups)**

  * 하루 1회(백업 윈도우 내) 자동 스냅샷 수행
  * 첫 스냅샷은 Full, 이후 Incremental
  * 자동 스냅샷 외에도 **5분 단위 트랜잭션 로그**가 S3에 기록됨
  * 보존 기간은 **0~35일** 설정
  * DB 삭제 후에도 자동 백업 보관은 가능하지만, 보존 기간 만료 시 삭제됨
  * 다른 리전으로 백업 복제 가능(스냅샷 + 트랜잭션 로그). 리전 간 복제/저장 비용 발생
  * Cross-Region 복제는 자동 백업 설정에서 **명시적으로 구성**해야 함

* 백업은 AWS가 관리하는 S3 버킷에 저장되며(사용자 S3에서 직접 보이지 않음), S3 특성상 **리전 단위 내구성**을 가집니다.

* Multi-AZ 활성화 시 백업은 Standby에서 수행됩니다.

### RDS 복구(Restore)

* 자동 백업/수동 스냅샷을 복구하면 RDS는 **새로운 RDS 인스턴스**를 생성하므로 **새 엔드포인트**가 생깁니다.
* 스냅샷 복구는 스냅샷 생성 시점의 **단일 시점**으로 복구합니다.
* 자동 백업은 **PITR(Point-in-Time Restore)** 을 지원하며 **5분 단위**의 원하는 시점으로 복구할 수 있습니다.
* 스냅샷 복구는 빠른 작업이 아니므로 **RTO 관점에서 주의**가 필요합니다.

---

## RDS Read Replicas

* 주요 목적 2가지: **성능(읽기 확장)**, **가용성(대안/복구 옵션)**
* Read Replica는 **읽기 전용** 복제본입니다.
* Multi-AZ Cluster는 읽기 인스턴스를 제공한다는 점에서 유사하지만, Read Replica는 별도 개념입니다:

  * 메인 DB 인스턴스의 “일부”가 아니라 **별도 리소스**
  * **자체 엔드포인트**를 가짐
  * 애플리케이션이 분기 처리 등 **지원**해야 함
  * Read Replica로의 **자동 페일오버는 기본 제공되지 않음**
* Primary ↔ Read Replica는 **비동기 복제(asynchronous replication)** 로 동기화됩니다.
* 복제 지연(lag)이 소량 발생할 수 있습니다.
* Read Replica는 다른 AZ 또는 다른 리전에도 생성 가능(CRR 성격의 구성).
* DB 인스턴스당 **최대 5개**의 직접 Read Replica 생성 가능.
* Read Replica마다 읽기 성능이 추가됩니다.
* Read Replica에 또 다른 Read Replica를 붙일 수 있으나, 이 경우 lag가 문제가 될 수 있습니다.
* 글로벌 성능 개선(리전 분산)에 도움이 됩니다.
* 스냅샷/백업은 RPO를 개선하지만 RTO에는 제한적입니다. Read Replica는 **거의 0에 가까운 RPO**에 유리할 수 있습니다.
* Read Replica를 Primary로 **승격(promote)** 하면 낮은 RTO도 기대할 수 있지만(지연이 분 단위일 수 있음), 데이터 정합성/절차를 고려해야 합니다.
* Read Replica는 **데이터 손상도 그대로 복제**될 수 있습니다.

---

## Data Security

* 모든 RDS 엔진은 **전송 구간 암호화(SSL/TLS)** 를 지원하며, 사용자 단위로 강제할 수도 있습니다.
* **저장 시 암호화(encryption at rest)** 는 EBS 볼륨을 KMS로 암호화하는 방식이며 DB 엔진 입장에서는 투명합니다.
* 고객 관리형 또는 AWS 관리형 CMK를 사용할 수 있습니다.
* 스토리지, 로그, 스냅샷은 동일한 CMK로 암호화됩니다.
* 암호화는 활성화 후 **해제할 수 없습니다.**
* 추가로 MSSQL/Oracle은 DB 엔진 레벨의 **TDE(Transparent Data Encryption)** 를 지원합니다.
* Oracle은 CloudHSM과 연동한 TDE로 더 강한 키 관리 구성이 가능합니다.
* IAM 인증(IAM authentication):

  * 기본적으로 DB 로그인은 로컬 DB 사용자(계정/비밀번호)로 제어합니다.
  * RDS를 **IAM 인증 허용**으로 설정할 수 있습니다(인증만 IAM, 권한 부여는 DB 내부 권한으로 처리).
  * (원문 이미지 참조: `images/RDSIAMAuthentication.png`)

---

## RDS Proxy

* DB 커넥션의 생성/종료는 자원을 소모하고 시간이 걸립니다. 짧은 쿼리만 수행하는 워크로드에서는 커넥션 오버헤드가 **지연의 주요 원인**이 될 수 있습니다.
* DB 장애 처리(특히 페일오버 시)도 애플리케이션에 부담과 리스크를 줍니다.
* 프록시를 직접 운영하는 것도 스케일링/HA 등 관리가 쉽지 않습니다.
* RDS Proxy를 쓰면 애플리케이션은 DB가 아니라 **프록시에 연결**하고, 프록시가 커넥션 풀링 및 DB 연결을 관리합니다.
* RDS Proxy는 **멀티플렉싱(multiplexing)** 을 제공해, DB에 실제로 맺는 커넥션 수를 줄이면서 더 많은 애플리케이션 커넥션을 수용할 수 있습니다.
* 페일오버 시 프록시가 정상 인스턴스를 감지해 재연결을 수행함으로써 애플리케이션의 부담을 줄일 수 있습니다.

### 언제 RDS Proxy를 쓰나?

* `Too many connections` 같은 오류가 발생할 때: DB 커넥션 수를 줄이고 프록시가 커넥션을 재사용하도록 할 수 있음
* AWS Lambda 같이 호출 단위로 커넥션이 늘어나는 환경: 커넥션 재사용 및 IAM 인증 연계로 효율 개선
* 장기 실행 애플리케이션(SaaS 등): 커넥션 관리 비용과 레이턴시 감소

### RDS Proxy 핵심 정리

* RDS/Aurora에서 제공하는 **완전 관리형**
* 기본적으로 **오토 스케일링, 고가용성(HA)**
* **커넥션 풀링**으로 DB 부하 감소
* **VPC 내부에서만 접근 가능**(퍼블릭 인터넷 직접 접근 불가)
* **Proxy Endpoint** 로 접근
* SSL/TLS 강제 가능
* Aurora의 경우 페일오버 시간을 **60% 이상 단축**할 수 있음
* DB 장애/페일오버를 애플리케이션에서 어느 정도 **추상화**해줌

---

## RDS Custom

* “일반 RDS”와 “EC2에 DB 직접 설치” 사이의 갭을 메우는 서비스입니다.
* 일반 RDS는 완전 관리형이라 OS/엔진 접근이 제한됩니다.
* EC2 기반 DB는 자유도가 높지만 운영 부담이 큽니다.
* RDS Custom은 RDS의 관리 편의성과 함께, EC2 수준의 **커스터마이징 접근**을 제공합니다.
* 현재 RDS Custom은 **MSSQL 또는 Oracle**에서 사용 가능합니다.
* underlying OS에 **SSH, RDP, Session Manager** 등으로 접속할 수 있습니다.
* RDS Custom은 **고객 AWS 계정 내에서 실행**됩니다. 일반 RDS는 AWS 관리 환경에서 실행됩니다.
* RDS Custom에서 커스터마이징 작업을 할 때는 **Database Automation**이 중단/간섭하지 않도록 설정을 확인해야 하며, 필요 시 해당 기간에 **Database Automation을 일시 중지(pause)** 해야 합니다.
