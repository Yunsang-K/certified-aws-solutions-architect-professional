# Gateway Load Balancers (GWLB)

* **서드파티 보안 어플라이언스**(Third-party security appliances)를 **실행하고 확장(Scale)** 하기 위한 서비스
* 보안 어플라이언스 예:

  * 방화벽(Firewall)
  * 침입 탐지/방지(IDS/IPS)
  * 트래픽/데이터 분석 도구 등
* **인바운드/아웃바운드 트래픽을 투명(Transparent)하게 검사/보호**하는 용도로 사용
  (애플리케이션/클라이언트 입장에서는 중간에 보안 장비가 있어도 동작이 크게 바뀌지 않게 구성)

---

## 구성 요소(High-level)

GWLB는 크게 2가지로 이해하면 됩니다.

### 1) **GWLB Endpoint (GWLBe)**

* 트래픽이 **VPC로 들어오거나(VPC Ingress), 나가거나(Egress)** 할 때 경유하는 **진입/이탈 지점**
* **Interface Endpoint**와 유사한 형태로 보이지만, GWLB 트래픽 처리를 위한 특성이 추가된 엔드포인트

### 2) **GWLB (Load Balancer) + 보안 어플라이언스**

* GWLB 뒤에는 보안 소프트웨어가 설치된 **EC2 기반의 어플라이언스들**이 동작
* GWLB가 이 어플라이언스들로 트래픽을 분산하여 **수평 확장(Scale-out)** 가능

---

## 트래픽 처리 핵심

* GWLB는 트래픽을 **변형 없이 전달**해야 함
  → 보안 어플라이언스가 **원본 패킷(원본 IP 포함)** 을 그대로 보고 판단해야 하기 때문
* GWLB는 **GENEVE**(Generic Network Virtualization Encapsulation)라는 **터널링 프로토콜**을 사용

  * 트래픽과 메타데이터를 **캡슐화(Encapsulation)** 해서 전달
* GWLB는 **L3/L4** 계층에서 동작하며, 개념적으로 **NLB와 유사**하지만:

  * **GWLB Endpoint와 결합**되어 동작하고
  * Endpoint ↔ GWLB ↔ 어플라이언스 사이 트래픽을 **GENEVE로 캡슐화/복호화**해서 왕복시킴
* **Flow stickiness(흐름 고정)** 를 관리

  * 하나의 플로우(5-tuple 기반 흐름)는 **항상 동일한 보안 어플라이언스**를 타도록 보장 (상태 기반 장비에 중요)

---

## Ingress Route Table

* **Internet Gateway(IGW)에 연결되는 Ingress Route Table**을 구성할 수 있음
* 이 라우팅 테이블은 “**VPC로 들어오는 트래픽이 처음에 어디로 갈지**”를 결정
* 예: 들어오는 트래픽을 **GWLB Endpoint로 리다이렉트**해서 “먼저 검사 → 이후 목적지” 흐름으로 만들 수 있음

---

## 아키텍처 흐름(예시 설명)

아래 예시는:

* 애플리케이션 VPC에서 **프라이빗 서브넷의 EC2** + **퍼블릭 서브넷의 ALB**
* 별도 Security VPC에서 **보안 어플라이언스가 ASG로 확장/축소**
  하는 형태입니다.

1. 외부 트래픽이 **IGW**에 도착
2. IGW에 연결된 **Ingress Route Table**에 의해 트래픽이 **GWLB Endpoint**로 라우팅
3. Endpoint가 트래픽을 **Security VPC의 GWLB**로 전달

   * 이때도 트래픽은 **원본 IP를 유지**
4. GWLB가 트래픽을 **GENEVE로 캡슐화**하여 보안 어플라이언스로 전달
5. 보안 어플라이언스가 패킷을 분석/검사 후 GWLB로 반환
6. GWLB가 **GENEVE 캡슐화를 제거(Decapsulation)** 하고 다시 **GWLB Endpoint**로 반환
7. 원본 IP가 유지되므로, 애플리케이션 VPC의 **로컬 라우팅**에 따라 **ALB**로 전달
8. ALB가 트래픽을 **선택된 애플리케이션 EC2 인스턴스**로 포워딩
9. 이후 응답 트래픽도 동일한 검사 경로를 따라(플로우 고정 포함) 되돌아감
10. 최종적으로 응답이 **IGW를 통해 원래 클라이언트**로 반환

---

![GWLB Architecture](images/GWLB3.png)
