# BGP - Border Gateway Protocol

* BGP는 **라우팅 프로토콜**이다.
* **데이터가 A 지점에서 B 지점으로 어떻게 흐를지**(경로 선택)를 제어하는 데 사용된다.
* BGP는 **Autonomous System(AS)** 이라고 불리는, 스스로 관리되는 다수의 네트워크들로 구성된다.
* AS는 대규모 네트워크, 라우트들의 집합 등일 수 있으며, BGP 관점에서는 **블랙박스(내부는 상세히 보지 않음)** 처럼 취급된다.
* 각 AS는 IANA로부터 **번호(ASN, Autonomous System Number)** 를 할당받는다.
* ASN은 **16비트** 길이이며 범위는 **0~65535** 이고, **64512~65534** 범위는 **프라이빗(사설) ASN** 이다.
* **32비트 ASN** 도 사용할 수 있다(이 시험 범위에서는 제외).
* ASN은 BGP에서 네트워크 상의 **서로 다른 엔터티(AS)를 식별**하는 데 사용된다.
* BGP는 **신뢰성(reliable)** 과 **분산(distributed)** 을 목표로 설계되었고, **TCP/179** 에서 동작한다.
* BGP는 **자동(autonomous)으로 설정되지 않으며**, 두 AS 간 통신(피어링)은 **수동으로 구성**해야 한다.
* AS 간에는 **네트워크 토폴로지(Topology) 정보**를 교환한다.
* BGP는 **Path-Vector 프로토콜**이다: 피어 간에 목적지까지의 **최적 경로(best path)** 를 교환하며, 이 경로는 **ASPATH(Autonomous System Path)** 라고 부른다.
* BGP는 **링크 속도나 상태**(대역폭, 지연, 혼잡 등)를 고려하지 않고, **경로(path)** 에 집중한다.
* **iBGP(Internal BGP)**: 하나의 AS **내부**에서 라우팅(경로 정보 교환)을 수행한다.
* **eBGP(External BGP)**: 서로 다른 AS **사이**에서 라우팅(경로 정보 교환)을 수행한다.
* BGP 예시:

  * (첨부 이미지: `BorderGatewayProtocol101.png`)
* BGP는 기본적으로 **가장 짧은 경로(ASPATH 길이 기준)** 를 선택한다. 또한 **AS Path Prepending**(자기 ASN을 경로에 여러 번 추가해 경로를 “인위적으로 길게” 만들어) 트래픽 경로를 **유도(influence)** 할 수 있다.
