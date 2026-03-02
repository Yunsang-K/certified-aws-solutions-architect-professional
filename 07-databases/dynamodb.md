# DynamoDB

* **NoSQL DBaaS**(관리형 데이터베이스 서비스)이며, **키-값(Key/Value)** 및 **문서(Document)** 데이터를 처리할 수 있습니다.
* 서버/인프라를 사용자가 직접 운영·관리할 필요가 없습니다.
* 다양한 확장(스케일링) 옵션을 지원합니다.

  * **Provisioned(프로비저닝)** 용량의 수동/자동 확장(읽기/쓰기)
  * **On-Demand(온디맨드)** 모드
* 기본적으로 **멀티 AZ 내구성/가용성**이 높고, 선택적으로 **글로벌(다중 리전)** 구성이 가능합니다.
* **단일 자릿수(ms) 지연**의 매우 빠른 조회 성능을 제공합니다.
* **자동 백업**, **PITR(Point-in-Time Recovery)**, **저장 시 암호화(Encryption at rest)** 를 제공합니다.
* 테이블 데이터 변경 시 이벤트 기반 연동이 가능하며, **테이블 변경(Insert/Update/Delete)** 을 트리거로 동작을 수행할 수 있습니다.

---

## DynamoDB Tables

* **테이블(Table)** 은 **동일한 프라이머리 키 구조**를 갖는 **아이템(Item)** 들의 집합입니다.
* **Primary Key** 는 다음 중 하나입니다.

  * **Simple primary key**: Partition Key(PK)
  * **Composite primary key**: Partition Key(PK) + Sort Key(SK)
* 아이템 개수에는 사실상 제한이 없도록 설계되었습니다.
* Composite key에서는 **PK+SK 조합이 유일(Unique)** 해야 합니다.
* 아이템은 기본 키 외에 추가 데이터인 **Attribute(속성)** 를 가질 수 있습니다.
* 각 아이템은 **기본 키 속성만 동일 구조로 갖고 있다면**, 나머지 속성은 서로 달라도 됩니다.
* 아이템 최대 크기는 **400KB** 입니다.
* 테이블은 **Provisioned / On-Demand** 용량 모드를 선택할 수 있습니다(용량 = 처리량/속도 개념).
* (용어 정리) **Provisioned 모드**에서 설정하는 단위가 보통 다음입니다.

  * **WCU(Write Capacity Unit)**: 1 WCU = **초당 1KB 쓰기 1회**
  * **RCU(Read Capacity Unit)**: 1 RCU = **초당 4KB 읽기 1회(강한 일관성 기준)**
    ※ On-Demand 모드에서도 내부적으로는 RCU/WCU 개념으로 과금이 산정됩니다.

---

## DynamoDB Backups

### On-demand 백업

* 테이블의 **전체 스냅샷(Full copy)** 이며, 사용자가 삭제하기 전까지 보관됩니다.
* 백업으로부터 **복원 시 데이터와 설정**을 복구할 수 있고, **교차 리전 복원**도 지원합니다.
* 복원 시 **인덱스 유지/제거**를 선택할 수 있습니다.
* 복원 시 **암호화 설정**도 조정할 수 있습니다.

### Point-in-time Recovery (PITR)

* 기본 비활성화이며, **명시적으로 활성화**해야 합니다.
* 변경 사항을 연속적으로 기록해 **35일 윈도우** 내 임의 시점으로 되돌릴 수 있습니다.
* 복원은 **1초 단위(1-second granularity)** 로 가능하며, 보통 **새 테이블로 복원**합니다.

---

## DynamoDB Considerations

* DynamoDB는 **관계형 DB가 아니며(Relational 아님)**, 조인 중심의 관계형 모델에 적합하지 않습니다.
* 주로 **키-값 / 문서 기반** 액세스 패턴에 최적화되어 있습니다.
* 접근 방식은 **Console, CLI, API(SDK)** 를 사용합니다.
* “진짜 SQL”을 그대로 지원하진 않지만, **PartiQL(SQL 유사 문법)** 을 제공합니다.
* 과금은 보통 **읽기/쓰기 처리량(RCU/WCU 또는 요청량)**, **스토리지**, **추가 기능(Streams, 백업, 글로벌 테이블 등)** 에 기반합니다. 장기 사용 시 **Reserved Capacity(예약 용량)** 을 구매할 수 있습니다.

---

## DynamoDB Operation, Consistency and Performance

* 테이블 생성 시 **On-Demand / Provisioned** 중 용량 모드를 선택합니다(이후 전환 가능).
* **On-Demand**

  * 트래픽이 **불규칙/예측 불가**할 때 적합
  * 운영 부담이 낮음(처리량 설정을 직접 하지 않음)
  * 보통 **요청량(백만 단위 read/write 요청)** 기준 과금
