## CI/CD

* CI/CD 아키텍처
  ![CI/CD architecture](images/CICD1.png)
* 브랜치 아키텍처
  ![Branching architecture](images/CICD2.png)
* CodePipeline
  ![Code pipeline](images/CICD3.png)
* 각 파이프라인은 여러 stage로 구성된다.
* 각 파이프라인은 리포지토리의 단일 브랜치와 연결되는 것이 바람직하다.
  ![Code deployment](images/CICD4.png)
* CodeBuild / CodeDeploy 설정 파일

  * `buildspec.yml`, `appspec.[yml|json]`
  * 참고: `appspec` 파일 레퍼런스
  * `buildspec.yml`은 CodeBuild에서 빌드 프로세스가 어떻게 수행될지 제어한다.
  * `appspec`은 CodeDeploy에서 배포 프로세스가 어떻게 진행될지 제어한다.

## AWS CodeCommit

* 관리형 Git 서비스이다.
* CodeCommit의 기본 단위는 리포지토리(repository)이다.
* 인증은 IAM 콘솔을 통해 구성할 수 있다.
* CodeCommit은 HTTPS, SSH, HTTPS over GRPC를 지원한다.

### 트리거 및 알림

* **Notification rules**

  * 리포지토리에서 발생하는 이벤트를 기준으로 알림을 보낼 수 있다.
  * 예: 커밋, Pull Request, 상태 변경 등
  * 알림 대상은 SNS 토픽 또는 AWS Chatbot이다.
* **Triggers**

  * 리포지토리에서 일어난 이벤트를 기반으로 이벤트 드리븐 프로세스를 실행할 수 있다.
  * 이벤트는 SNS 또는 Lambda 함수로 전달할 수 있다.

## AWS CodePipeline

* 지속적 전달(Continuous Delivery) 도구이다.
* 소스 코드에서 시작해 빌드와 배포까지의 흐름을 제어한다.
* 파이프라인은 여러 stage로 구성된다.
* stage 안에는 action이 있으며, 순차 실행 또는 병렬 실행이 가능하다.
* stage 간 이동은 자동으로 진행되거나 수동 승인(manual approval)을 요구할 수 있다.
* stage 내 action은 artifact를 소비하거나 생성할 수 있다.
* artifact는 action이 생성하거나 사용하는 파일이다.
* 파이프라인, stage, action의 상태 변경은 모두 EventBridge 이벤트로 게시된다.
* CloudTrail로 API 호출을 모니터링할 수 있다.
* 콘솔 UI에서 파이프라인을 확인하고 조작할 수 있다.

## AWS CodeBuild

* Build as a Service 형태의 서비스이다.
* 완전관리형 서비스이며, 빌드 중 사용한 리소스에 대해서만 비용을 지불한다.
* Jenkins 같은 서드파티 빌드 솔루션의 대안이다.
* CodeBuild는 빌드 환경으로 Docker를 사용하며, 사용자가 이를 커스터마이징할 수 있다.
* KMS, IAM, VPC, CloudTrail, S3 등 다양한 AWS 서비스와 통합된다.
* 아키텍처 관점에서 CodeBuild는 GitHub, CodeCommit, CodePipeline, S3 등에서 소스를 가져올 수 있다.
* 코드 빌드와 테스트를 수행한다.
* 빌드 동작은 `buildspec.yml` 파일로 커스터마이징한다.
* 이 파일은 **반드시 소스 루트(root)에 위치**해야 한다.
  **시험 포인트: 파일명과 위치를 정확히 기억**
* 빌드 로그는 CloudWatch Logs로 전송된다.
* 메트릭은 CloudWatch Metrics로 전송된다.
* 이벤트는 EventBridge(구 CloudWatch Events)로 전송된다.
* Java, Ruby, Python, Node.js, PHP, .NET, Go 등 다양한 빌드 환경을 지원한다.

### `buildspec.yml`

