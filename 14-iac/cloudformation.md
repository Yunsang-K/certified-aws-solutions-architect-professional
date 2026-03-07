# CloudFormation

## 물리 리소스와 논리 리소스

* CloudFormation은 YAML 또는 JSON 파일로 정의된 템플릿에서 시작한다.
* 템플릿에는 논리 리소스(Logical Resources), 즉 우리가 생성하고자 하는 리소스 정의가 포함된다.
* 템플릿을 사용해 하나 이상의 CloudFormation 스택(Stack)을 생성할 수 있다.
* 스택의 초기 역할은 템플릿에 정의된 논리 리소스를 기반으로 실제 물리 리소스(Physical Resources)를 생성하는 것이다.
* 스택의 템플릿이 변경되면, 이에 따라 물리 리소스도 변경된다.
* 스택이 삭제되면 일반적으로 해당 물리 리소스도 함께 삭제된다.

## 스택

* 스택은 하나의 단위로 관리할 수 있는 AWS 리소스들의 집합이다.

* 스택 내 모든 리소스는 해당 스택의 CloudFormation 템플릿에 의해 정의된다.

* 스택 옵션:

  * 태그(Tags): 스택에 연결되는 키/값 쌍이다. 비용 배분 목적 등으로 스택을 식별하는 데 사용할 수 있다.
  * 권한(Permissions): CloudFormation이 Assume할 수 있는 IAM 서비스 역할이다.
  * 스택 실패 옵션(Stack failure options):

    * 스택 프로비저닝 중 실패가 발생했을 때 무엇을 할지 지정한다.
    * 옵션:

      * 모든 스택 리소스를 롤백
      * 성공적으로 프로비저닝된 리소스는 유지
  * 스택 정책(Stack policy): 스택 업데이트 중 의도치 않은 변경으로부터 보호할 리소스를 정의한다.
  * 롤백 구성(Rollback configuration): 스택 생성 또는 업데이트를 모니터링하면서 임계값이 초과되면 롤백할 수 있다. 예를 들어 어떤 CloudWatch Alarm이 ALARM 상태가 되는 경우 롤백 가능하다.
  * 알림 옵션(Notification options): 알림을 전송할 SNS 토픽을 지정할 수 있다.
  * 스택 생성 옵션(Stack creation options): 아래 옵션은 스택 생성 시에만 사용 가능하며, 업데이트 시에는 사용할 수 없다.

    * Timeout: CloudFormation이 스택 생성 작업에 대해 허용할 최대 시간(분 단위)을 지정한다.

## 템플릿 파라미터와 의사 파라미터

* 템플릿 파라미터(Parameters)는 스택 생성 또는 업데이트 시 콘솔, CLI, API를 통해 입력값을 받을 수 있게 해준다.

* 파라미터는 템플릿 내에 정의되며, 논리 리소스 내부에서 참조할 수 있다.

* 파라미터는 기본값, 허용값, 최소/최대 길이, 허용 패턴, NoEcho, 타입 등을 가질 수 있다.

* NoEcho는 비밀번호처럼 입력값이 화면에 표시되지 않도록 할 때 유용하다.

* 의사 파라미터(Pseudo Parameters):

  * AWS는 CloudFormation 템플릿에서 참조할 수 있는 내장 파라미터를 제공한다.
  * 예시:

    * `AWS::Region`
    * `AWS::StackId`
    * `AWS::StackName`
    * `AWS::AccountId`
  * 의사 파라미터는 사용자가 값을 넣는 것이 아니라, AWS가 자동으로 채워서 참조할 수 있도록 제공하는 값이다.

* 파라미터는 템플릿의 이식성을 높여준다.

* 베스트 프랙티스:

  * 파라미터 수는 가능한 한 최소화하고, 가능하다면 기본값을 제공한다.
  * 가능한 경우 의사 파라미터를 우선 사용한다.

## 내장 함수(Intrinsic Functions)

* 내장 함수는 템플릿 실행 시점까지 값을 알 수 없는 속성에 값을 할당할 때 사용한다.

* 함수 예시:

  * `Ref`, `Fn::GetAtt`: 다른 논리 리소스의 값을 참조
  * `Fn::Join`, `Fn::Split`: 문자열 결합 및 분리
  * `Fn::GetAZs`, `Fn::Select`: 리전의 가용 영역 목록을 가져오고 그중 하나를 선택
  * 조건 함수: `Fn::If`, `And`, `Equals`, `Not`, `Or`
  * `Fn::Base64`, `Fn::Sub`: 문자열을 Base64로 인코딩하거나 변수 치환 수행
  * `Fn::Cidr`: CIDR 블록 생성

