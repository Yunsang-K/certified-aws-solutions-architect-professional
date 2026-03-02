# 정책(Policies)

* IAM 정책은 **작업(Action)에 대한 권한**을 정의하며, 그 작업을 **어떤 방식(콘솔/CLI/API 등)으로 수행하든** 동일하게 적용된다.

## 정책 유형(Policy types)

* **ID 기반 정책(Identity-based policies)**: IAM ID(사용자, 사용자가 속한 그룹, 역할)에 관리형 정책(Managed) 또는 인라인 정책(Inline)을 연결한다. **ID에게 권한을 부여**한다.
* **리소스 기반 정책(Resource-based policies)**: 리소스에 인라인 정책을 연결한다. 대표 예시는 **S3 버킷 정책**과 **IAM 역할 신뢰 정책(Trust policy)** 이다. 정책에 명시된 **Principal(주체)** 에게 권한을 부여한다. Principal은 리소스와 **같은 계정**일 수도, **다른 계정**일 수도 있다.
* **권한 경계(Permissions boundaries)**: 관리형 정책을 IAM 엔터티(사용자/역할)의 권한 경계로 사용한다. 이 정책은 ID 기반 정책이 해당 엔터티에 **부여할 수 있는 최대 권한 상한**을 정의하지만, **그 자체로 권한을 부여하지는 않는다**. 또한 권한 경계는 **리소스 기반 정책이 부여할 수 있는 최대 권한**은 정의하지 못한다.
* **Organizations SCPs(서비스 제어 정책)**: AWS Organizations의 SCP로 조직/OU 내 계정 구성원에 대한 **최대 권한 상한**을 정의한다. SCP는 계정 내 엔터티(사용자/역할)에 대해 **ID 기반 정책 또는 리소스 기반 정책이 부여하는 권한을 제한**하지만, **권한을 부여하지는 않는다**.
* **ACL(Access control lists)**: ACL이 연결된 리소스에 대해 **다른 계정의 Principal**이 접근할 수 있도록 제어한다. ACL은 리소스 기반 정책과 유사하지만, JSON 정책 문서 구조를 사용하지 않는 **유일한 정책 유형**이다. ACL은 지정된 Principal에게 **교차 계정 권한을 부여**하며, **같은 계정 내 엔터티에는 권한을 부여할 수 없다**.
* **세션 정책(Session policies)**: AWS CLI/API로 역할(AssumeRole) 또는 페더레이션 사용자로 세션을 만들 때 **고급 세션 정책**을 전달할 수 있다. 세션 정책은 역할/사용자의 ID 기반 정책이 세션에 부여하는 권한을 **제한**한다. 즉, 생성된 세션의 권한을 제한하지만 **권한을 부여하지는 않는다**. 자세한 내용은 Session Policies 참고.

## 정책 심화(Policies Deep Dive)

* 정책의 구성(Anatomy of a policy): JSON 문서로 `Effect`, `Action`, `NotAction`( `Action`의 역조건), `Resource`, `Condition`, `Policy Variables` 로 구성된다.
* AWS 권한 평가 우선순위(Priority order)는 다음과 같다: **명시적 Deny > Allow > 암시적 Deny**
  정책은 기본적으로 **암시적 Deny(Implicit deny)** 를 전제로 한다. 즉, 명시적으로 허용하지 않으면 수행할 수 없다.
* **명시적 `DENY`는 항상 `ALLOW`보다 우선**한다.
* 베스트 프랙티스: **최소 권한(Least privilege)** 으로 최대 보안 확보

  * **Access Advisor**: 부여된 권한과 마지막 사용 시점을 확인하는 도구
  * **Access Analyzer**: 외부 엔터티와 공유된 리소스를 분석하는 데 사용
* 자주 쓰는 관리형 정책(Common Managed Policies)

  * `AdministratorAccess`
  * `PowerUserAccess`: IAM/Organizations/Account 관련 권한(일부 예외 제외)은 허용하지 않으며, 그 외에는 관리자 권한과 유사