* 빌드 프로세스를 커스터마이징하는 파일이다.
* 리포지토리의 루트 폴더에 위치해야 한다.
* 주요 4개 phase를 포함할 수 있다.

  * `install`

    * 빌드 환경에 필요한 패키지를 설치한다.
  * `pre_build`

    * 로그인 처리, 의존성 설치 등 빌드 전 작업을 수행한다.
  * `build`

    * 실제 빌드 명령을 실행한다.
  * `post_build`

    * 아티팩트 패키징, Docker 이미지 푸시, 명시적 알림 전송 등을 수행한다.
* 환경 변수도 포함할 수 있다.

  * shell
  * variables
  * parameter-store
  * secrets-manager 변수
* `artifacts` 섹션에서는 어떤 결과물을 어디에 저장할지 지정한다.

## AWS CodeDeploy

* Deployment as a Service 형태의 서비스이다.
* Jenkins, Ansible, Chef, Puppet, 심지어 CloudFormation과도 비교되는 배포 도구이지만, 목적은 다르다.
* **리소스를 배포하는 서비스가 아니라 코드를 배포하는 서비스**이다.

  * 리소스 배포는 CloudFormation을 사용한다.
* EC2, 온프레미스, Lambda, ECS에 코드 배포가 가능하다.
* 코드뿐 아니라 설정 파일, 실행 파일, 패키지, 스크립트, 미디어 등도 배포할 수 있다.
* KMS, IAM, VPC, CloudTrail, S3 등 AWS 서비스와 통합된다.
* EC2 및 온프레미스 배포 시에는 CodeDeploy Agent가 필요하다.

### `appspec.[yaml|json]`

* 대상 환경에서 배포가 어떻게 이뤄질지 제어하는 파일이다.
* 배포 설정과 라이프사이클 이벤트 훅을 정의한다.

#### Configuration section

중요한 3개 섹션이 있다.

* **Files**

  * EC2 / 온프레미스에 적용된다.
  * 어떤 파일을 인스턴스 어디에 설치할지 정의한다.
* **Resources**

  * ECS / Lambda에 적용된다.
  * Lambda의 경우 함수 이름, alias, 현재 버전, 대상 버전 등을 포함한다.
  * ECS의 경우 task definition, 컨테이너 정보, 포트, 트래픽 라우팅 등을 포함한다.
* **Permissions**

  * EC2 / 온프레미스에 적용된다.
  * `Files` 섹션에서 배치한 파일 및 폴더에 대해 어떤 권한을 어떻게 적용할지 정의한다.

#### Lifecycle event hooks

* `ApplicationStop`

  * 애플리케이션 다운로드 전에 실행된다.
  * 애플리케이션을 정상적으로 중지하는 데 사용된다.
* `DownloadBundle`

  * 에이전트가 애플리케이션을 임시 위치에 복사한다.
* `BeforeInstall`

  * 설치 전 작업을 수행한다.
* `Install`

  * 에이전트가 임시 폴더의 애플리케이션을 최종 위치로 복사한다.
* `AfterInstall`

  * 설치 후 작업을 수행한다.
* `ApplicationStart`

  * `ApplicationStop`에서 중지했던 서비스를 다시 시작한다.
* `ValidateService`

  * 배포가 성공적으로 완료되었는지 검증한다.
    **시험 포인트: 매우 중요**

## SAP-C02 관점 핵심 암기 포인트

* **CodePipeline** = 전체 흐름 오케스트레이션
* **CodeBuild** = 빌드 및 테스트
* **CodeDeploy** = 코드 배포
* **CodeCommit** = Git 리포지토리
* `buildspec.yml` = **CodeBuild**, **루트 경로**
* `appspec.yml/json` = **CodeDeploy**, **배포 방식/훅 정의**
* **CodeDeploy는 리소스 생성용이 아님**

  * 리소스 프로비저닝은 CloudFormation
* **EC2 / 온프레미스에 CodeDeploy 사용 시 Agent 필요**
* `ValidateService` = 배포 완료 검증 단계
