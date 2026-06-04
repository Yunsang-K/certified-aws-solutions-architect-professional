# AWS SAP Session 1

Date: 2026-06-04
Status: Completed
Cycle: 1

## Source Files

- `01-accounts/organizations.md`
- `01-accounts/policies.md`

## Result

- Question Count: 10
- Correct: 10
- Wrong: 0
- Accuracy: 100%
- Main Weakness: None

## Covered Topics

### AWS Organizations

- Standard AWS account는 AWS Organizations에 속하지 않은 단독 계정이다.
- Organization을 생성한 계정은 Management Account가 된다.
- Management Account는 다른 계정을 초대하거나 새로 생성하여 조직에 편입할 수 있다.
- 조직에 들어온 계정은 Member Account가 된다.
- 조직은 Root, OU, Account로 구성되는 계층형 구조다.
- Root는 IAM root user가 아니라 Organizations 내부의 최상위 컨테이너다.

### Consolidated Billing

- Member Account의 비용은 Management Account를 통해 통합 결제된다.
- Management Account는 Payer Account 역할을 한다.
- RI, Savings Plans 등의 할인 혜택은 조직 단위로 풀링될 수 있다.

### OrganizationAccountAccessRole

- AWS Organizations에서 새 계정을 Create 방식으로 만들면 일반적으로 `OrganizationAccountAccessRole`이 자동 생성된다.
- 기존 계정을 Invite 방식으로 편입한 경우 Member Account 쪽에 이 Role을 수동으로 준비해야 동일한 접근 경로를 만들 수 있다.

### Service Control Policies

- SCP는 계정/OU/Root에 적용하는 최대 권한 경계다.
- SCP는 권한을 부여하지 않는다.
- 계정 내 IAM 정책이 Allow하더라도 SCP가 Deny하면 최종적으로 거부된다.
- Management Account는 SCP의 영향을 받지 않는다.
- 상위 Root/OU에 연결한 SCP는 하위 OU/계정으로 상속된다.
- Deny list 방식은 대부분 허용하고 특정 행위만 차단한다.
- Allow list 방식은 기본 차단 후 필요한 서비스/액션만 허용한다.

### IAM Policy Types

- Identity-based Policy: IAM User, Group, Role에 연결하며 ID에게 권한을 부여한다.
- Resource-based Policy: S3 Bucket Policy, IAM Role Trust Policy처럼 리소스에 연결하며 Principal에게 권한을 부여한다.
- Permission Boundary: IAM User/Role의 ID 기반 권한 최대치를 제한하지만 권한을 직접 부여하지 않는다.
- SCP: 조직/OU/계정의 최대 권한을 제한하지만 권한을 직접 부여하지 않는다.
- ACL: JSON 정책 문서 구조가 아닌 정책 유형이며, 다른 계정 Principal의 접근을 제어한다.
- Session Policy: AssumeRole 또는 Federation 세션에 전달해 세션 권한을 제한한다.

### Policy Evaluation

- 평가 우선순위는 Explicit Deny > Allow > Implicit Deny다.
- 정책은 기본적으로 Implicit Deny를 전제로 한다.
- Explicit Deny는 항상 Allow보다 우선한다.

## Quiz Review

All 10 questions were answered correctly.

No weak tag was added.

## User Question Logged

### Question

관리형 정책과 인라인정책의 차이

### Answer Summary

- 관리형 정책은 독립된 정책 객체이며 여러 IAM User, Group, Role에 재사용 가능하다.
- 인라인 정책은 특정 IAM User, Group, Role 내부에 직접 포함되는 1:1 종속 정책이다.
- 관리형 정책은 재사용성과 운영 편의성이 좋다.
- 인라인 정책은 특정 엔터티 하나에만 강하게 묶인 예외 권한에 적합하다.
- 엔터티 삭제 시 인라인 정책은 함께 사라진다.

### Exam Point

- 여러 IAM 엔터티에 같은 권한을 적용해야 하면 관리형 정책을 우선 고려한다.
- 특정 엔터티와 강하게 묶인 예외 권한이면 인라인 정책을 사용할 수 있다.
- 실무 운영 관점에서는 고객 관리형 정책이 일반적으로 관리하기 쉽다.

## Next Session

- `02-identity/identity-center.md`
- `03-networking/vpc.md`
