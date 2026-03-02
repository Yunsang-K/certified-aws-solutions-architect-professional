# AWS Trusted Advisor

* AWS 모범 사례(Best Practices)에 따라 리소스를 프로비저닝하도록 **실시간 가이드**를 제공하는 서비스

* **계정(Account) 단위** 서비스이며, 별도 에이전트 설치가 필요 없음

* 5가지 주요 영역에서 다양한 **체크(Check)** 및 **권장사항(Recommendation)** 제공

  * 비용 최적화(Cost Optimization & Recommendations)
  * 성능(Performance)
  * 보안(Security)
  * 내결함성(Fault Tolerance)
  * 서비스 한도(Service Limits)

* Trusted Advisor는 “제대로” 활용하려면 **유료(지원 플랜 영향)** 성격이 강함

* **Basic / Developer Support 플랜**에서는 **무료 버전**(제한된 체크)만 사용 가능

* 무료 버전은 **7개 핵심 체크(시험용 암기)** 제공

  * S3 버킷 권한(공개 액세스 여부)
  * 보안 그룹: 특정 포트가 무제한(0.0.0.0/0 등)으로 개방되어 있는지
  * IAM 사용(모범 사례 관점의 IAM 관련 체크)
  * 루트 계정 MFA 설정 여부
  * EBS 퍼블릭 스냅샷 여부
  * RDS 퍼블릭 스냅샷 여부
  * 서비스 한도(Service Limits) 50개 체크: 자주 쓰는 50개 한도에 대해 **80% 이상 사용** 시 식별

* 위 7개를 넘어서는 체크는 **Business 또는 Enterprise Support 플랜**이 필요

* Business/Enterprise에서는 추가로 **약 115개 체크** 제공

* 또한 **AWS Support API** 사용 가능

## AWS Support API (Trusted Advisor 관련)

* AWS Support 기능을 **프로그래밍 방식**으로 접근 가능

  * AWS가 제공하는 체크의 이름/식별자 조회
  * Trusted Advisor 체크를 계정/리소스에 대해 실행 요청
  * 요약 및 상세 결과를 프로그램으로 조회
  * Trusted Advisor **리프레시(Refresh)** 요청 가능
* Support API로 **서포트 티켓 생성 및 관리**도 가능
* Business/Enterprise에서는 **CloudWatch 연동** 제공
  → 이벤트 기반으로 결과에 대응하는 자동화(알림/조치) 구성 가능

---

## AWS Support Plans (Trusted Advisor 관점)

* **Basic Support**

  * 모든 AWS 고객에게 기본 제공(무료)
  * Trusted Advisor는 **7개 코어 체크만** 제공
* **Developer Support**

  * Trusted Advisor는 **동일하게 7개 코어 체크만** 제공
* **Business Support**

  * Trusted Advisor **전체 체크/권장사항 세트** 제공
  * Trusted Advisor에 대한 **프로그램 접근(Support API)** 가능
* **Enterprise Support**

  * Trusted Advisor 기준으로는 Business와 **동급(전체 기능)**

---

## Good to Know

* S3 버킷이 퍼블릭인지(버킷 레벨)는 확인 가능하지만, **버킷 내부 오브젝트가 퍼블릭인지(오브젝트 레벨)** 까지는 Trusted Advisor에서 직접 확인 불가
  → 이 모니터링은 CloudWatch Events / S3 Events 등으로 보완
* **Service Limits**

  * 한도 모니터링은 Trusted Advisor에서 가능
  * 한도 상향은 **AWS Support Center에서 케이스를 수동 생성**해서 요청해야 함