* `Fn::GetAZs`

  * 해당 리전에서 사용 가능한 AZ 목록을 반환한다.
  * 리전에 기본 VPC가 구성되어 있다면, 기본 VPC에서 사용 가능한 AZ를 반환한다.

## 매핑(Mappings)

* 템플릿에는 `Mappings` 객체를 포함할 수 있으며, 키와 값의 매핑 구조를 정의할 수 있다.

* 매핑은 1단계 키 또는 2단계 키 구조를 가질 수 있다.

* 매핑은 `Fn::FindInMap` 내장 함수와 함께 사용된다.

* 매핑은 템플릿의 이식성을 높이기 위해 사용된다.

* 예시:

```yaml
Mappings:
  RegionMap:
    us-east-1:
      HVM64: 'ami-xxx'
      HVMG2: 'ami-yyy'
    us-east-2:
      HVM64: 'ami-zzz'
      HVMG2: 'ami-vvv'
```

## 출력값(Outputs)

* 템플릿의 `Outputs` 섹션은 선택 사항이다.

* 이 섹션에서 선언한 값은 CLI나 콘솔에서 출력값으로 확인할 수 있다.

* 중첩 스택(Nested Stack)을 사용하는 경우 상위 스택에서 출력값에 접근할 수 있다.

* Outputs는 export하여 스택 간 참조(Cross-Stack Reference)에 사용할 수 있다.

* 예시:

```yaml
Outputs:
  WordPressUrl:
    Description: 'description text'
    Value: !Join ['', ['https://', !GetAtt Instance.DNSName]]
```

## 조건(Conditions)

* 조건을 사용하면 특정 상황에 따라 인프라를 다르게 구성할 수 있다.

* 조건은 선택적 섹션인 `Conditions`에 선언한다.

* 여러 조건을 정의할 수 있으며, 각 조건은 `TRUE` 또는 `FALSE`로 평가된다.

* 조건은 리소스 생성 전에 평가된다.

* 조건은 `AND`, `EQUALS`, `IF`, `NOT`, `OR` 같은 함수와 함께 사용된다.

* 모든 리소스는 조건을 연결할 수 있으며, 조건 결과에 따라 생성 여부가 결정된다.

* 예를 들어 dev, test, prod 같은 환경에 따라 다른 리소스를 만들도록 할 수 있다.

* 예시:

```yaml
Conditions:
  IsProd: !Equals
    - !Ref EnvType
    - prod
```

* 조건은 중첩해서 사용할 수 있다.

## DependsOn

* `DependsOn`은 리소스 간 의존성을 명시적으로 설정할 때 사용한다.
* CloudFormation은 효율성을 위해 리소스를 병렬로 생성, 업데이트, 삭제하려고 한다.
* 또한 참조나 함수 사용 관계를 바탕으로 의존 순서를 자동 추론한다. 예를 들어 VPC → Subnet → EC2 순서다.
* 명시적인 의존성이 필요할 경우 `DependsOn` 속성을 사용해 어떤 리소스에 의존하는지 지정할 수 있다.
* `DependsOn`은 단일 리소스 또는 리소스 목록을 받을 수 있다.

## 생성 정책, 대기 조건, cfn-signal

* CreationPolicy, WaitCondition, cfn-signal은 리소스 생성 완료 여부를 CloudFormation에 신호로 전달하는 방법을 제공한다.
* CloudFormation이 특정 개수의 성공 신호를 기다리도록 설정할 수 있다.
* 신호가 도착해야 하는 타임아웃도 설정할 수 있으며, 최대 12시간까지 가능하다.
* 필요한 수의 성공 신호가 타임아웃 내에 도착하면 스택 상태는 `CREATE_COMPLETE`가 된다.
* `cfn-signal`은 EC2 인스턴스에서 실행되어 CloudFormation에 성공 또는 실패 신호를 보내는 유틸리티다.
* 타임아웃이 지나도록 필요한 성공 신호 수를 충족하지 못하면 스택 생성은 실패한다.
* EC2 또는 Auto Scaling Group 프로비저닝에는 보통 `CreationPolicy`를 사용하는 것이 적절하다.
* 그 외 일반적인 대기/동기화 목적에는 `WaitCondition`을 사용할 수 있다.
* `WaitCondition`은 하나의 논리 리소스로 정의되므로 `DependsOn`을 가질 수 있다. 템플릿에서 일반적인 진행 게이트 역할을 할 수 있다.
* `WaitCondition`은 또 다른 논리 리소스인 `WaitHandle`에 의존한다.
* `WaitHandle`은 presigned URL을 생성하며, 이 URL을 통해 `WaitCondition`에 신호를 전달할 수 있다.
* `WaitHandle`을 통해 템플릿에 데이터를 되돌려 보낼 수도 있다.
* 이 데이터는 `!GetAtt WaitCondition.Data`로 조회할 수 있다.

