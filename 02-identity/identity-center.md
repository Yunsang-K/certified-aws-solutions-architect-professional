# IAM Identity Center (AWS Single Sign-On(SSO)의 후속)

* **기존 기업용 ID 스토어(Enterprise Identity Store)** 를 AWS에서 사용할 수 있게 해주는 서비스
* 여러 **AWS 계정**과 **외부 비즈니스 애플리케이션(SaaS 등)** 에 대한 **SSO 접근을 중앙에서 관리**할 수 있음
* 과거에 **SAML 2.0** 로 직접 구성하던 “전통적인 연동 유스케이스”를 **Identity Center가 더 표준적인 방식으로 흡수/대체**하는 흐름
* **유연한 ID 스토어(Identity Store) 선택**: 사용자의 신원(계정/그룹)을 어디에 저장/관리할지 선택할 수 있으며, 외부 신원을 AWS 접근(권한)과 연결해 **AWS 자격 증명(임시 권한)** 으로 전환해서 사용하게 함
* IAM Identity Center가 지원하는 ID 스토어 유형:

  * **내장(Built-in) Identity Store**
  * **AWS Managed Microsoft AD**
  * **온프레미스 Microsoft AD**(양방향 트러스트 또는 **AD Connector**)
  * **외부 IdP**(SAML 2.0)
* AWS는 “**워크포스(사내 사용자)**”용 연합 인증/SSO는 전통적인 SAML 2.0 직접 연동보다 **IAM Identity Center 사용을 권장**하는 포지션

## IAM Identity Center 아키텍처

* (그림) IAM Identity Center 구성도: `images/AWSSSO.png`

* IAM Identity Center의 전제 조건 중 하나:

  * **유효한 AWS Organizations가 생성되어 있어야 함** (멀티 계정 SSO 중앙 관리를 위한 기반)
