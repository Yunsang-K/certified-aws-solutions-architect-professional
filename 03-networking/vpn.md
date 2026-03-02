# VPNs

## IPSEC VPN 기본

* **IPSEC**은 여러 프로토콜의 집합이다.
* 목적은 **신뢰할 수 없는 네트워크**(예: 공용 인터넷) 위에 **보안 터널**을 구성하는 것이다. 예: 공용 인터넷을 통해 두 개의 보안 네트워크(피어)를 연결
* IPSEC는 **인증(authentication)**을 제공한다.
* IPSEC를 통해 전송되는 트래픽은 **암호화**된다.
* IPSEC는 **비대칭 암호**로 **대칭 키**를 교환하고, 이후 지속적인 암호화에는 그 **대칭 키**를 사용한다.
* IPSEC는 2개의 주요 단계(Phase)로 구성된다.

### 1) IKE(Internet Key Exchange) Phase 1

* **느리고 무겁다**.
* 키를 어떻게 교환할지에 대한 프로토콜이다.
* 버전은 **v1(구버전)**, **v2(신버전)**가 있다.
* 비대칭 암호를 사용해 **공유 대칭 키를 합의·생성**한다.
* 이 단계의 결과물은 **IKE SA(Security Association) Phase 1 터널**이다.

### 2) IKE Phase 2

* **더 빠르고 민첩**하다.
* Phase 1에서 합의한 키를 사용한다.
* 대용량 데이터 전송에 사용될 **암호화 방식과 키** 합의에 집중한다.
* 이 단계의 결과물은 **IPSEC SA Phase 2 터널**이며, **Phase 1 위에서 동작**한다.

### 트래픽 매칭 방식 기준 VPN 유형

* **정책 기반(Policy-based) VPN**: 룰셋으로 트래픽을 매칭한다. 트래픽 유형별로 다른 룰을 둘 수 있다.
* **라우트 기반(Route-based) VPN**: **프리픽스(prefix)** 기준으로 매칭한다. 네트워크 프리픽스마다 **단일 SA 쌍**을 사용한다. (기능은 적지만 설정이 훨씬 단순)

---

## AWS Site-to-Site VPN

* **Site-to-Site VPN**은 공용 인터넷 위에서 동작하며, **IPSec으로 암호화**되는 **VPC ↔ 온프레미스 네트워크 간 논리적 연결**이다.
* 올바르게 구현하면 **완전한 HA 구성**이 가능하다.
* **프로비저닝이 빠르며**, 1시간 내에도 구성 가능하다(DX 대비).

### VPN 연결 구성 요소

* **VPC**
* **Virtual Private Gateway(VGW)**

  * 라우팅 테이블의 하나 이상의 규칙 타깃이 될 수 있는 **게이트웨이 객체**
  * **단일 VPC에만 연결(associate)** 가능
* **Customer Gateway(CGW)**: 두 의미로 쓰인다.

  * AWS 내의 **논리 구성(리소스)**
  * VPN이 연결되는 **온프레미스 라우터(물리 장비)**
* **VPN Connection**

  * AWS의 VGW와 온프레미스의 CGW를 연결하는 VPN 자체

### Static VPN vs Dynamic VPN

* **Static VPN**

  * 정적 네트워크 구성 사용
  * AWS 측 라우팅 테이블에 **정적 라우트 추가**
  * 온프레미스 측에서도 VPN 연결에 **대상 네트워크를 정적으로 식별**
  * 단순하고 어디서든 가능하지만, **로드밸런싱/다중 연결 페일오버**에 제한이 있다.
* **Dynamic VPN**

  * **BGP** 프로토콜 사용
  * 고객 라우터가 BGP를 지원하지 않으면 Dynamic VPN을 사용할 수 없다.
  * BGP는 라우팅을 동적으로 처리하며, 같은 위치 간 **다중 링크 동시 사용** 및 **HA 아키텍처** 구성이 가능하다.
  * 정적 라우트를 수동으로 추가하는 것도 가능
  * **Route propagation**을 활성화하면 라우트가 라우팅 테이블에 **자동 전파/추가**된다.

