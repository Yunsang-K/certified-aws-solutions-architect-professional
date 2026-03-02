# AWS Security Hub

## 1) 서비스 목적(What/Why)

* **AWS 환경 전반의 보안 상태를 통합 가시화**하는 서비스입니다.
* **보안 표준(Industry standards) 및 베스트 프랙티스** 대비 현재 구성을 평가(assessment)하고,
* **여러 계정/리전/서비스/서드파티**에서 올라오는 보안 데이터를 수집해 **우선순위 높은 이슈(Findings)** 를 중심으로 분석·추적하게 해줍니다.

> 핵심 포인트: Security Hub는 “통합 보안 대시보드 + 표준 기반 점검 + 파인딩 집계/우선순위화” 역할입니다.

---

## 2) 활성화(Enablement) / 운영 모델

* **기본적으로 “활성화해야(Enable) 사용”** 할 수 있습니다.

* 활성화 방식은 2가지:

  1. **AWS Organizations 연동(권장)**: 조직 단위로 중앙 운영이 가능
  2. **수동(개별 계정/리전 단위)**: 소규모 또는 PoC에 적합

* Organizations 연동 시:

  * **Delegated administrator(위임 관리자)** 계정을 지정할 수 있습니다.
  * 실제 중앙 운영/정책 관리를 이 계정이 담당합니다.

---

## 3) Central Configuration (멀티 계정/멀티 리전 중앙 설정)

Central Configuration은 **여러 계정과 리전에 걸쳐 Security Hub를 “정책 기반”으로 일관되게 구성**하는 기능입니다.

### 전제 조건(필수)

* **Security Hub ↔ AWS Organizations 연동 필수**
* **Home Region 지정 필수**

  * Home Region은 **Delegated admin이 조직 전체 설정을 “정의/배포”하는 기준 리전**입니다.
  * 설정은 Home Region에서 시작해 **연결된(Linked) 리전**들로 확장 적용됩니다.

### Configuration Policy(구성 정책)

Delegated administrator는 **Configuration policy**를 만들어 조직에 적용합니다. 이 정책에는 보통 아래가 포함됩니다.

* Security Hub 기능 설정(활성화 범위)
* 적용할 보안 표준(Security standards)
* 보안 컨트롤(Security controls) 구성

#### 정책 동작 특성(시험 포인트)

* **연결(Associate)되어야 적용**됩니다.
* **계정 또는 OU는 “단 하나의 정책”만 연결 가능**
* **정책은 “Complete specification(완전한 스펙)”**

  * 부분 패치 개념이 아니라 “이 정책이 곧 최종 상태”에 가깝습니다.
* **연결 후 되돌리기(Revert) 옵션 없음**

  * 운영상 변경은 “새 정책/수정 정책”으로 재연결하는 흐름이 됩니다.
* **Home Region + 모든 Linked Region에 적용**
* **정책 자체가 리소스이며 ARN을 가짐**

### 정책 유형

* **Recommended configuration policy**: AWS 제공 권장 설정(빠른 표준 적용에 유리)
* **Custom configuration policy**: 조직 요구사항에 맞춘 커스텀 정책

---

## 4) SAP 스타일로 자주 나오는 “구분 포인트”

* **Security Hub 자체는 중앙 “집계/평가” 서비스**이고,
  “실제 탐지 소스(예: GuardDuty, Inspector 등)”의 결과를 **Findings로 받아 통합**하는 형태로 자주 출제됩니다.
* 멀티계정 운영에서 키워드는:

  * **Organizations + Delegated admin + Central configuration + Home Region + Configuration policy(Associate/1개 정책/No revert)**

