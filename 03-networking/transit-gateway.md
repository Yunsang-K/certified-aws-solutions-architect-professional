# AWS Transit Gateway

* 여러 VPC를 서로 연결하고, Site-to-Site VPN 및 Direct Connect를 통해 온프레미스 네트워크와도 연결하는 **네트워크 트랜짓 허브**입니다.
* AWS 내 네트워크 아키텍처 복잡도를 줄이도록 설계되었습니다.
* **네트워크 게이트웨이 리소스**이며, **고가용성(HA)** 및 **확장성**을 제공합니다.
* **Attachment(연결)**: TGW가 VPC/온프레미스 네트워크에 연결되기 위해 생성하는 연결 객체입니다. 유효한 attachment 유형:

  * VPC attachment
  * Site-to-Site VPN attachment
  * Direct Connect Gateway attachment
* Attachment는 연결 대상 VPC의 **각 서브넷에 대해 구성**합니다.
* 리전 간 / 계정 간으로 **Transit Gateway Peering**(TGW 피어링)을 할 수 있습니다.
* TGW를 **Direct Connect 연결과 연동**할 수 있습니다.
* Transit Gateway 고려사항:

  * **Transitive Routing(전이 라우팅)** 지원: 하나의 TGW에 여러 attachment를 붙이고 **Route Table**로 라우팅 제어
  * 피어링을 통해 **글로벌 네트워크** 구성 가능
  * **AWS RAM**으로 TGW 공유 가능
  * VPC Peering 중심 설계보다 **아키텍처가 단순**해지는 경향

---

## Transit Gateway - Deep Dive

* Transit Gateway는 **허브-스포크(hub-and-spoke)** 아키텍처이며, AWS 내부의 다양한 네트워킹 객체와 연결할 수 있습니다.

### Direct Connect 연동

* **Transit VIF**가 필요하며, 이는 **DX Gateway**를 통해 구성됩니다.
* DX Gateway는 **Transit Gateway Attachment**로 TGW에 연결할 수 있습니다.
* **1개의 DX Gateway는 최대 3개의 Transit Gateway에 연결**할 수 있습니다.

### 라우팅(기본 Route Table)

* TGW에는 기본 Route Table이 있으며, attachment에서 유입되는 경로로 채워질 수 있습니다.

  * VPC: 해당 VPC의 **CIDR 범위**
  * VPN: **BGP로 학습한 경로**
  * DX Gateway + TGW Attachment: **DX Gateway 측 attachment 설정에서 정의한 네트워크**

### 피어링 한도 및 기본 동작

* TGW는 다른 TGW와 리전 간 피어링이 가능하며, **최대 50개 TGW와 피어링**할 수 있습니다(피어된 TGW도 추가 피어링 가능).
* TGW는 기본적으로 **1개의 Route Table**을 가집니다.
* 기본적으로 모든 attachment는 이 Route Table을 기준으로 라우팅 결정을 합니다.
* 기본적으로 모든 attachment는 이 Route Table에 경로를 **propagate(전파)** 합니다. *(예외: peering attachment)*
* 기본 설정에서는 **모든 attachment가 서로 라우팅 가능**한 상태가 됩니다.

---

## Transit Gateway Peering

* Peering attachment의 경우 **경로가 자동 공유되지 않으므로**, VPC Peering처럼 **정적 라우트**를 구성해야 합니다.

  * (AWS는 향후 라우트 광고 관련 확장을 고려해 **고유 ASN 사용**을 권장)
* **Inter-Region Peering**에서는 **Public DNS를 Private 주소로 해석**하는 기능이 지원되지 않습니다.
* 피어링 연결 구간의 데이터 전송은 **암호화**되며, **AWS 네트워크**를 통해 전달됩니다.
* TGW당 **최대 50개의 peering attachment**를 구성할 수 있고, 서로 다른 리전/계정 간도 가능합니다.

---

## Transit Gateway Isolated Routing (격리 라우팅)

### 기본 상태

* 기본적으로:

  * 모든 attachment가 **같은 Route Table에 association(연결)** 됩니다.
  * 모든 attachment가 **같은 Route Table에 propagate(전파)** 하며, 결과적으로 서로의 경로를 인지합니다.

### Route Table/Attachment 관계

* Attachment는 **오직 1개의 Route Table에만 association**될 수 있습니다.
* 하나의 Route Table은 **여러 attachment와 association**될 수 있습니다.
* Attachment는 **여러 Route Table로 propagate**할 수 있으며, 심지어 자신이 association된 Route Table이 아니어도 propagate 가능할 수 있습니다.

### 네트워크를 격리하려면

* 격리 목표에 따라 Route Table을 분리해서 구성합니다.

  * (예시) 서로 통신시키고 싶은 attachment들만 특정 Route Table에 **association**하고,
  * 필요한 attachment들만 해당 Route Table로 **propagate** 하도록 조정합니다.
* 통신을 원치 않는 attachment는 **별도의 Route Table**에 association하고, 다른 attachment들의 propagate를 분리해서 **상호 라우팅이 성립하지 않도록** 구성합니다.
