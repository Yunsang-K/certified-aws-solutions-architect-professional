## AWS Batch (SAP-Pro 관점 정리)

* **AWS Batch**는 대규모 데이터 처리/분석처럼 “사용자 상호작용 없이 돌아가는 작업(Job)”을 **스케줄링·큐잉·자원할당·오케스트레이션**까지 묶어서 제공하는 **관리형 배치 처리 서비스**입니다.
* 사용자는 “무슨 작업을 어떤 리소스로 돌릴지”만 정의하면, Batch가 **필요한 컴퓨팅을 확보하고(Compute Environment)**, **큐에서 우선순위대로 잡을 실행**합니다.

---

## 핵심 구성요소 (시험에서 자주 묻는 포인트)

### 1) Job

* 실제 실행 단위.
* 보통 **컨테이너 기반(이미지 + 커맨드)** 으로 실행.
* **의존성(다른 Job 완료 후 실행)** 을 구성할 수 있음.

### 2) Job Definition

* Job 실행에 필요한 **메타데이터 템플릿**
* 예: vCPU/메모리, IAM Role, 환경변수, 볼륨 마운트(EFS/EBS), 재시도/타임아웃 등

### 3) Job Queue

* Job이 들어가 **대기**하는 곳.
* 여러 큐를 두고 **우선순위(priority)** 로 처리 순서를 제어할 수 있음.

### 4) Compute Environment

* 실제로 Job이 돌아가는 **컴퓨팅 자원 풀**
* 관리 방식에 따라:

  * **Managed CE**: Batch가 용량을 자동으로 맞춰줌
  * **Unmanaged CE**: 사용자가 준비한 환경을 “여기서 돌려라” 식으로 지정

---

## Lambda vs Batch (결정 포인트)

* **실행 시간**

  * Lambda: **최대 15분**
  * Batch: **실질적으로 제한 없음**(장시간 배치/ML/ETL에 적합)
* **런타임/실행 형태**

  * Lambda: 서버리스, 런타임 제약 존재
  * Batch: **Docker 컨테이너 기반**이라 런타임 자유도 높음
* **디스크/파일**

  * Lambda: 기본 디스크 제한(확장 필요 시 EFS 연동 + 보통 VPC 고려)
  * Batch: 워크로드 특성상 **대용량/지속 실행에 유리**

👉 결론: **짧고 이벤트 기반이면 Lambda**, **길고 무거운 배치면 Batch**.

---

## Managed vs Unmanaged AWS Batch (구분 암기)

### Managed Compute Environment

* Batch가 **용량을 자동으로 프로비저닝/스케일**
* 사용자는 **인스턴스 타입/용량 범위, On-Demand vs Spot, 최대 Spot 가격** 같은 “정책”만 지정
* 네트워크/접근(예: VPC 엔드포인트, NAT/IGW 등)은 워크로드에 맞춰 별도 설계 필요

### Unmanaged Compute Environment

* 사용자가 **EC2/ECS 기반 실행 환경을 직접 준비**해두고 Batch에 연결
* 이미 표준화된 컴퓨팅 플랫폼이 있거나 특수 제약이 있을 때 사용
* 보통 **요구되는 AMI/에이전트/구성**이 맞아야 함(운영 부담↑)

---

## SAP-Pro에서 자주 나오는 연결 포인트(키워드)

* **Batch + Step Functions**: 배치 파이프라인/의존성 워크플로우
* **Batch + ECR**: 컨테이너 이미지 관리
* **Batch + CloudWatch Logs**: 로그/모니터링
* **Batch + IAM Role**: S3/DB 접근 권한 제어
* **Batch + EFS**: 공유 스토리지(여러 Job/노드 간 파일 공유)
