# Amazon WorkSpaces (SAP-C02 관점 한국어 정리)

* **DaaS(Desktop as a Service)**: AWS가 관리하는 **가상 데스크톱(Windows/Linux)** 을 사용자에게 제공하는 서비스입니다.
* **재택/원격 근무**에 적합하며, 개념적으로 Citrix/Remote Desktop과 유사하지만 **AWS 관리형**이라는 점이 핵심입니다.
* 사용자는 어디서든 클라이언트로 접속해 **동일한 데스크톱 환경**을 사용합니다(로그아웃/재접속 시에도 환경 유지).

## 과금

* **월 단위(Always-on 성격)** 또는 **시간 단위(Usage 기반)** 과금 모델을 선택할 수 있습니다.
* 시간 과금이라도 **기본 인프라 월 비용(고정비)** 이 추가로 붙는 형태가 일반적이라, “순수 종량제”로만 보면 비용 계산이 틀리기 쉽습니다(시험 포인트).

## 인증/디렉터리 연동

* WorkSpaces는 사용자 인증 및 계정 관리를 위해 **AWS Directory Service** 계열을 사용합니다.

  * **Simple AD / AWS Managed Microsoft AD / AD Connector**(온프렘 AD 연동)
* 디렉터리 서비스는 VPC 안에 **ENI를 생성**하여(= 네트워크 엔드포인트) 인증/도메인 기능을 제공합니다.

## 네트워크 구조 (아키텍처 핵심)

* 각 WorkSpace는 고객 VPC의 서브넷에 **ENI(Elastic Network Interface)** 를 “주입”해 붙습니다.
* 실제 WorkSpaces 실행/관리 영역은 **AWS 관리 VPC** 쪽에 있고, 고객 VPC와는 ENI로 연결되는 형태로 이해하면 안전합니다.
* WorkSpaces 접속은 전용 클라이언트를 사용하며, **클라이언트 ↔ WorkSpaces 구간의 대역폭 자체는 별도 과금 없이 포함**되는 케이스가 많습니다.
  다만 WorkSpace에서 인터넷/외부로 나가는 트래픽은 **일반 VPC egress 비용(예: NAT GW, IGW, 데이터 전송 등)** 으로 과금될 수 있습니다.

## 스토리지/암호화

* 기본적으로 **System volume(시스템)** 과 **User volume(사용자)** 가 분리되어 제공됩니다.
* 두 볼륨 모두 **암호화(Encryption)** 를 적용할 수 있습니다(KMS 기반으로 설명되는 문제가 자주 나옵니다).

## 연동 시나리오 (자주 나오는 포인트)

* Windows WorkSpaces는 같은 AD 기반 환경에서 **EC2 Windows**나 **Amazon FSx(Windows File Server 등)** 와 연동이 자연스럽습니다.
* **하이브리드 네트워크(VPN/Direct Connect)** 가 있으면 WorkSpaces에서 **온프레미스 리소스 접근**도 가능(보안/라우팅 설계가 전제).

## 가용성/장애 관점

* WorkSpaces는 기본적으로 **“단일 데스크톱이 자동으로 멀티 AZ HA”** 인 서비스가 아닙니다.
* AZ 장애에 대비하려면 **사용자를 다른 AZ의 WorkSpace로 분산**하는 설계는 가능하지만, **특정 WorkSpace 1대가 AZ 장애 영향을 받을 수 있음**을 전제로 답해야 합니다.
