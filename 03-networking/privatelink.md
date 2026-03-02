# AWS PrivateLink

* 다른 **AWS 계정**에서 호스팅되는 서비스에 **프라이빗 연결**을 제공한다.
* 직접 연결하거나, **AWS Marketplace 파트너 서비스**를 통해 소비(consume)할 수도 있다.
* 두 경우 모두, 소비자 VPC 관점에서는 해당 서비스가 **프라이빗 IP 주소와 ENI(Elastic Network Interface)** 형태로 VPC 내부에 “있는 것처럼” 보인다.
* AWS PrivateLink는 **Interface Endpoint(인터페이스 VPC 엔드포인트)** 의 기술적 기반이다.
* 고가용성(HA)을 위해 **엔드포인트를 다중 AZ에 분산**해야 한다. 일반적으로 **소비가 필요한 각 AZ마다 1개(서브넷 1개당 1개 권장)** 를 둔다.
* PrivateLink는 **IPv4 및 TCP** 를 지원한다. *(IPv6는 현재 지원됨 — 2022년 5월 업데이트 참고)*
* **Private DNS** 를 지원한다. (소비하는 서비스가 퍼블릭 DNS를 제공하는 경우, 이를 **프라이빗 엔드포인트로 오버라이드** 가능)
* PrivateLink 엔드포인트는 **Direct Connect, Site-to-Site VPN, VPC Peering** 경로를 통해서도 접근할 수 있다.

---

## VPC Endpoints

### Gateway Endpoints

* 지원 서비스(**S3, DynamoDB**)에 대해 **프라이빗 액세스**를 제공한다.
* “인터넷 없이” 프라이빗 전용 VPC 리소스가 S3/DynamoDB에 접근하도록 한다.
* 리전/서비스 단위로 게이트웨이 엔드포인트를 생성하고, VPC의 **하나 이상의 라우트 테이블**(실무적으로는 해당 서브넷의 라우트 테이블)에 연결한다.
* 게이트웨이 엔드포인트를 연결하면 라우트 테이블에 **Prefix List** 대상 경로가 추가된다.
* S3/DynamoDB 목적지 트래픽은 **IGW/NAT가 아니라 게이트웨이 엔드포인트**로 라우팅된다.
* 게이트웨이 엔드포인트는 리전 내 AZ 전반에 **고가용**이며, ENI처럼 서브넷 안에 “박히는” 형태가 아니다.
* **Endpoint policy** 로 “엔드포인트를 통해 접근 가능한 범위”를 제한할 수 있다. (예: 특정 S3 버킷만 허용)
* **동일 리전 내 서비스**에 대해서만 사용한다.
* S3 버킷 정책을 게이트웨이 엔드포인트 기반으로 제한하면, 의도치 않은 공개/유출 위험(이른바 *Leaky Bucket*)을 줄일 수 있다.
* 게이트웨이 엔드포인트는 **논리적 게이트웨이 객체**이며, **연결된 VPC 내부에서만** 접근된다.

---

### Interface Endpoints

* Gateway Endpoint와 유사하게 AWS 퍼블릭 서비스를 **프라이빗**하게 접근하게 한다.
* 역사적으로 S3/DynamoDB 외 서비스용으로 주로 쓰였고, 최근에는 **S3도 Interface Endpoint로 접근** 가능해졌다.
* Gateway Endpoint와의 핵심 차이: Interface Endpoint는 **기본적으로 HA가 아니다.** 각 서브넷에 **ENI로 생성**된다.
* HA를 위해서는 VPC 내 **각 AZ의 서브넷마다 Interface Endpoint를 배치**하는 구성이 일반적이다.
* Interface Endpoint에는 **Security Group** 을 연결할 수 있다. (Gateway Endpoint는 SG를 붙이지 못함)
* Interface Endpoint에도 **Endpoint policy** 를 적용할 수 있다.
* (원문 기준) **IPv4/TCP** 기반으로 동작하며, 내부적으로 **PrivateLink** 를 사용한다.
* 동작 방식 차이:

  * Gateway Endpoint: **Prefix List + Route Table**
  * Interface Endpoint: **DNS 기반**
* Interface Endpoint는 서비스 접근을 위한 **새 DNS 이름**을 제공한다.

  * **Endpoint Region DNS**
  * **Endpoint Zonal DNS**
  * **Private DNS**: 기존 서비스 기본 DNS를 **인터페이스 엔드포인트로 향하도록 오버라이드**

---

## VPC Endpoint Policies

* Endpoint policy만으로는 AWS 서비스에 대한 접근 권한을 “부여”하지 않는다.
* 실제 접근 주체(IAM 사용자/역할)는 여전히 해당 리소스에 대한 **IAM 권한**이 필요하다.
* Endpoint policy는 **그 서비스에 접근할 때 ‘해당 엔드포인트를 경유하는 접근’에 한해** 추가로 제한을 건다.
* 엔드포인트 정책은 일반적으로 “누가/무엇을/어떤 조건에서” 접근 가능한지에 대한 **정책 + 조건**으로 구성된다.
* 보통 **프라이빗 VPC가 접근 가능한 대상 범위를 최소화**하는 용도로 사용한다.
