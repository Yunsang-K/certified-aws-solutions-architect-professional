# DX - Direct Connect

* AWS 네트워크로 들어가는 **물리적 진입점(Physical entry point)** 이다.
* 퍼블릭 인터넷을 거치지 않고 AWS 서비스에 접근할 수 있도록 하는 **물리적 광(파이버) 회선**이다.
* 연결 경로는 **사업장(온프렘) → DX Location → AWS Region** 이다.
* Direct Connect를 주문할 때 실제로 주문하는 것은 DX Location에서의 **포트 할당(Port Allocation)** 이다.
* 포트 비용은 2가지로 구성된다.

  * DX Location과 포트 속도에 따른 **시간당 비용(Hourly cost)**
  * **아웃바운드 데이터 전송 비용(Outbound data transfer)** (인바운드는 무료)
* Direct Connect를 사용하려면 AWS 계정 내에서 **Connection(논리 엔터티)** 을 생성해야 하며, 이 Connection에는 **고유 ID**가 부여된다.
* 프로비저닝 기간: **수주~수개월**이 추가로 걸릴 수 있다.
* DX는 **낮고 일관된 지연시간 + 고속(1/10/100Gbps)** 을 제공한다.
* DX Location에서는 고객 네트워크를 AWS 네트워크에 연결하기 위해 **크로스 커넥트(Cross-connect, 물리 케이블)** 를 설치해야 한다.

![DX architecture](images/DirectConnectArchitecture1.png)

---

## DX Physical Connection Architecture

* Direct Connect는 DX Location에서 할당되는 **물리 포트**다.
* 이 물리 포트는 **1, 10, 100Gbps** 속도를 제공한다.
* DX는 AWS에서 직접 주문하거나 파트너를 통해 주문할 수 있다(파트너는 더 다양한 속도 옵션을 제공하는 대신 옵션 제약이 있을 수 있음).
* 포트는 **싱글모드 광섬유(Single-mode fiber)** 가 필요하며 **구리 케이블은 지원되지 않는다.**
* 주문한 속도에 따라 다음 표준으로 DX에 인터페이스한다.

  * **1000BASE-LX** (1310 nm) 트랜시버: 1Gbps
  * **10GBASE-LR** (1310 nm) 트랜시버: 10Gbps
  * **100GBASE-LR4**: 100Gbps
* **Auto-Negotiation은 비활성화**해야 한다.
* 포트 속도를 구성하고, 네트워크 연결은 **Full-duplex를 수동 설정**해야 한다.
* DX Location의 라우터는 **BGP** 및 **BGP MD5 Authentication**을 지원해야 한다.
* 선택 구성:

  * MACsec
  * Bidirectional Forwarding Detection (BFD)

---

## Direct Connect - MACsec

* DX의 오래된 문제인 **기본 내장 암호화 부재**를 개선(부분적으로 해결)하는 보안 기능이다.
* OSI 2계층(L2)에서 프레임을 암호화하는 표준이다. (프레임은 L2에서 전송되는 데이터 단위)
* MACsec은 **홉-바이-홉(hop-by-hop)** 암호화 방식이다.
  → MACsec을 사용하려면 **L2 인접성(Layer 2 adjacency)** 으로 두 장비가 서로 이웃해야 한다.
* MACsec 기능:

  * **기밀성(Confidentiality)**: EtherType과 Payload를 포함한 프레임을 L2에서 강력하게 암호화
  * **무결성(Data integrity)**: 전송 중 변조를 양측이 탐지할 수 있도록 추가 필드를 포함
  * **송신자 진위(Data origin authenticity)**: 프레임이 신뢰된 피어로부터 왔음을 상호 확인
  * **재전송(Replay) 방지**
* MACsec은 **DX 위의 IPsec VPN을 대체하지 않는다.** (엔드-투-엔드가 아님)
* 테라비트급 네트워크에서도 사용될 수 있도록 **초고속 전송**을 염두에 둔 설계다.
* MACsec 주요 구성요소:

  * **Secure Channel(단방향)**: 각 참여자가 트래픽 송신을 위한 MACsec 채널을 생성
  * **SCI(Secure Channel Identifier)**: Secure Channel을 고유하게 식별
  * **Secure Associations(SA)**: 각 Secure Channel 위에서 일어나는 세션(일시적).
    연결 수명 동안 여러 SA가 발생할 수 있으며 보통 한 번에 1개 SA가 활성(교체 시 예외)
  * **MACsec encapsulation**: 16바이트 태그 + 16바이트 ICV(Integrity Check Value) 삽입
  * **MACsec Key Agreement**: 발견/인증/키 생성 관리
  * **Cipher Suite**: 암호 알고리즘, 키당 패킷 수, 키 로테이션 등을 제어