## 중첩 스택(Nested Stacks)

* 대부분의 단순한 프로젝트는 하나의 CloudFormation 스택으로 구성된다.

* 하지만 스택에는 한계가 있다.

  * 스택당 리소스 한도: 500개
  * 이미 생성된 리소스를 쉽게 재사용하기 어렵다. 예를 들어 기존 VPC 참조가 불편할 수 있다.

* 멀티 스택 아키텍처 방식은 2가지가 있다.

  * Nested Stacks
  * Cross-Stack References

* Nested Stacks:

  * 루트 스택(Root Stack): 가장 먼저 생성되는 스택으로, 수동 또는 자동화로 생성된다.
  * 부모 스택(Parent Stack): 자신이 직접 생성한 다른 스택의 부모가 된다.
  * 하나의 루트 스택은 여러 부모-자식 구조의 중첩 스택을 만들 수 있다.
  * 루트 스택도 일반 스택처럼 파라미터와 출력값을 가질 수 있다.

* 스택은 `AWS::CloudFormation::Stack` 타입 리소스를 사용해 다른 CloudFormation 스택을 리소스로 포함할 수 있다.

* 이때 중첩 템플릿의 URL이 필요하다.

* 중첩 스택에 입력값을 전달할 수 있다.

* 중첩 스택의 파라미터 중 기본값이 없는 경우 반드시 값을 넘겨야 한다.

* 중첩 스택의 출력값은 루트 스택으로 반환되며 `NestedStack.Outputs.XXX` 형식으로 참조할 수 있다.

* Nested Stack의 장점은 이미 생성된 스택 자체를 재사용하는 것이 아니라 템플릿을 재사용할 수 있다는 점이다.

* 서로 생명주기를 함께 가져가야 하는 스택들에는 Nested Stack이 적합하다.

## 스택 간 참조(Cross-Stack References)

* CloudFormation 스택은 기본적으로 서로 격리되고 독립적으로 설계된다.
* Nested Stack은 코드 재사용에 가깝고, Cross-Stack Reference는 다른 스택이 생성한 실제 리소스를 참조할 수 있게 해준다.
* 일반적으로 Outputs는 다른 스택에서 보이지 않는다.
* 예외적으로 Nested Stack에서는 부모 스택이 자식 스택의 Output을 참조할 수 있다.
* 템플릿의 Outputs를 export하면 다른 스택에서도 볼 수 있게 된다.
* Export 이름은 동일 리전 내에서 유일해야 한다.
* Export된 값을 사용할 때는 `Fn::ImportValue` 내장 함수를 사용한다.
* Cross-Stack Reference는 리전 간 또는 계정 간에는 지원되지 않는다.

## StackSets

* StackSets는 여러 리전과 여러 계정에 걸쳐 인프라를 생성, 업데이트, 삭제할 수 있게 해준다.

* StackSet은 관리자 계정에 존재하는 컨테이너 개념이다.

* 이 관리자 계정은 꼭 특별한 계정일 필요는 없고, StackSet을 적용하는 계정이면 된다.

* StackSet 안에는 스택 인스턴스(Stack instances)가 들어 있으며, 이는 개별 스택을 위한 컨테이너 역할을 한다.

* 실제 스택 인스턴스와 스택은 대상 계정에 생성된다.

* StackSet이 만드는 각 스택은 “한 계정의 한 리전”에 생성되는 하나의 스택이다.

* 보안:

  * Self-managed roles 또는 Service-managed roles를 사용할 수 있다.
  * CloudFormation은 대상 계정과 상호작용하기 위해 역할을 Assume한다.

* 용어:

  * Concurrent Accounts: 동시에 배포를 수행할 계정 수
  * Failure Tolerance: 몇 개의 개별 배포 실패까지 StackSet 전체 실패로 간주하지 않을 것인지
  * Retain Stacks: StackSet에서 스택 인스턴스를 제거하지만 실제 인프라는 유지