* **Provisioned**

  * 테이블(및 GSI)에 대해 **RCU/WCU를 직접 설정**하는 방식
* 모든 작업은 최소 **1 RCU/WCU** 이상을 소모합니다(요청 크기/아이템 크기에 따라 증가).
* 단위 정의(핵심)

  * **1 RCU**: 초당 **4KB 강한 일관성 읽기 1회**
    또는 **4KB 최종 일관성 읽기 2회**
  * **1 WCU**: 초당 **1KB 쓰기 1회**
* 테이블에는 **Burst Capacity(버스트 크레딧)** 개념이 있으며, 짧은 시간(예: 수 분 단위) 동안 순간적인 트래픽 스파이크를 완화합니다.

### DynamoDB Operations

* **Query**

  * 반드시 **Partition Key** 를 지정해야 합니다.
  * 필요 시 **Sort Key 조건(=, 범위, begins_with 등)** 을 추가할 수 있습니다.
  * 결과는 0개 이상이며, 항상 특정 PK 파티션 범위에서 조회합니다.
  * 반환 속성을 Projection으로 제한할 수 있지만, 기본적으로는 읽기 비용이 아이템 크기/조회량에 의해 결정됩니다(설정에 따라 차이 발생).
* **Scan**

  * 가장 유연하지만 **가장 비효율적**입니다.
  * 테이블(또는 인덱스) 전체를 훑으며 **스캔한 만큼 용량을 소비**합니다.
  * 필터를 걸어도, 필터링은 **읽은 후 적용**되는 특성이 있어 비용 절감 효과가 제한적입니다.

---

## DynamoDB Consistency Model

* DynamoDB는 두 가지 읽기 일관성 옵션을 제공합니다.

  * **Eventually consistent(최종 일관성)**
  * **Strongly consistent(강한 일관성)**
* DynamoDB는 AZ 전반에 데이터를 복제하는 스토리지 노드 구조를 갖고, 내부적으로 **리더(leader) 역할 노드**가 쓰기를 수용하고 복제본에 전파하는 형태로 동작합니다(구현은 서비스 내부 관리 영역).
* **Eventually consistent reads**

  * 직전 쓰기 직후에는 **이전 값(스테일)** 을 읽을 가능성이 있습니다.
  * 같은 RCU로 **2배 읽기 처리량**을 얻을 수 있습니다.
* **Strongly consistent reads**

  * 최신 값을 보장하도록 읽습니다.
  * **RCU 비용이 최종 일관성 대비 2배**입니다.
  * 애플리케이션이 “읽자마자 최신 값”을 반드시 요구하면 필요합니다.

---

## WCU/RCU 계산

* 예: 초당 10개 아이템 저장, 평균 2.5KB/아이템

  * **WCU**

    * `ceil(2.5KB / 1KB) = 3 WCU(아이템 1개당)`
    * `3 × 10 = 30 WCU`
* 예: 초당 10개 아이템 조회, 평균 2.5KB/아이템

  * **RCU**

    * `ceil(2.5KB / 4KB) = 1 RCU(아이템 1개당)`
    * 강한 일관성: `1 × 10 = 10 RCU`
    * 최종 일관성: 같은 처리량에 필요한 RCU가 절반이므로 `5 RCU`

---

## DynamoDB Indexes

* 인덱스는 DynamoDB 조회를 효율화하기 위한 **대체 뷰(Alternative view)** 입니다.
* 인덱스를 사용하면 **Query** 로 접근 가능한 키 패턴을 확장할 수 있습니다.
* **LSI(Local Secondary Index)**: 동일 PK + 다른 SK
* **GSI(Global Secondary Index)**: 다른 PK(및 선택적 SK)

### Local Secondary Index (LSI)

* **테이블 생성 시에만 생성 가능**합니다.
* 테이블당 최대 **5개**.
* **PK는 동일**, **SK만 변경**한 조회 뷰를 제공합니다.
* 메인 테이블과 **RCU/WCU를 공유**합니다.
* Projection 옵션: `ALL`, `KEYS_ONLY`, `INCLUDE(선택 속성)`
* **Sparse** 특성: 인덱스 키 속성이 없는 아이템은 인덱스에 포함되지 않습니다.

### Global Secondary Index (GSI)