* MACsec은 **단일 DX Connection** 또는 **LAG(Link Aggregation Group)** 에서 정의할 수 있다.
* MACsec 구성: AWS DX 라우터(들)와 고객 라우터 양측에 **CAK/CKN 페어**를 연결에 연동한다.
* DX Location에서 고객 사업장까지 MACsec을 확장하는 것도 가능하다.

  * 단, 이를 위해 **크로스커넥트를 사업장까지 물리적으로 전용 연장**해야 하며, 이 방식은 **L2 인접성**이 필요하다.

---

## DX Connection Process

* DX Connection은 AWS 장비와 고객/사업자 장비가 함께 있는 **DX Location**에서 시작한다.
* AWS는 이 시설을 소유하지 않는다(사업자도 동일). 일반적으로 **제3자 데이터센터**가 소유한다.
* 대규모 데이터센터는 여러 **케이지(cage)** 로 구성되며, 각 케이지는 고객이 임대하는 구역이다.
* 케이지 사이의 공간은 데이터센터 직원만 접근 가능하며, 케이지 간 케이블 연결 역시 **데이터센터 직원만** 수행할 수 있다.
* 서로 다른 고객 케이지를 연결하려면 모든 당사자의 승인이 필요하며, 이 승인 문서를 **LOA-CFA(Letter of Authorization - Customer Facility Access)** 라고 한다.
* LOA-CFA는 데이터센터 직원이 한 고객의 요청으로 다른 고객의 장비에 연결 작업을 수행할 수 있도록 허가하는 문서다.
* DX 연결 절차:
  ![DX Connection Process](images/DXConnectionProcess.png)

---

## DX Virtual Interfaces BGP Sessions + VLAN

* DX Connection은 **L1(물리)** 연결이지만, 그 위에서 **L2(데이터 링크)** 프로토콜이 동작한다.
* 단일 DX 연결 위에서 여러 종류의 **L3(IP) 네트워크(VPC, Public Zone 등)** 로 연결하려면 **VIF(Virtual Interface)** 가 필요하다.
* VIF는 사실상 **BGP 피어링 세션**이다.
  → 프리픽스(prefix)를 교환하여 트래픽이 양 끝단을 오가도록 라우팅한다.
* 이 모든 것은 **VLAN** 안에서 격리된다.
* VLAN은 태깅을 통해 서로 다른 L3 네트워크를 분리한다.
* BGP는 프리픽스를 교환하며, BGP MD5 인증은 인증된 데이터만 수락되도록 한다.
* DX에서 운용 가능한 VIF 타입 3가지:

  * **Private VIF**: AWS 프라이빗 네트워크(VPC) 연결
  * **Public VIF**: Public Zone 서비스 연결
  * **Transit VIF**: DX와 Transit Gateway 통합
    ![DX VIFs - BGP Session + VLAN](images/DXBGPSessonVLAN.png)
* 하나의 Dedicated DX는 총 **Public/Private VIF 합산 최대 50개**, 그리고 **Transit VIF 1개**를 가질 수 있다.
* Hosted VIF: VIF를 다른 AWS 계정과 공유 가능. (상대 계정의 VPC에 있는 VGW로 연결 가능)

---

## Private VIFs

* **하나의 AWS VPC 내부 리소스**에 **프라이빗 IP**로 접근할 때 사용한다.
* Private VIF로는 리소스의 프라이빗 IP로만 접근 가능하며 **퍼블릭 IP/Elastic IP는 동작하지 않는다.**
* Private VIF는 **VGW(Virtual Private Gateway)** 와 연동되며, VGW는 **1개 VPC에만 연결**된다.
  또한 DX 종단(연결 종료)되는 **동일 리전에 있어야 한다.**
* 관계 요약: **1 Private VIF = 1 VGW = 1 VPC** (Transit VIF 등으로 우회 가능)
* Private VIF에는 기본적으로 암호화가 없다(DX도, Private VIF도 암호화를 제공하지 않음).
  (대안 예: HTTPS 등 애플리케이션 계층 암호화)
