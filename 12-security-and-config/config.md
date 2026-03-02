## AWS Config

* **AWS Config의 핵심 역할은 2가지**

  1. **주요 역할(Primary)**: AWS 리소스의 **구성(Configuration) 변경 이력**을 시간 흐름대로 기록

     * 리소스 구성이 변경될 때마다 **Configuration Item(CI)** 이 생성되며, 해당 시점의 변경 상태를 저장
  2. **보조 역할(Secondary)**: 변경에 대한 **감사(Auditing)** 및 **표준/정책 준수(Compliance)** 평가

* **중요 포인트**

  * Config는 **변경을 “막지” 않는다.**
    권한 제어/보호(Prevention) 제품이 아니며, 표준을 정의해도 **준수 여부를 “검사”** 할 뿐, 표준 위반을 **사전에 차단하지는 못한다.**

* **서비스 범위**

  * AWS Config는 **리전(Region) 단위 서비스**
  * **교차 리전 / 교차 계정 Aggregation(집계)** 지원

* **이벤트/알림 연동**

  * 변경 발생 시 **SNS 알림**을 보낼 수 있음
  * **EventBridge + Lambda**로 **준실시간 이벤트 처리** 가능

* **데이터 저장**

  * 변경 이력을 **일관된 포맷**으로 **S3(Config 전용 버킷)** 에 **장기 보관**

* **주의**

  * Config **레코딩(Recording)은 수동으로 활성화해야 함** (기본 자동 활성화 아님)

---

## AWS Config Rules

* **규칙 유형**

  * **AWS Managed Rules**(관리형)
  * **Custom Rules**(사용자 정의): **Lambda** 기반

* **동작 방식**

  * 리소스를 규칙으로 평가하여 **Compliant / Non-compliant** 판정
  * Custom Rule은 Lambda가 평가 로직을 수행하고 결과를 **Config에 반환**

---

## 자동 조치(Remediation) 연동

* Config 결과를 **EventBridge**로 받아 **Lambda 기반 자동 복구(Remediation)** 플로우 구성 가능
* 또는 **SSM(시스템 관리자) Automation**과 연동해 표준 조치 실행 가능

---

## AWS Config Remediations

* **Non-compliant 리소스**를 **SSM Automation 문서**로 **자동/수동** 복구 가능
* AWS Config는 **관리형(Managed) Automation 문서**(정해진 조치)를 제공
* 필요하면 **커스텀 Remediation 문서**도 작성 가능

---

## Conformance Packs

* **Conformance Pack** = 여러 **Config Rules + Remediation Actions**를 **한 번에 배포하는 묶음**
* 배포 범위

  * 특정 **계정/리전**
  * 또는 **AWS Organizations 전체(여러 계정/리전)** 로 확장 가능
* 작성 방식

  * **YAML 템플릿**으로 규칙/조치 목록을 정의해 배포