* **언제든 생성 가능**합니다.
* 기본 한도는 보통 테이블당 **20개**(계정/리전별 제한 및 상향 가능).
* **PK와 SK를 새로 정의**할 수 있습니다.
* Provisioned 모드에서 GSI는 **자체 RCU/WCU를 별도 설정**할 수 있습니다.
* Projection 옵션: `ALL`, `KEYS_ONLY`, `INCLUDE`
* **Sparse**: 새 PK/SK에 값이 있는 아이템만 인덱스에 포함됩니다.
* GSI 조회는 일반적으로 **Eventually consistent** 기반으로 동작하며(복제 지연 가능), 메인 테이블로부터 인덱스로 데이터가 전파됩니다.

### LSI/GSI 고려사항

* Projection을 과도하게 포함하면 **용량 소모**가 커집니다.
* 인덱스에 필요한 속성을 Projection하지 않으면, 조회 후 **원본 테이블에서 추가 페치**가 발생해 비효율이 될 수 있습니다.
* 일반적으로 AWS는 **GSI를 기본 선택**으로 권장하고, **강한 일관성 요구** 등 특정 케이스에서 LSI를 고려하는 흐름이 많습니다.

---

## DynamoDB Streams and Triggers

* **DynamoDB Streams** 는 테이블 변경 사항의 **시간 순서 로그**입니다.
* Streams는 **24시간 롤링 윈도우**로 변경 이벤트를 유지합니다(내부적으로 Kinesis 계열 메커니즘을 활용).
* 테이블 단위로 활성화해야 합니다.
* Insert/Update/Delete를 기록합니다.
* View type(레코드에 담을 정보 수준)

  * `KEYS_ONLY`: 변경된 아이템의 키만 기록
  * `NEW_IMAGE`: 변경 후 전체 아이템
  * `OLD_IMAGE`: 변경 전 전체 아이템
  * `NEW_AND_OLD_IMAGE`: 변경 전/후 모두 기록
* 삭제의 경우 NEW_IMAGE가 비어 있는 등, 상황에 따라 이미지가 비어 있을 수 있습니다.
* Streams는 **DB 트리거(Triggers)** 구현의 기반이며, 보통 **Lambda** 와 결합해 집계/메시징/알림 등을 처리합니다.

---

## DynamoDB Accelerator (DAX)

* DynamoDB와 직접 통합되는 **인메모리 캐시**입니다.
* **VPC 내부**에서 동작하며, 보통 멀티 AZ로 클러스터를 구성합니다.
* 클러스터는 노드로 구성되며, **Primary/Replica** 형태로 복제합니다.
* 두 가지 캐시

  * **Item cache**: (`Batch`)`GetItem` 결과
  * **Query cache**: Query/Scan 파라미터 기반 결과 집합
* DAX 엔드포인트로 접근하며, 엔드포인트가 노드들에 로드밸런싱합니다.
* 장애 시 리더가 선출되어 HA를 제공합니다.
* Scale-up / scale-out 가능
* 쓰기 시 **Write-through** 방식으로 DB와 캐시에 함께 반영됩니다.
* 애플리케이션도 **VPC 내**에 있어야 DAX를 사용하기 쉽습니다(네트워크 접근 제약).
* 캐시 히트는 마이크로초 단위, 미스는 밀리초 단위.
* **Strongly consistent reads가 필요한 워크로드**에는 부적합합니다.

---

## DynamoDB Global Tables

* **다중 리전 멀티마스터** 복제를 제공합니다.
* 여러 리전에 테이블을 만들고 동일 글로벌 테이블로 묶어 **replica tables** 로 구성합니다.
* 충돌 해결은 **Last writer wins** 입니다.
* 어느 리전에서든 읽기/쓰기가 가능하며, 업데이트는 보통 **서브-초(sub-second)** 로 전파됩니다(지연 가능).
* **강한 일관성 읽기**는 일반적으로 **동일 리전 내**에서만 보장 가능한 범위가 큽니다.
* 글로벌 HA 및 DR/BC 목적에 유용합니다.

---

## DynamoDB TTL

* **TTL(Time-to-Live)** 을 활성화하고 TTL로 쓸 **속성(attribute)** 을 지정합니다.
* TTL 속성 값은 보통 **Epoch time(초 단위 정수)** 입니다.
* 파티션 단위 백그라운드 프로세스가 TTL 만료 여부를 확인해 만료 처리합니다.
* 만료된 아이템은 이후 백그라운드에서 테이블/인덱스에서 삭제되며, Streams가 켜져 있으면 **삭제 이벤트**가 기록될 수 있습니다.
* 백그라운드로 수행되며, 일반적으로 테이블 성능에 큰 영향을 주지 않고 **추가 비용 없이** 사용할 수 있는 패턴으로 안내됩니다.
* TTL 삭제 이벤트를 추적해 정리 작업(하우스키핑)이나 “복구(undelete) 유사 로직”을 구성하는 패턴도 가능합니다(설계 시 이벤트 보존/재처리 전략 필요).
