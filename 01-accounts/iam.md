# IAM: Identity and Access Management (한국어 정리)

## IAM 개요

* AWS에 접근할 때 **루트(root) 계정은 절대 사용하지 않는다.**

  * 사람/시스템에는 반드시 **필요 권한만 가진 IAM 사용자/역할**을 만들어 사용한다.
* IAM은 AWS에서 **인증(Authentication)과 권한부여(Authorization)**의 중심 서비스다.

## IAM 구성 요소

* **Users(사용자)**

  * 실제 사람(개인) 1명에 대응하는 엔티티

* **Groups(그룹)**

  * 여러 사용자를 묶는 단위
  * 예: 기능별(admin, devops), 팀별(engineering, design) 등

* **Roles(역할)**

  * 주로 **AWS 리소스(서비스/애플리케이션/워크로드)가 사용**하는 권한 단위
  * 고정 자격증명(Access key)을 들고 다니기보다, **필요할 때만 AssumeRole로 임시 자격증명**을 받는 구조에 적합
  * **Cross-account Role(크로스 계정 역할)**

    * 다른 AWS 계정의 주체가 **우리 계정 리소스에 접근하기 위해 Assume(역할 전환)하는 역할**

* **Policies(정책, JSON 문서)**

  * 위의 사용자/그룹/역할이 **무엇을 할 수 있고 못 하는지** 정의하는 권한 문서
  * IAM에는 AWS가 미리 제공하는 **관리형(Managed) 정책**들이 존재
  * 정책 종류(질문에서 자주 분리해서 나옴)

    * **AWS Managed Policy**: AWS가 관리/업데이트
    * **Customer Managed Policy**: 사용자가 생성하고 재사용 가능
    * **Inline Policy**: 특정 사용자/그룹/역할에 1:1로 붙는 정책(재사용/중앙관리 어려움)

* **Resource-based Policies(리소스 기반 정책)**

  * IAM 사용자/역할에 붙이는 게 아니라, **S3 버킷 정책, SQS 큐 정책처럼 “리소스 자체에” 붙는 정책**
  * 주로 크로스 계정 액세스에서 핵심으로 사용됨

---

## IAM Role vs Resource-based Policy (차이)

* **Role을 Assume(역할 전환)하면**

  * 원래 갖고 있던 권한을 “유지하는 게 아니라”
  * **전환된 역할(Role)의 권한으로만** 동작한다(임시 자격증명 기반)

* **Resource-based policy를 쓰면**

  * 요청 주체(principal)가 **자기 권한을 유지한 채로**
  * 대상 리소스에서 “이 principal을 허용”해 줄 수 있다

### 예시(의도 정리)

* 계정 A의 사용자가 **계정 A의 DynamoDB 테이블을 Scan**하고, 결과를 **계정 B의 S3 버킷에 저장**해야 한다.
* 여기서 계정 B의 Role로 Assume해버리면, 그 순간 권한 컨텍스트가 B Role로 바뀌어 **A 테이블 Scan 권한이 없어질 수 있다.**
* 실무/시험에서 흔한 해결 방향은:

  * **S3 버킷(계정 B)에 resource-based policy로 계정 A principal(또는 A의 Role)을 허용**하고,
  * DynamoDB Scan은 계정 A 권한으로 수행하면서 S3 PutObject만 B 버킷 정책으로 허용받는 형태(또는 양쪽에 맞는 역할 설계)

---

## Best practices (모범 사례)

* **사람 1명당 IAM User 1개만**
* **애플리케이션(워크로드) 1개당 IAM Role 1개**(EC2/Lambda/ECS 등에서 역할 사용)
* IAM 자격증명(비밀번호/Access key)은 **절대 공유 금지**
* 자격증명을 코드에 하드코딩하는 일은 **절대 금지**
* 루트 계정은 **초기 설정 등 꼭 필요한 경우 외 사용 금지**
* 사용자/시스템에는 업무 수행에 필요한 **최소 권한(Least Privilege)**만 부여