* Private VIF는 **일반 프레임(MTU 1500)** 또는 **점보 프레임(MTU 9001)** 사용 가능
* VGW를 사용하면 **Route propagation이 기본 활성화**된다.
* Private VIF 생성 절차:

  * VIF를 올릴 Connection 선택
  * VGW(기본) 또는 Direct Connect Gateway 선택
  * 인터페이스 소유자 선택(본 계정 또는 타 계정)
  * VLAN ID 선택(802.1Q, 고객 측 설정과 일치 필요)
  * 온프렘 BGP ASN 입력(공인/사설 가능). 사설 ASN 사용 시 **64512~65535**
  * 피어 IP를 직접 지정하거나 자동 생성
  * AWS는 VPC CIDR 및 BGP 피어 IP(`/30`)를 광고(advertise)한다
  * 온프렘 측은 기본 라우트 또는 특정 사내 프리픽스를 광고할 수 있음(**최대 100개**. 하드 리밋 초과 시 인터페이스가 idle 상태로 전환)
* Private VIF 아키텍처:
  ![Private VIFs Architecture](images/DXPrivateVIFS.png)
* 핵심 포인트:

  * Private VIF는 **프라이빗 AWS 서비스 접근**에 사용
  * **Private VIF → 1 VGW → 1 VPC**
  * VPC는 DX 종단과 **같은 리전**
  * VGW는 AWS가 할당(관리)
  * Private VIF 위에서 IPv4/IPv6 BGP가 동작(각각 별도 피어링)
  * VIF에 구성하는 ASN은 사설/공인 모두 가능

---

## Public VIFs

* AWS **퍼블릭 존 서비스**(퍼블릭 서비스 및 퍼블릭 Elastic IP를 가진 서비스)에 접근할 때 사용한다.
* VPC 내부 **프라이빗 서비스에 대한 직접 접근은 제공하지 않는다.**
* Public VIF 하나로 AWS 글로벌 네트워크를 통해 **모든 리전의 Public Zone**에 접근할 수 있다.
* AWS는 모든 AWS 퍼블릭 IP 범위를 고객에게 광고하며, AWS 서비스로 가는 트래픽은 AWS 글로벌 네트워크를 통해 전달된다.
* 고객은 BGP를 통해 자신이 보유한 퍼블릭 IP 프리픽스를 광고할 수 있다. (퍼블릭 IP가 없으면 AWS Support와 협의해 할당받을 수 있음)
* Public VIF는 **양방향 BGP Community**를 지원한다.
* 광고된 프리픽스는 **트랜짓되지 않는다**(고객 프리픽스는 AWS 밖으로 나가지 않음).
* Public VIF 생성 절차:

  * VIF를 올릴 Connection 선택
  * 인터페이스 소유자 선택(본 계정 또는 타 계정)
  * VLAN 선택(802.1Q, 고객 설정과 일치 필요)
  * 고객 측 BGP ASN 선택(공인 ASN이 Public VIF 기능을 최대한 활용하기에 권장)
  * MD5 인증 구성 및 피어링 IP(옵션) 선택
  * 광고할 프리픽스 선택
* Public VIF 아키텍처:
  ![Public VIFs Architecture](images/DXPublicVIFS.png)

---

## Direct Connect Public VIF + VPN

* VPN을 사용하면 **암호화 및 인증된 터널**을 제공한다.
* 아키텍처 관점에서 DX 위 VPN은 **Public VIF + VGW/TGW 퍼블릭 엔드포인트**를 사용한다.
* VPN은 VGW 또는 TGW의 **퍼블릭 IP**에 연결한다.
* VPN은 전송 경로에 독립적이다(인터넷을 통해서도, DX를 통해서도 VGW/TGW에 VPN 연결 가능).
* VPN은 **CGW(Customer Gateway) ↔ TGW/VGW** 간 **엔드-투-엔드 암호화**이고, MACsec은 **단일 홉 기반**이다.
* VPN은 다양한 벤더를 폭넓게 지원한다.
* VPN은 MACsec 대비 암호화 오버헤드가 더 크다.
* VPN은 즉시 제공 가능하며, DX 프로비저닝 기간 동안 임시로 사용하거나 DX 백업으로 사용할 수 있다.
  ![VPN over DX](images/DXPublicVIFVPN.png)

---

## Direct Connect Gateways

