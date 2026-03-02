# AWS Resource Access Manager (RAM)

* **AWS 계정 간 리소스 공유**를 가능하게 하는 서비스
* 서비스에 따라 **어떤 AWS 계정과도 공유** 가능한 경우가 있고, **동일 AWS Organizations 내부 계정끼리만 공유** 가능한 경우도 있음(예: Subnet)
* **RAM을 지원하는 서비스만** 공유 가능(모든 리소스가 공유되는 것은 아님)
* 리소스를 공유할 대상(Principal)은 **계정(Account), OU, Organization** 단위로 지정 가능
* 공유된 리소스는 **각 서비스의 “네이티브 방식”으로 접근**(별도 프록시/복제 개념이 아니라, 공유된 형태로 그대로 사용)
* RAM 자체 사용 비용은 **없음**(단, 공유되는 서비스의 사용 요금은 발생 가능)
* Organizations 내부 공유 기능은 CLI의 `enable-sharing-with-aws-organizations`로 활성화 가능

  * 이 작업은 서비스 연결 역할(Service-linked role)인 **`AWSServiceRoleForResourceAccessManager`** 를 생성하고
  * 해당 역할에 IAM 관리형 정책 **`AWSResourceAccessManagerServiceRolePolicy`** 를 연결함
  * 이 역할은 RAM이 **Organizations 및 계정/OU 구조 정보를 조회**할 수 있도록 허용하며, 그 결과 **조직 전체(또는 OU 단위) 공유**가 가능해짐

---

## Availability Zone IDs

* 한 리전(Region)에는 여러 AZ가 존재함. 예: `us-east-1a`, `us-east-1b` 등
* **AZ 이름(예: us-east-1a)은 계정마다 매핑이 다를 수 있음**

  * 즉, 두 계정에서의 `us-east-1a`가 **물리적으로 같은 AZ를 의미하지 않을 수 있음**
* 하드웨어 레벨 장애가 발생하면, 계정마다 장애가 **서로 다른 AZ 이름으로 보이는 것처럼** 보여 트러블슈팅이 어려워질 수 있음
* 이를 해결하기 위해 AWS는 **AZ ID**를 제공함. 예: `use1-az1`, `use1-az2`
* **AZ ID는 여러 계정에서 일관되게 동일한 물리 AZ를 가리킴**

---

## RAM 핵심 개념

* **Owner account(소유자 계정)**

  * 리소스를 소유하며, 리소스 공유(Resource share)를 생성하고 공유 이름(Name)을 정의
  * 공유된 리소스에 대한 **완전한 권한은 계속 보유**
  * 특정 리소스를 공유할 대상 Principal(계정/OU/조직 전체)을 정의
* **Principal(공유 대상 주체)**

  * AWS 계정, OU, 또는 AWS Organizations 전체가 될 수 있음
  * 리소스는 Principal에게 공유됨
* 참여자 계정이 **같은 Organizations 내부**이고, **Organizations 공유가 활성화**되어 있으면 공유는 **자동 수락(Auto-accept)** 될 수 있음
* Organizations 외부 계정이거나 Organizations 공유가 비활성화된 경우에는 **초대(Invite)를 수동으로 수락**해야 함

> 원문에 “Principle”로 표기되어 있었는데, AWS 용어는 **Principal**이 정확합니다.

---

## Shared Services VPC

* 여러 계정/워크로드가 공통으로 사용하는 인프라를 제공하는 **공유용 VPC 패턴**
* 전통적으로는 별도 네트워크를 **VPC Peering** 또는 **Transit Gateway**로 연결하는 방식이 많았음

  * AWS RAM + AWS Organizations를 쓰면 더 단순하고 효율적으로 구성 가능
* VPC 소유자 계정이 VPC 및 Subnet을 생성/관리하고, 이를 **같은 Organizations의 참가 계정들에게 공유**할 수 있음
* 참가 계정은 공유된 Subnet에 **자신의 리소스를 프로비저닝**할 수 있고, 일부 네트워크 객체는 **참조(Read)** 가능하지만 Subnet 자체를 **수정/삭제는 불가**
* 참가 계정이 만든 리소스는:

  * 다른 참가 계정이나 VPC 소유자 계정에서 **직접 보이지 않을 수 있음(계정 단위 격리)**
  * 하지만 **동일 VPC/서브넷 네트워크 상**에 있으므로, 네트워크/보안 설정이 허용하면 **서로 통신/접근은 가능**

(이미지: `images/RAM.png`)
