# 애플리케이션(Layer 7) 방화벽

* **Layer 3/4 방화벽**

  * 패킷/세그먼트, IP 주소, 포트 수준을 본다.
  * 요청 스트림과 응답 스트림을 **서로 다른(분리된) 흐름**으로 본다.

* **Layer 5 방화벽**

  * 요청/응답 스트림을 **하나의 세션(Session)** 으로 인식하는 기능을 제공한다.
  * 이를 통해 관리 오버헤드를 줄이고, 더 **문맥 기반(Contextual)** 보안 정책을 적용할 수 있다.

* **공통점**

  * 두 경우 모두 자신이 동작하는 레이어보다 **상위 레이어의 의미(프로토콜 내용)** 는 이해하지 못한다.

* **Layer 7 방화벽**

  * HTTP 같은 **L7 프로토콜을 이해**한다.
  * L7 관점에서 정상/비정상 요소를 식별할 수 있다.
  * 프로토콜 수준의 공격/취약점을 방어할 수 있다.
  * **HTTPS의 경우** 내용을 분석하려면 방화벽에서 **TLS 종료(Termination)** 가 필요하다.
    즉, 클라이언트–방화벽 구간에서 복호화 후 검사하고, 방화벽–서버 구간에 대해 **새 HTTPS 연결을 재수립**한다.
  * 데이터를 **검사(Inspect)** 하고, **차단(Block)** / **치환(Replace)** / **태깅(Tag)** 할 수 있다.
  * 성인 콘텐츠, 스팸, 주제 이탈 콘텐츠, 악성코드 등 **콘텐츠 기반 정책**도 적용 가능하다.

---

## WAF (Web Application Firewall)

* WAF는 **Layer 7 방화벽**이다(HTTP/S 이해).
* 일반적인 “방화벽”은 주로 **Layer 3/4/5**에서 동작한다.
* WAF는 SQL Injection, XSS 같은 **복잡한 L7 공격/익스플로잇**을 방어한다.
* **Geo 차단**(국가/지역 기반)과 **요청 속도(Rate) 인지** 기능을 제공한다.

---

## WEBACL (Web Access Control List)

* WAF의 주요 설정 단위는 **WEBACL**이며, 이를 다음 리소스에 연결(Associate)할 수 있다.

  * **ALB, API Gateway, CloudFront, AppSync**
* WEBACL은 **규칙(Rule)/규칙 그룹(Rule Group)** 으로 구성되며, 트래픽이 도착할 때 평가된다.

### (WAF) 규칙 예시 (AWS Managed Rule Group)

* Allow List / Deny List
* SQL Injection
* XSS
* HTTP Flood
* IP Reputation
* Bots(봇넷 등 자동화 트래픽 방어)

### WEBACL 특징

* WEBACL의 시작점은 **기본 동작(Default Action)** 이다.

  * 어떤 규칙에도 매치되지 않은 트래픽에 대해 **ALLOW 또는 BLOCK** 적용
* WEBACL은 **CloudFront용** 또는 **리전(Regional) 서비스용(ALB, API GW, AppSync)** 으로 생성한다.
* 필터링을 하려면 WEBACL에 **Rule Group/Rule** 을 추가해야 하며, **위에서 아래 순서대로** 처리된다.
* WEBACL에는 규칙들이 사용할 수 있는 **연산 한도**가 있으며, 이를 **WCU(WEBACL Capacity Units)** 로 관리한다.

  * WCU는 규칙 복잡도의 지표
  * 하나의 ACL에 포함 가능한 WCU 총량에 제한이 있다.
  * 기본 최대는 **1500 WCU**이며, **서포트 티켓으로 증설 가능**
* 리소스에 WEBACL을 연결하는 데는(서비스에 따라) 시간이 걸릴 수 있다.
  연결된 WEBACL의 규칙을 수정하는 것은 상대적으로 더 빠르다.
* 하나의 AWS 리소스는 **1개 ACL만** 연결 가능하지만, 하나의 WEBACL은 **여러 리소스에 재사용** 가능하다.
* **CloudFront용 ACL**은 다른 리전 서비스와 **혼용 연결할 수 없다**.
* **AWS Outposts는 WEBACL을 지원하지 않는다.**

---

## Rule Group (규칙 그룹)

* 여러 규칙을 묶은 단위
* Rule Group 자체에는 **기본 동작(Default Action)이 없다.**
  (WEBACL에 추가할 때 그룹에 대한 동작을 지정)
* 유형

  * **Managed**(AWS 또는 Marketplace)
  * **Customer Managed**(직접 생성)
  * **Service Owned**(Shield, Firewall Manager 등)
* AWS Managed Rule Group은 대체로 **추가 비용 없이 제공**되는 편이지만,

  * **WAF Bot Control / Fraud Control** 등은 별도 비용이 있다.
* Marketplace Rule Group은 **구독(Subscription) 비용**이 붙는다.
* Rule Group 생성 시 **WCU 용량(최대 1500)** 을 미리 정의한다.

---

## Rule (규칙)

### 규칙의 구조: Type / Statement / Action

* **Type**: 규칙이 동작하는 큰 방식
* **Statement**: 트래픽 매치 조건(하나 이상 가능)
* **Action**: 매치 시 WAF가 수행할 동작

### Type

* **Regular**: 특정 조건이 발생하면 매치
* **Rate-based**: 특정 조건이 **지정된 속도/빈도**를 초과할 때 매치

### Statement (조건 정의)

* Regular 규칙: “무엇을 매치할지(WHAT)” 정의
* Rate-based 규칙:

  * 소스 IP별 요청 수에 레이트 리밋 적용, 또는
  * 특정 조건을 만족하는 요청들에 대해 IP 기준 레이트 리밋 적용

### 매치 기준(예)

* Origin Country
* IP
* Label
* Header
* Cookies
* Query Parameters
* URI Path
* Query String / Body (**처음 8192 bytes만**)
* HTTP Method

### 매치 방식(예)

* Exact match, Starts with, Contains, Regular expression 등
* AND / OR / NOT 조합으로 다중 조건 구성 가능

### Action

* Regular 규칙 액션

  * **Allow, Block, Count, CAPTCHA, Custom response / Custom header(`x-amzn-waf-`), Label**
* Rate-based 규칙 액션

  * **Allow는 불가**, **Block / Count / CAPTCHA** 만 가능
* Custom response/custom header

  * **Block** 시: Custom response + Custom header 가능
  * **Allow** 시: Custom header만 가능
* **Label**

  * WAF 내부 개념으로, 다단계 처리 흐름 구성에 사용
  * 예: 첫 규칙이 라벨을 추가 → 다음 규칙이 라벨 존재 여부로 추가 제어

---

## 과금(Pricing)

* WEBACL: **ACL 1개당 월 과금**(현재 **$5/월**)

  * WEBACL은 **재사용 가능**
* WEBACL 내 Rules: **규칙 1개당 월 과금**(현재 **$1/월**)
* ACL에 추가하는 **Rule Group / Managed Rule Group** 도 과금 대상
* 요청 처리: ACL 기준으로 **요청량 과금**(현재 **$0.60 / 100만 요청**)
* 옵션 보안 기능(추가 비용)

  * Intelligent Threat Mitigation
  * Bot Control (**$10/월 + $1/100만 요청**)
  * CAPTCHA
  * Fraud Control / Account Takeover
* Marketplace Rule Group은 별도 비용이 추가된다.
