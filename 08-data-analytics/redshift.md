# Amazon Redshift

* 페타바이트(PB) 규모의 데이터 웨어하우스 서비스
* 리포팅 및 분석(Analytics) 용도로 설계됨
* OLTP(행 기반/트랜잭션) DB가 아니라 **OLAP(열 기반)** DB

  * **OLTP(Online Transaction Processing)**: 실시간 트랜잭션 데이터를 캡처·저장·처리
  * **OLAP(Online Analytical Processing)**: (주로 OLTP 등) 다른 시스템에서 축적된 **과거/집계 데이터**를 대상으로 복잡한 분석 쿼리를 수행하도록 설계

## Redshift의 고급 기능

* **Redshift Spectrum**: 데이터를 Redshift로 적재하지 않고도 **S3의 데이터를 직접 쿼리**
* **Federated Query**: 원격(외부) 데이터 소스에 저장된 데이터를 **직접 쿼리**
* **QuickSight**와 연동하여 시각화 가능
* JDBC/ODBC를 통한 **SQL 유사 인터페이스** 제공
* 기본적으로 **프로비저닝(Provisioned)** 기반 서비스이며 서버리스가 아님

  * 단, **Redshift Serverless** 옵션도 제공됨
  * 프로비저닝 방식은 준비(프로비저닝) 시간이 발생함
* **클러스터 아키텍처** 사용

  * 클러스터는 프라이빗 네트워크이며 **직접 접근이 불가**
* 기본 설계상 **단일 AZ에서 동작**(기본적으로 HA 구조가 아님)
* 모든 클러스터는 **리더(Leader) 노드**를 가짐

  * 쿼리 수행 시 리더 노드가 쿼리 계획/집계 등을 담당
* **컴퓨트(Compute) 노드**: 실제로 데이터를 처리하며 쿼리를 실행

  * 컴퓨트 노드는 **슬라이스(Slice)**로 분할됨
  * 각 슬라이스는 메모리·디스크의 일부를 할당받아 워크로드 일부를 처리하며, 슬라이스들이 병렬로 동작
  * 노드 리소스 용량에 따라 보통 2, 4, 16, 32개의 슬라이스를 가질 수 있음
* VPC 기반 서비스이며 VPC 보안 모델 사용

  * IAM 권한, KMS 기반 저장 시 암호화, CloudWatch 모니터링 등
* **Redshift Enhanced VPC Routing**

  * 기본적으로 Redshift가 외부 서비스 또는 퍼블릭 AWS 서비스(예: S3)와 통신할 때 VPC의 네트워크 제어(보안 그룹, NACL 등) 밖의 경로를 사용할 수 있음
  * 활성화 시 트래픽이 **VPC 네트워크 구성(보안 그룹, NACL, 라우팅, DNS, 게이트웨이 등)** 을 따르도록 라우팅됨
  * 결과적으로 보안 그룹을 통한 제어, VPC DNS 사용, VPC 게이트웨이(예: NAT/IGW/VPCE) 기반 경로 통제가 가능

## Redshift 아키텍처

* (이미지) Redshift architecture: `images/RedshiftArchitecture.png`

---

## Redshift 구성 요소(Components)

* **클러스터(Cluster)**: 리더 노드 1개 + 컴퓨트 노드 1개 이상으로 구성된 노드 집합

  * 클러스터를 프로비저닝하면 **데이터 적재 및 쿼리 실행에 사용하는 DB 1개가 생성**됨
  * 노드를 추가/제거하여 **Scale out/in** 가능
  * 노드 타입을 변경하여 **Scale up/down** 가능
  * 리전별로 8시간 블록 중 임의로 **30분 유지보수 윈도우**가 할당되며, 주중 임의의 요일에 발생

    * 유지보수 동안 클러스터는 일반 작업에 사용 불가
  * Redshift는 **EC2-VPC** 및 **EC2-Classic** 플랫폼에서 클러스터 런칭을 지원
  * VPC에 프로비저닝하는 경우 **클러스터 서브넷 그룹(Cluster Subnet Group)** 을 생성하여 VPC 내 서브넷 집합을 지정

* **Redshift 노드(Nodes)**

  * **리더 노드(Leader node)**

    * 클라이언트 애플리케이션의 쿼리를 수신
    * 쿼리를 파싱하고 실행 계획을 생성
    * 컴퓨트 노드와 병렬 실행을 조율하고 중간 결과를 집계
    * 최종 결과를 클라이언트에 반환
  * **컴퓨트 노드(Compute nodes)**

    * 실행 계획에 따라 쿼리를 수행
    * 노드 간 데이터 전송을 통해 쿼리를 처리
    * 중간 결과를 리더 노드로 전달(리더 노드가 집계 후 클라이언트로 반환)
  * **노드 타입(Node Type)**

    * **DS(Dense Storage)**: 대용량 데이터 워크로드용, HDD 사용
    * **DC(Dense Compute)**: 성능 집약 워크로드 최적화, SSD 사용

* **파라미터 그룹(Parameter Groups)**

  * 클러스터 내 생성되는 모든 DB에 적용되는 파라미터 집합
  * 기본(Default) 파라미터 그룹은 사전 설정값을 가지며 수정 불가

---

## Redshift 복원력 및 복구(Resilience and Recovery)

* 백업은 스냅샷 형태로 **S3에 저장** 가능
* 백업 종류 2가지

  * **자동 백업(Automated backups)**

    * 기본적으로 8시간마다 또는 5GB 데이터마다 수행
    * 기본 보존 1일(최대 35일)
    * 스냅샷은 **증분(incremental)** 방식
  * **수동 스냅샷(Manual snapshots)**

    * 수동 트리거로 수행
    * 별도 보존기간 설정이 없을 수 있음(운영 정책에 따라 관리)
* 스냅샷에서 복원 시 **새 클러스터가 새로 생성**되며, 프로비저닝할 AZ를 선택 가능
* 스냅샷을 다른 리전으로 복사하여 해당 리전에 새 클러스터를 프로비저닝 가능
* 복사된 스냅샷에도 보존 기간을 설정할 수 있음
* (이미지) Redshift Resilience and Recovery: `images/RedshiftDR.png`

---

## Amazon Redshift Workload Management(WLM)

* 워크로드 내 우선순위를 유연하게 관리하여 **짧고 빠른 쿼리가 긴 쿼리 뒤에서 대기열에 묶이지 않도록** 지원
* WLM은 서비스 클래스(Service Class)에 따라 런타임에 쿼리 큐를 생성

  * 내부 시스템 큐 및 사용자 접근 가능 큐 등 다양한 큐 구성을 포함
* 사용자 관점에서 **사용자 접근 가능 서비스 클래스**와 **큐(queue)** 는 기능적으로 동일하게 취급됨
