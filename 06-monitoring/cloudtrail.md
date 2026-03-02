## CloudTrail

* **CloudTrail**은 AWS 계정에서 발생하는 **API 호출(누가/무엇이/언제/어디서/무슨 작업을)** 기록하는 서비스입니다.
  예: EC2 인스턴스 중지, S3 버킷 삭제 등

* 기록은 **CloudTrail Event**로 남고, **사용자(User) / 역할(Role) / AWS 서비스**가 수행한 작업을 포함합니다.

---

## Event History vs Trail

### Event History

* 기본으로 **활성화(Enabled by default)** 되어 있고 **최근 90일**의 관리 이벤트를 콘솔에서 조회할 수 있습니다.
* 기본 조회는 **저장/보관을 장기화하거나, 계정 간/리전 간 통합 저장** 용도로는 제한이 있습니다.

### Trail

* CloudTrail 로그를 **S3로 장기 보관**(JSON 로그 파일 형태)하고, 필요하면 **CloudWatch Logs**로도 내보낼 수 있습니다.
* 분석/경보를 위해 Athena, OpenSearch, SIEM, Metric Filter 등을 붙이기 쉬워집니다.

---

## 이벤트 유형 (Trail에서 선택/확장)

### 1) Management events (기본)

* **Control plane** 작업(리소스 “관리” 작업)을 기록
  예: RunInstances(EC2 생성), TerminateInstances(종료), IAM 정책 변경 등
* 기본 설정에서 주로 이 범주가 기록됩니다.

### 2) Data events (옵션)

* **Data plane** 작업(리소스 “데이터 접근/사용” 작업)을 기록
  예: S3 객체 Get/Put, Lambda Invoke 등
* **비용/로그량이 커질 수 있어** 필요한 리소스만 선택하는 설계가 중요합니다.

### 3) Insights events (옵션)

* **API 호출량/에러율**의 “평소 패턴(베이스라인)”을 학습하고, 이상 징후가 나면 **Insights 이벤트**를 생성합니다.
* 운영 관점에서 “갑자기 호출 폭증/오류 폭증” 같은 탐지에 유용합니다.

---

## 리전/글로벌 이벤트 처리

* CloudTrail은 **리전 단위 서비스**이지만, Trail은 다음 중 하나로 구성합니다.

  * **Single-region trail**: 생성한 리전의 이벤트만 수집
  * **Multi-region trail(All regions)**: 모든 리전 이벤트를 수집

* 일부 서비스(IAM, STS, CloudFront 등)는 **글로벌 이벤트**로 취급되어 특정 리전(일반적으로 **us-east-1**)에 기록되는 형태가 많습니다.

  * “글로벌 서비스 이벤트 포함” 옵션을 켜야 수집되는 구성들이 있습니다.
  * 시험에서는 보통 **“모든 리전 Trail + 글로벌 이벤트 포함”** 조합이 정답으로 자주 나옵니다.

---

## Organizations Trail

* AWS Organizations **관리 계정(Management account)** 에서 Trail을 만들고,
  **조직 내 모든 계정의 이벤트를 중앙 S3로 집계**할 수 있습니다(조직 단위 감사/컴플라이언스).

---

## 실시간 여부 (중요 포인트)

* “CloudTrail은 실시간이 아니다”라는 취지는 맞습니다.
  다만 시험/실무에서의 정확한 감각은:

  * **CloudTrail 로그 파일의 S3 전달은 수 분 단위 지연이 흔함**(준실시간에 가깝지만 즉시성 보장 X)
  * “즉시 탐지/대응”이 목표면 **CloudTrail → CloudWatch Logs → Metric Filter/Alarm**, 또는 다른 실시간 텔레메트리와 조합을 고려합니다.

---

## 과금 핵심

* **Event History(최근 90일 조회)**: 기본 제공 범위는 무료
* **Management events**: 계정/리전당 “기본 1개 복사본”은 무료 범주로 설명되는 경우가 많고,
* **추가 Trail, Data events, Insights, 로그 저장/분석(특히 S3 저장량/Athena 스캔)** 등에서 비용이 발생합니다.

---

## 자주 나오는 시험 포인트 (암기용)

* “누가 S3 객체를 삭제했는지” → **CloudTrail Data events(S3 Object-level)** 필요
* “조직 전체 계정의 API 감사” → **Organization trail + 중앙 S3**
* “콘솔에서 90일 이상 검색” → **Trail로 S3 보관 + Athena 등으로 조회**
* “이상 호출 폭증 탐지” → **CloudTrail Insights**

원하면, 위 내용을 **SAP-C02 스타일로 자주 출제되는 오답 포인트**(예: CloudWatch vs CloudTrail 역할 혼동, Data event 비용/범위 착각 등)까지 묶어서 한 장 요약으로 만들어줄게요.
