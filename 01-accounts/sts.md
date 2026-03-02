## STS (AWS Security Token Service) 요약 (SAP-Professional 관점)

* **역할(Role) Assume를 위한 서비스**: 같은 계정/다른 계정(Cross-account)에서 **Role을 Assume**할 수 있게 해줌.
* **임시 자격 증명(Temporary credentials) 발급**: `sts:AssumeRole*` 계열 API로 **만료되는 임시 키**를 생성.
* **임시 자격 증명 특징**

  * Access Key와 형태는 유사하지만 **영구 IAM 사용자 키가 아님**.
  * **만료(expire)** 되며, **Assume한 주체(사용자/서비스)의 “소유 키”가 아니라 세션 기반**.
  * 보통 **제한된 권한(least privilege)** 로 구성하는 게 일반적.
  * 발급 요청 주체는 AWS 내부 주체(서비스/역할)일 수도, 외부 IdP 연동(페더레이션)일 수도 있음.
* **임시 자격 증명 구성 요소**

  * `AccessKeyId`
  * `SecretAccessKey`
  * `SessionToken` (**STS 임시 자격 증명은 이게 필수**)
  * `Expiration`
* **Identity Federation(연합 인증) 핵심 구성요소**: SAML/OIDC 등 외부 IdP 사용자에게 STS로 임시 자격 증명을 발급해 AWS 접근을 성립시킴.

---

## STS로 Role Assume 흐름

1. 계정 내/크로스 계정 **IAM Role 정의**
2. 그 Role의 **Trust policy(신뢰 정책)** 에서 “누가 Assume 가능한지(Principal)” 정의
3. 허용된 주체가 **STS `AssumeRole`** 호출해 Role 세션 발급
4. 임시 자격 증명 유효기간: **최소 15분 ~ (설정된 최대치까지)**

> 시험 포인트: “권한은 Role의 Permission policy가 결정”, “Assume 가능 여부는 Trust policy가 결정”.

---

## 임시 자격 증명 회수(Revocation) 관련 핵심

* **STS 임시 자격 증명은 ‘발급 후 개별 세션을 수동으로 취소’할 수 없음.**
* 즉, **만료 시점까지 유효**.
* 단순히 Role의 Permission policy를 바꾸면:

  * 유출 세션도 막을 수 있지만
  * **정상 사용자 세션도 같이 영향** → 운영 리스크 큼

### 현실적인 대응(세션 강제 무효화에 가까운 방법)

* Role에 **`AWSRevokeOlderSessions` 인라인 정책을 일시 적용**해서

  * “특정 시점 이전에 발급된 세션”을 **거부**하도록 만들 수 있음
  * 이후 새로 발급되는 세션은 영향을 받지 않게 운용 가능

> 시험에서 자주 나오는 결론: **“STS 세션은 개별 revoke 불가 → 만료/세션 차단 정책/신뢰정책 조정으로 대응”**.
