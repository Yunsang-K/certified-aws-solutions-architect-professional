# AWS Control Tower

* Control Tower의 목적은 **멀티 계정(AWS multi-account) 환경을 빠르고 쉽게 구축**하도록 돕는 것입니다.
* Control Tower는 이 기능을 제공하기 위해 **여러 AWS 서비스를 오케스트레이션(조율)** 합니다.
* 내부적으로 **AWS Organizations, IAM Identity Center, CloudFormation, AWS Config** 등을 사용합니다.
* 본질적으로 **AWS Organizations를 발전(확장)시켜 거버넌스/자동화/지능형 기능을 추가한 서비스**로 볼 수 있습니다.

---

## Control Tower 주요 기능

* **Landing Zone(랜딩 존)**: 멀티 계정 환경의 기반(틀)

  * 다른 AWS 서비스를 통해 다음을 제공합니다:

    * **SSO / ID Federation(연합 인증)**
    * **중앙집중 로깅 및 감사(Auditing)**
* **Guardrails(가드레일)**: 모든 계정에 걸쳐 규칙/표준을 **강제하거나 위반을 탐지**하는 거버넌스 규칙
* **Account Factory**: 신규 계정 생성을 **자동화 및 표준화**
* **Dashboard**: 전체 환경을 한 화면에서 **가시성 있게 관리/감독**
* Control Tower 설정 시 기본으로 **2개의 OU(Organizational Unit)** 를 생성:

  * **Foundational OU(보안/기반 OU)**
  * **Custom OU(예: 샌드박스 OU)**
* Foundational OU 내부에 Control Tower가 **2개의 계정**을 생성:

  * **Audit Account**: Control Tower가 제공하는 **감사 정보에 접근**해야 하는 사용자를 위한 계정
  * **Log Archive Account**: 랜딩 존에 등록(Enrolled)된 모든 계정의 **로그를 중앙에서 보관/조회**하기 위한 계정
* Custom OU 내부에서는 Account Factory가 **자동으로 AWS 계정 프로비저닝**을 수행
* 신규 계정에는 표준 템플릿 기반 설정이 적용됨:

  * **Account Baseline 템플릿**
  * **Network Baseline 템플릿(쿠키 커터 방식)**
* Control Tower는 내부적으로 **CloudFormation**을 사용해 자동화를 구현
* 또한 **AWS Config + SCP(Service Control Policies)** 로 가드레일을 구현합니다.

---

## Landing Zones

* 누구나 **Well-Architected** 방식의 멀티 계정 환경을 구현할 수 있게 설계된 기능입니다.
* **Home Region**: 제품(Control Tower)을 최초로 배포하는 리전
* Landing Zone은 **Security OU와 Sandbox OU** 를 만들며, 추가 OU/계정도 생성할 수 있습니다.
* Landing Zone은 멀티 계정 및 연합 인증을 위해 **IAM Identity Center** 를 사용합니다.
* Landing Zone은 **CloudWatch + SNS** 로 모니터링 및 알림을 제공합니다.
* Service Catalog를 사용해 **최종 사용자(end-user)가 Landing Zone에서 신규 계정을 프로비저닝**하도록 허용할 수 있습니다.

---

## Guardrails

* 멀티 계정 거버넌스를 위한 규칙입니다.
* 규칙 유형은 3가지:

  * **Mandatory(필수)**
  * **Strongly Recommended(강력 권장)**
  * **Elective(선택)**
* Guardrails는 동작 방식이 2가지:

  * **Preventive(사전 차단)**: 특정 작업을 **하지 못하게 막음**

    * **SCP**로 구현
    * 상태는 보통 **Enforced(강제) / Not enabled(미사용)** 관점으로 봄
    * 예: 허용/차단 리전 제한, 조직 전체에서 S3 버킷 정책 변경 금지 등
  * **Detective(탐지/준수 검사)**: 규정 준수 여부를 **탐지**

    * **AWS Config Rules**로 컴플라이언스 위반을 감지
    * 상태는 **Clear(정상) / In violation(위반) / Not enabled(미사용)**
    * 예: CloudTrail 활성화 여부 점검, EC2에 퍼블릭 IPv4가 붙어있는지 점검 등

---

## Account Factory

* 계정을 **자동 프로비저닝**할 수 있게 해줍니다.
* 클라우드 관리자가 수행할 수도 있고, 권한이 있다면 **최종 사용자도 수행**할 수 있습니다.
* 프로비저닝된 계정에는 **Guardrails가 자동 적용**됩니다.
* 계정의 관리자(Account admin)는 계정을 생성한 사용자로 지정하거나 **다른 지정 사용자로 위임**할 수 있습니다.
* 계정은 표준 **Account/Network 구성**으로 설정할 수 있습니다.
* 기업의 **SDLC(개발 생명주기)** 와 완전하게 통합하는 형태로 운영할 수 있습니다.