* StackSet 사용 사례:

  * AWS Config Rules 배포
  * 계정 간 접근용 IAM Role 생성

## DeletionPolicy

* 템플릿에서 어떤 논리 리소스를 삭제하면 기본적으로 해당 물리 리소스도 CloudFormation에 의해 삭제된다.

* 그러나 일부 리소스는 이렇게 삭제하면 데이터 손실이 발생할 수 있다.

* `DeletionPolicy`를 사용하면 각 리소스별로 다음 동작을 정의할 수 있다.

  * `Delete` 기본값
  * `Retain`
  * `Snapshot` 지원되는 리소스에 한함

* Snapshot을 사용하면 물리 리소스를 삭제하기 전에 스냅샷을 먼저 생성한다.

* Snapshot 지원 리소스 예시:

  * EBS 볼륨
  * ElastiCache
  * Neptune
  * RDS
  * Redshift

* 주의:

  * `DeletionPolicy`는 삭제 작업에만 적용된다.
  * 교체(Replace) 작업에는 적용되지 않는다.

## 스택 역할(Stack Roles)

* 기본적으로 CloudFormation은 스택 생성을 시작한 사용자 또는 역할의 권한을 사용한다.
* Stack Role 기능을 사용하면, 스택 생성자를 대신해 CloudFormation이 특정 역할을 Assume하여 리소스를 생성할 수 있다.
* 따라서 스택 생성 주체는 실제 리소스 생성 권한이 없어도 되고, `iam:PassRole` 권한만 있으면 된다.

## `AWS::CloudFormation::Init` 와 `cfn-init`

* CloudFormation Init은 간단한 구성 관리 시스템이다.
* 구성 지시문은 템플릿 안에 저장된다.
* `AWS::CloudFormation::Init`은 EC2 인스턴스 논리 리소스의 일부로 정의된다.
* 이를 통해 생성된 EC2 인스턴스에 적용할 구성을 지정할 수 있다.
* User Data는 절차 중심이다. 즉 “어떻게 할 것인가(HOW)”를 기술한다.
* 반면 `cfn-init`은 원하는 상태 중심이다. 즉 “무엇을 만들 것인가(WHAT)”를 기술한다.
* `cfn-init`은 크로스 플랫폼으로 사용할 수 있고, 멱등성을 가진다.
* CloudFormation Init 데이터는 인스턴스에 설치된 `cfn-init` 헬퍼 스크립트를 통해 적용된다.

## `cfn-hup`

* `cfn-init`은 부트스트랩 과정(User Data)에서 한 번 실행되는 헬퍼 도구다.
* 이후 `AWS::CloudFormation::Init` 내용이 업데이트되더라도 `cfn-init`은 자동으로 다시 실행되지 않는다.
* `cfn-hup`은 EC2 인스턴스에 설치할 수 있는 헬퍼 도구다.
* 이 도구는 리소스 메타데이터의 변경을 감지한다.
* 변경이 감지되면 설정된 작업을 실행할 수 있다.
* 필요하다면 `cfn-init`을 다시 실행하도록 구성할 수 있다.

## Change Sets

* Change Set은 스택 업데이트 전에 어떤 변경이 발생할지 미리 확인할 수 있게 해준다.
* 여러 개의 Change Set을 만들어 각각 미리 검토할 수 있다.
* 그중 실제로 적용할 Change Set을 선택해서 실행할 수 있다.

## Custom Resources

* CloudFormation은 AWS의 모든 기능을 기본적으로 지원하지는 않는다.

* Custom Resource를 사용하면 CloudFormation이 기본 지원하지 않는 작업도 통합할 수 있다.

* 이를 통해 CloudFormation 기능을 확장할 수 있다.

* 예를 들어 써드파티에서 설정 정보를 가져오는 작업 등을 수행할 수 있다.

* Custom Resource 아키텍처:

  * CloudFormation은 Custom Resource에 정의된 엔드포인트로 데이터를 전송한다.
  * 이 엔드포인트는 Lambda 함수일 수도 있고 SNS 토픽일 수도 있다.
  * Custom Resource가 생성, 업데이트, 삭제될 때 CloudFormation은 작업 유형과 추가 속성 정보를 포함한 이벤트를 이 엔드포인트로 전달한다.
  * 해당 컴퓨트 예: Lambda 함수는 이 이벤트를 처리한 뒤 성공 또는 실패를 CloudFormation에 반환할 수 있다.