* IAM 정책 조건(Condition) 구조

```json
"Condition": {
    "{condition-operator}": {
        "{condition-key}": "{condition-value}"
    }
}
```

* 연산자(Operators)

  * String: `StringEquals`, `StringNotEquals`, `StringLike` 등
  * Numeric: `NumericEquals`, `NumericNotEquals`, `NumericLessThan` 등
  * Date: `DateEquals`, `DateNotEquals`, `DateLessThan` 등
  * Boolean
  * IpAddress/NotIpAddress:

    * `"Condition": {"IpAddress": {"aws:SourceIp": "192.168.0.1/16"}}`
  * ArnEquals/ArnLike
  * Null

    * `"Condition": {"Null": {"aws:TokenIssueTime": "192.168.0.1/16"}}`  *(원문 그대로 유지: 예시는 값이 다소 부자연스러울 수 있음)*

* 정책 변수 및 태그(Policy Variables and Tags)

  * `${aws:username}` 예: `"Resource":["arn:aws:s3:::mybucket/${aws:username}/*"]`
  * AWS 공통 키(AWS Specific)

    * `aws:CurrentTime`
    * `aws:TokenIssueTime`
    * `aws:PrincipalType`: Principal이 계정/사용자/페더레이션/Assumed role 중 무엇인지 표시
    * `aws:SecureTransport`
    * `aws:SourceIp`
    * `aws:UserId`
  * 서비스별 키(Service Specific)

    * `ec2:SourceInstanceARN`
    * `s3:prefix`
    * `s3:max-keys`
    * `sns:EndPoint`
    * `sns:Protocol`
  * 태그 기반(Tag Based)

    * `iam:ResourceTag/key-name`
    * `iam:PrincipalTag/key-name`

## 권한 경계(Permission Boundaries)

* 권한 경계는 **ID 기반 권한(Identity permissions)** 에만 영향을 준다. 리소스 기반 정책은 **그대로(full)** 적용된다.
* 권한 경계는 IAM **사용자(User)** 와 IAM **역할(Role)** 에 적용할 수 있다.
* 권한 경계는 어떤 액션에 대한 접근도 **부여하지 않는다**. ID가 받을 수 있는 **최대 권한**만 정의한다.
* 사용 사례(Use cases)

  * 위임(Delegation) 문제: 사용자에게 높은 권한을 주면, 사용자가 스스로 권한을 승격해 관리자 권한을 얻거나, 관리자 권한을 가진 다른 사용자/역할을 생성할 수 있다.
  * 해결: 경계를 두어 **자기 자신의 권한 변경을 금지**하고, **과도하게 높은 권한을 가진 사용자/역할 생성도 금지**하도록 만든다.

## 권한 평가 로직(Policy Evaluation Logic)

* 권한 평가에 관여하는 구성요소

  * Organization SCPs
  * Resource Policies
  * IAM Identity Boundaries
  * Session Policies
  * Identity Policies
* 정책 평가 로직(같은 계정)

  * ![policy evaluation logic - same account](images/PolicyEvaluation1.png)
* 정책 평가 로직(다른 계정)

  * ![policy evaluation logic - different account](images/PolicyEvaluation2.png)

## AWS Policy Simulator

* 새 커스텀 정책을 만들 때 아래에서 테스트 가능

  * [https://policysim.aws.amazon.com/home/index.jsp](https://policysim.aws.amazon.com/home/index.jsp)
  * 커스텀 정책의 Statement 때문에 거부(Deny)될 때 원인 파악 시간을 줄여준다.
* 대안으로 CLI 사용 가능

  * 일부(전부는 아님) AWS CLI 명령은 `--dry-run` 옵션으로 API 호출을 시뮬레이션할 수 있어 권한 테스트에 활용된다.
  * 성공 시 메시지: `Request would have succeeded, but DryRun flag is set`
  * 실패 시 메시지: `An error occurred (UnauthorizedOperation) when calling the {policy_name} operation`
