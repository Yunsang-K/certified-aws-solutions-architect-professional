## AWS Shield (DDoS Protection)

* **AWS Shield**는 AWS 리소스를 대상으로 하는 **DDoS(분산 서비스 거부) 공격**을 완화하기 위한 서비스입니다.
* AWS가 설계한 **표준화된 DDoS 보호 체계**를 제공하며, 특히 **인프라 계층(L3/L4)** 공격에 강점이 있습니다.
* **알려진 인프라 계층 DDoS 공격(L3/L4)**을 대상으로 보호합니다.

  * L3 예: **대역폭 소진(Volumetric) 공격**
  * L4 예: **프로토콜/세션 악용 공격**, **TCP SYN Flood**

---

## 1) Shield Standard

* **모든 AWS 고객에게 무료 제공**
* 보호는 AWS 네트워크 **경계(perimeter)**에서 수행됩니다.

  * 일반적으로 **리전/VPC 경계** 또는 (CloudFront 사용 시) **엣지(Edge)** 구간
* **일반적인 L3/L4 네트워크·전송 계층 공격**에 대한 기본 완화 제공
* **Route 53, CloudFront, Global Accelerator**를 함께 사용할 때 보호 효과가 가장 큽니다.
* 사용자가 “직접 설정하는” 형태의 **사전/능동적 구성(커스텀 튜닝, 특정 리소스 지정 등)** 기능은 거의 없습니다.

---

## 2) Shield Advanced

* **요금**: 조직(Organization) 단위로 **월 $3,000**, **1년 약정**

  * (설명하신 대로) 월별 데이터 아웃/추가 과금 구조가 붙을 수 있음
* **계정 단위가 아니라 Organization 단위**이므로, 여러 계정을 보호하려면 **같은 AWS Organizations**에 있어야 운영이 깔끔합니다.
* 보호 대상 리소스 범위가 확장됩니다.

  * **CloudFront, Global Accelerator, Route 53**
  * **EIP가 붙은 리소스(예: EC2)**
  * **로드 밸런서(ALB/CLB/NLB)**

### 운영 포인트

* Shield Advanced의 보호는 **자동으로 “다 알아서” 적용되는 형태가 아니라**,
  **Shield Advanced에서 리소스를 명시적으로 보호 대상으로 활성화**하거나,
  **AWS Firewall Manager의 Shield Advanced 정책**으로 배포해야 합니다.

### 핵심 추가 혜택

* **AWS Shield Response Team(SRT)**: 24/7 고급 대응 지원
* **DDoS로 인한 비용 증가에 대한 재정적 보호(비용 보호/크레딧 성격의 보험 기능)**
* **실시간 DDoS 이벤트 가시성(대시보드/알림 기반)**
* **Health-based detection**: 애플리케이션 헬스체크 기반으로 이상 징후를 더 빠르게 탐지/완화할 수 있게 지원
* **Protection groups**

  * Shield Advanced로 보호할 리소스를 **그룹 단위로 관리**
  * 그룹의 **멤버십 기준(규칙)**을 정의하면, 새 리소스가 자동으로 그룹에 포함되도록 구성 가능

### WAF 통합

* Shield Advanced는 **AWS WAF와 연계**되어 **L7(애플리케이션 계층) 공격** 방어를 보완합니다.
* 또한 **기본 WAF 비용(웹 ACL, 룰, 웹 요청)**이 포함되는 혜택이 있습니다(시험에서는 “WAF 비용 포함” 포인트로 자주 출제).

---

### 시험 관점 한 줄 정리

* **L3/L4 DDoS 기본 방어 = Shield Standard(무료)**
* **SRT + 비용보호 + 리소스 지정 보호 + WAF 연계(L7 보완) = Shield Advanced(유료/조직 단위)**
