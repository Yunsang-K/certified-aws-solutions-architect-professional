# AWS Organizations

## 기본 개념

* **Standard AWS account**: AWS Organizations에 **속하지 않은** 단독 계정
* 단독 계정에서 **AWS Organization을 생성**할 수 있다.
* Organization은 “계정 안에 생기는 것”이라기보다, 그 계정을 **관리 주체로 삼아** 조직(Organization)을 만든다고 이해하면 된다.
* Organization을 만든 계정은 **Management Account**(구 Master Account)가 된다.
* Management Account를 통해 다른 계정을 조직으로 **초대(Invite)** 하거나 **새로 생성(Create)** 하여 편입시킬 수 있다.
* 조직에 들어온 계정은 **Member Account**가 된다.
* Organizations는 **1개의 Management Account**와 **0개 이상의 Member Account**로 구성된다.

## 조직 구조(계층)

* 조직 내 계정들을 비즈니스 유닛, 기능, 환경(Dev/Prod) 등 기준으로 **그룹화**할 수 있다.
* 구조는 **계층형(트리)** 이며, 루트가 최상단에 있는 **역트리(inverted tree)** 형태로 설명된다.
* 최상단에는 조직의 **Root 컨테이너**가 있다.

  * 이건 “root user”와 무관한 **조직 내부의 컨테이너**다.
* Root 아래에 다른 컨테이너를 만들 수 있는데, 이를 **OU(Organizational Unit)** 라고 한다.
* OU는 **계정(Management/Member)** 또는 **하위 OU**를 포함할 수 있다.

---

# Consolidated Billing(통합 과금)

* AWS Organizations의 중요한 기능 중 하나.
* Member Account의 개별 청구는 실무적으로 **통합 청구 모델로 전환**되며, 비용은 Management Account를 통해 결제된다.
* 이때 Management Account는 **Payer Account** 역할을 한다.
* 결과적으로 조직 전체에 대해 **단일 월별 청구서**를 받는다.
* 예약 혜택/할인(예: RI/Savings Plans 등)은 조직 단위로 **풀링**되어, 한 계정의 사용량/지출이 조직 전체 최적화에 기여할 수 있다.

---

# Best Practices(모범 사례)

* 사용자가 직접 여러 계정에 로그인하기보다,
  **단일 진입 계정(Identity / Login Account)** 에서 로그인한 뒤 **IAM Role Assume**으로 각 계정에 접근하는 구조를 권장.
* 이 “Login Account”는 Management Account일 수도 있고, 별도의 Member Account로 분리할 수도 있다.

  * 실무에서는 보통 **관리/결제(Management)** 와 **사용자 아이덴티티(SSO/IAM Identity Center)** 를 분리하는 패턴이 자주 나온다.

---

# `OrganizationAccountAccessRole`

* AWS Organizations에 새로 **생성(Create)** 한 계정에 접근하기 위한 기본 IAM Role.
* Organization에서 계정을 “생성”하면 일반적으로 이 Role이 **자동 생성**된다.
* 반면, 기존 계정을 **초대(Invite)** 해서 조직에 편입시키는 경우엔, Member Account 쪽에 이 Role을 **수동으로 준비/생성**해야 접근 경로가 동일해진다(실무 관점).

---

# Service Control Policies (SCP)

## SCP의 성격

* AWS Organizations 기능으로, **계정 단위로 허용/차단 가능한 최대 권한 범위(Guardrail)** 를 정의한다.
* JSON 정책 문서 형태.
* 적용 위치:

  * 조직 **Root**
  * **OU**
  * **개별 Account**
* **상속(inherit)**: 상위(루트/OU)에 붙인 SCP는 **하위 OU/계정으로 내려간다**.

## Management Account 예외(중요)

* **Management Account는 SCP의 영향을 받지 않는다.**

  * 즉, 루트/OU에 SCP를 붙여도 Management Account 자체는 그 제한을 “강제”당하지 않는다.
  * (시험에서도 자주 나오는 포인트)

## “권한 경계”로서의 SCP

* SCP는 **권한을 부여하지 않는다(Grant 없음)**.
* SCP는 “이 계정에서 **최대로 가능한 범위가 어디까지인지**”를 제한한다.
* 계정 내 IAM 정책(사용자/역할 정책)이 Allow라도, SCP가 막으면 **최종적으로 불가**.
* root user는 “사용자”로서 막는 게 아니라, **계정이 할 수 있는 행위 자체를 제한**하므로 결과적으로 root user도 영향을 받는다.

---

# SCP 운영 방식: Deny list vs Allow list

## 1) Deny list(기본 운영 방식)

* 기본은 “대부분 허용” + **특정 서비스/행위만 명시적으로 Deny**.
* `FullAWSAccess`:

  * SCP 활성화 시 기본으로 조직/OU에 붙는 정책(“기본 제한 없음” 의미).
* 평가 우선순위(중요):

  1. **Explicit Deny**
  2. Allow
  3. Default(implicit) deny
* 장점: AWS가 새 서비스를 추가해도, 별도 조치 없이 기본적으로 사용 가능해 **운영 부담이 낮다**.

## 2) Allow list(화이트리스트 방식)

* 기본은 “전부 차단” + **필요한 것만 Allow로 열기**.
* 구현 흐름(개념):

  1. `FullAWSAccess`를 제거하거나 상속 경로에서 배제
  2. 허용할 서비스/액션만 Allow로 나열한 SCP를 적용
* 장점: 보안상 더 엄격한 통제 가능
* 단점: 신규 서비스/기능 도입 때마다 정책 업데이트가 필요해 **운영 부담이 높다**.
