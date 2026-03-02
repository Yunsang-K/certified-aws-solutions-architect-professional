# Amazon Cognito

* 모바일/웹 애플리케이션을 위한 **인증(Authentication), 인가(Authorization), 사용자 관리(User management)** 서비스를 제공
* 크게 2가지 핵심 기능으로 구성됨:

  * **User Pools(사용자 풀)**: 로그인(사인인) 용도. 인증 성공 후 **JWT(JSON Web Token)** 발급

    * **User Pools는 AWS 서비스 접근 권한을 직접 부여하지 않음!**
    * 사용자 디렉터리 관리 및 사용자 프로필, 회원가입/로그인(커스터마이징 가능한 웹 UI), MFA 등 보안 기능 제공
    * Google, Apple, Facebook 등 **소셜 로그인** 지원
    * SAML 등 **외부 IdP(Identity Provider)** 를 통한 로그인도 지원
  * **Identity Pools(아이덴티티 풀)**: 외부(연동) 신원을 **임시 AWS 자격 증명(Temporary AWS credentials)** 으로 교환하여 AWS 리소스에 접근하도록 하는 목적

    * **비인증(Unauthenticated) 사용자**: 게스트(미로그인) 사용자에게도 AWS 서비스 접근을 제한적으로 허용 가능
    * **연동(Federated) 사용자**: Google/Facebook/Twitter, SAML 2.0, 그리고 **Cognito User Pools** 등으로 인증한 사용자를 AWS 리소스에 **일시적으로** 접근하도록 인가

## Cognito 개념(Concepts)

![Cognito Concepts](images/CognitoConcepts.png)
