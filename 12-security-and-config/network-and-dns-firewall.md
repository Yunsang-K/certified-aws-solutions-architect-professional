# AWS Network and DNS Firewalls 

## AWS Network Firewall

* **AWS Network Firewall**은 VPC에서 사용하는 **상태 저장(Stateful) 기반의 관리형 네트워크 방화벽 + IDS/IPS(침입 탐지/방지)** 서비스이다.
* VPC 내에 **관리형 네트워크 어플라이언스** 형태로 배치되어 **인바운드/아웃바운드 트래픽을 필터링**한다.
* VPC에서는 보통 **별도 서브넷(방화벽 서브넷)**에 배치되며, 해당 서브넷에 **Firewall Endpoint**가 생성된다.
* **유연한 룰 엔진**을 제공해 네트워크 트래픽에 대해 **세밀한 제어(정책 기반 허용/차단)**가 가능하다.
* 보호 대상 서브넷(워크로드 서브넷)에서 나온 트래픽을 **방화벽 서브넷으로 유도**하고, 방화벽이 검사/필터링 후 트래픽을 **목적 게이트웨이(Internet Gateway, Transit Gateway, Direct Connect, IPSec VPN 등)** 방향으로 전달하도록 설계한다.
* 방화벽 엔드포인트는 트래픽 처리를 위해 **Gateway Load Balancer(GWLB)** 아키텍처를 사용한다.
* 따라서 보호 대상 서브넷이 외부로 나갈 때 **라우팅 테이블을 구성**해 트래픽이 **GWLB(방화벽 엔드포인트)로 향하도록** 해야 한다.
* 대규모/조직 단위 운영에서는 **AWS Organizations + AWS Firewall Manager**로 배포/정책 관리를 중앙집중화할 수 있다.
* 주요 기능:

  * **Stateless / Stateful** 방화벽 정책
  * **IPS(Intrusion Prevention System)** 기능
  * **웹 필터링(Web filtering)**
* **방화벽 서브넷에는 일반 워크로드 리소스를 두지 않는 것이 원칙**(트래픽 검사 전용 경로로 설계).
* 고가용성(HA)을 위해 일반적으로 **AZ마다 방화벽 서브넷을 1개씩** 구성한다.

---

## Route 53 Resolver DNS Firewall

* **Route 53 Resolver DNS Firewall**은 VPC에서 발생하는 **아웃바운드 DNS 질의(Outbound DNS traffic)**를 **필터링/규제**하는 기능이다.
* VPC의 DNS 쿼리가 **Route 53 Resolver 경로로 통과**할 때 DNS Firewall 정책을 적용해 **허용/차단/모니터링**을 수행한다.
* 이를 통해 애플리케이션이 조회 가능한 **도메인을 통제**할 수 있고, 특히 **DNS를 통한 데이터 유출(DNS exfiltration)** 같은 행위를 완화하는 데 유용하다.
* 조직 단위로는 **AWS Firewall Manager**를 사용해 DNS Firewall 정책을 **중앙에서 구성/배포/관리**할 수 있다.