* Direct Connect는 기본적으로 **리전 단위 서비스**다.
* DX 연결이 구성되면 Public VIF로 모든 리전의 AWS 퍼블릭 서비스에 접근할 수 있다.
* Private VIF는 VGW를 통해 **같은 리전의 VPC만** 접근 가능하다.
* **Direct Connect Gateway(DXGW)** 는 **글로벌** 네트워크 디바이스로, 모든 리전에서 접근 가능하다.
* 온프렘 측은 Private VIF를 만들 때 VGW 대신 **DXGW에 연결**하여 온프렘 라우터와 DXGW를 통합한다.
* AWS 측에서는 어떤 리전의 VPC든 **VGW Association** 을 생성할 수 있다.
* DXGW는 온프렘 ↔ AWS 사이 라우팅을 제공하지만, **DXGW에 연결된 VPC들끼리 상호 통신은 허용하지 않는다.**
* DXGW 하나당 **VGW 연결(Association) 10개**까지 가능
* 1 DX Connection은 최대 50개의 Private VIF를 가질 수 있고, 각 VIF는 1 DXGW를 지원하며, DXGW는 10개 VGW Association을 지원
  → 이론상 **최대 500 VPC**까지 연결 가능
* DXGW 자체 비용은 없고, **데이터 전송(Transit) 비용**만 발생한다.
  ![DX Gateway Architecture](images/DirectConnectGateway3.png)
* Cross-account DXGW: 여러 계정이 DXGW에 대해 Association proposal을 생성할 수 있다.

---

## Transit VIFs and TGW

* DXGW는 연결된 VPC들 간 라우팅을 제공하지 않고, **온프렘 ↔ AWS** 방향 라우팅만 제공한다.
* **Transit Gateway(TGW)** 는 **리전 단위** 서비스이며, TGW 간 피어링으로 리전 간 연결이 가능하다.
* TGW는 **허브-스포크** 아키텍처로, TGW에 연결된 것들끼리는 상호 통신이 가능하다.
* 이 구조는 피어링된 TGW 간에도 동일하게 적용된다.
* DX-TGW 아키텍처:
  ![DX-TGW Architecture](images/DXGateway4.png)
* DX는 Public/Private VIF 합산 최대 50개, Transit VIF는 1개만 지원한다.
* **DXGW 하나에 최대 3개의 TGW**를 연결할 수 있다.
* 단, **하나의 DXGW는 (VPC+Private VIF) 방식 또는 (TGW+Transit VIF) 방식 중 하나만** 가능하며, **둘을 동시에 사용할 수 없다.**
* 고려사항:

  * DXGW는 어태치먼트 간 라우팅을 하지 않으므로, 이를 보완하려면 **TGW 피어링**이 필요하다.
  * TGW는 최대 20개의 DXGW에 연결 가능
  * TGW는 최대 5000 어태치먼트, 피어링 어태치먼트는 최대 50개 지원
* DXGW 라우팅 제약:

  * DXGW는 Private VIF ↔ 연결된 VGW 간 통신만 허용
  * Transit Gateway를 사용하면(특정 리전에서) DXGW를 TGW에 연결해 이를 해결할 수 있다.

---

## Direct Connect Resilience and HA

* 복원력 향상 방법:

  * 포트 1개 대신 **DX 포트 2개** 주문
    → 크로스커넥트 2개, 고객 DX 라우터 2대, 온프렘 라우터 2대로 구성
  * **서로 다른 DX Location 2곳**에 연결
    → 서로 다른 건물(지리적으로 분리)에서 고객 라우터/온프렘 라우터 2쌍 구성
* 비복원력(취약) 아키텍처:
  ![DX resilience NONE](images/DirectConnectResilience1.png)
* 복원력 OK 아키텍처:
  ![DX resilience OK](images/DirectConnectResilience2.png)
* 더 나은 복원력 아키텍처:
  ![DX resilience BETTER](images/DirectConnectResilience3.png)
* 최고 수준 복원력 아키텍처:
  ![DX resilience GREAT](images/DirectConnectResilience4.png)

---

## Direct Connect Link Aggregation Groups (LAG)

* LAG는 여러 물리 연결을 **하나의 논리 연결처럼** 동작하도록 구성한다.
* 속도 관점에서 LAG 구성:

  * 100Gbps 포트 2개
  * 또는 100Gbps 미만 포트 최대 4개
* 최대 **200Gbps**까지 LAG 생성 가능
* LAG는 일부 복원력을 제공하지만, AWS는 이를 “완전한 HA”로 마케팅하지 않는다.
  특히 **하드웨어 장애**나 **DX Location 전체 장애**에 대한 복원력은 제공하지 않는다.
* LAG는 **Active/Active** 방식이며, 최대 4개의 커넥션이 포함될 수 있다.
* 모든 커넥션은 **동일 속도**여야 하고 **같은 DX Location**에서 종단되어야 한다.
* LAG에는 `minimumLinks` 속성이 있으며, 정상 동작하는 커넥션 수가 이 값 이상이면 LAG가 활성 상태를 유지한다.
  ![DX LAG](images/DXDirectConnectLAG.png)