### VPN 고려사항

* 속도 제한(2개 터널 기준): **1.25Gbps (AWS 제한)**. 고객 라우터의 제한도 영향을 줄 수 있다.
* 지연 시간: 공용 인터넷 경유로 **일관되지 않을 수 있음**
* 비용: 시간당 비용 + 아웃바운드 트래픽 비용, 온프레미스 측 데이터 캡도 영향 가능
* 구축 속도: 매우 빠름(수시간 이내 가능). IPSec은 다양한 장비가 지원하지만, BGP 지원은 상대적으로 덜 흔함. VPN은 어떤 프라이빗 연결 기술보다도 빠르게 구축 가능
* VPN은 **Direct Connect 백업**으로 사용 가능하며, 또는 **Direct Connect 위에 VPN을 올려 암호화 레이어**를 추가할 수도 있다.

---

## Accelerated Site-to-Site VPN

* **AWS Site-to-Site VPN 성능 향상 옵션**으로, **AWS 글로벌 네트워크**(Global Accelerator/CloudFront가 사용하는 네트워크)를 활용한다.
* 일반 Site-to-Site VPN은 공용 인터넷을 경유한다. 이를 회피하려고 어떤 회사는 **Direct Connect 위에 VPN**을 올리기도 하지만 비용이 높다. DX가 어려운 환경을 위해 **Accelerated S2S VPN**이 더 나은 성능을 제공하도록 만들어졌다.
* **Transit Gateway attachment 생성 시에만 가속을 활성화 가능**

  * **VGW 기반 VPN에는 호환되지 않음**
* 비용은 **고정 accelerator 비용 + 데이터 전송 비용**으로 구성된다.

---

## Client VPN

* Site-to-Site VPN이 “사업장(사이트) ↔ AWS VPC” 연결이라면, **Client VPN은 개별 클라이언트(사용자 단말) ↔ AWS** 연결에 가깝다.
* **Client VPN**은 관리형 **OpenVPN 구현**이다.
* OpenVPN 클라이언트를 사용할 수 있는 어떤 디바이스든 지원한다.
* 아키텍처상 **Client VPN endpoint**로 연결하며, 이 엔드포인트는 **하나의 VPC** 및 **1개 이상 Target Network(고가용성)**와 연동된다.
* 과금은 **네트워크 연동(association)** 및 **사용 시간 기반 hourly fee**로 산정된다.

### Client VPN 설정

* Client VPN Endpoint를 만들고, VPC 및 VPC의 **1개 이상 서브넷**과 연결(associate)한다.
* 이 연결(association)은 선택된 서브넷에 **ENI**를 생성한다.
* **AZ당 서브넷 1개만 선택 가능**하다.
* 인증 방식: 인증서, Cognito User Pool, Federated Identities, AWS Directory Service 등 다양한 방식 지원
* Client VPN Endpoint에 **라우팅 테이블을 연결**해 라우팅/연결성을 설정한다. (예: NAT Gateway를 통한 인터넷, VPC Peering으로 다른 VPC 등)
* 이 라우팅 테이블은 Client VPN에 접속한 클라이언트에게 **푸시**된다.
* 기본 동작: Client VPN 라우팅 테이블이 클라이언트의 로컬 라우트를 **대체**한다.
  → 따라서 기본 상태에서는 클라이언트가 **자기 로컬 네트워크 자원에 직접 접근**하지 못하고, Client VPN Endpoint 경유가 필요해진다.
* **Split tunnel**을 사용하면, Client VPN 라우트가 클라이언트 로컬 라우팅 테이블에 **추가**되는 형태가 된다.
  → 기본 동작의 문제를 해결한다.
* Split tunnel은 **기본값이 아니며**, 사용자가 활성화해야 한다. 활성화하지 않으면 **공용 인터넷 트래픽 포함 모든 데이터가 터널을 통해** 흐른다.
