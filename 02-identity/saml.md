## SAML 2.0 Identity Federation (AWS SAP-C02 관점) — 한국어 번역/정리

* **페더레이션(Federation)**은 **AWS 외부 사용자(기업 사용자)**가 **일시적(Temporary)** 으로 **IAM 역할(Role)** 을 Assume 해서 AWS 리소스에 접근하도록 하는 방식이다.
* 사용자는 사내 **IdP(Identity Provider)** 에서 인증을 받고, 그 결과로 발급된 **SAML Assertion** 을 이용해 AWS에서 역할을 Assume 한다.
* **SAML 2.0(Security Assertion Markup Language 2.0)** 은 표준 기반의 인증/SSO 프로토콜이며, AWS에서는 **온프레미스/기업 디렉터리 계정**을 **간접적으로** AWS 콘솔 및 CLI/API 접근에 활용할 수 있게 한다.
* SAML 2.0 페더레이션은 보통 **SAML 2.0 호환 기업 IdP**(예: ADFS, Okta, Ping, Azure AD 등)를 이미 운영 중일 때 사용한다.
* 이미 **전사 계정/권한 관리 조직(Identity 관리팀)** 이 있고, AWS도 그 체계에 편입해 **단일 소스 오브 트루스(SSOT)** 로 운영하려는 경우에 적합하다.
* (요약 문장 그대로 유지) **단일 소스 오브 트루스 유지가 중요하거나 사용자 수가 5,000명 이상이면 SAML 기반 페더레이션을 권장**한다는 관점으로 설명되곤 한다.
* AWS 접근은 **IAM Role + STS 임시 자격 증명(Temporary Credentials)** 을 사용하며, 문서 기준으로 **최대 12시간 유효**로 설정되는 케이스가 많다(구성에 따라 달라질 수 있음).

---

## 인증 흐름 (API/CLI 접근)

1. 사용자가 사내 IdP에 로그인(인증)
2. IdP가 사용자/그룹 정보를 포함한 **SAML Assertion** 발급
3. 클라이언트가 AWS STS의 **AssumeRoleWithSAML** 호출
4. STS가 **임시 보안 자격 증명**(Access Key/Secret/Session Token) 발급
5. 사용자는 해당 임시 자격 증명으로 AWS API 호출

---

## 인증 흐름 (AWS 콘솔 접근)

1. 사용자가 IdP 포털에서 “AWS 콘솔” 앱 클릭(SSO)
2. IdP가 SAML Assertion을 브라우저로 전달(POST)
3. AWS가 Assertion을 검증하고 역할을 매핑
4. 콘솔 세션이 생성되고, 사용자는 해당 역할 권한으로 콘솔 사용

---

## 구성/키 포인트 (시험에서 자주 묻는 부분)

* **AWS IAM과 SAML IdP 간 신뢰(Trust) 설정**이 필요

  * AWS 측: **SAML Provider(메타데이터 등록)** + **Role의 Trust Policy** 에 SAML Provider를 Principal로 허용
  * IdP 측: AWS를 Service Provider로 등록하고 Role/Attribute 매핑
* 핵심 STS API: **`AssumeRoleWithSAML`**
* “예전 방식”으로 표현되는 이유: 요즘 AWS 권장 흐름은 **IAM Identity Center(구 AWS SSO)** 를 통한 중앙 SSO/계정 접근 관리가 더 일반적이기 때문(특히 AWS Organizations 환경).

---

## 용어 한 줄

* **SAML Assertion**: IdP가 “이 사용자는 누구이고 어떤 속성/그룹을 가졌다”를 서명해서 전달하는 인증 토큰.
