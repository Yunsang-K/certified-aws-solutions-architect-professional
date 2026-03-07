# Elastic Beanstalk - EB

* AWS의 PaaS(Platform as a Service) 제품이다. 즉, 벤더가 인프라를 처리하고 우리는 코드만 제공한다.
* EB는 개발자 중심의 서비스로, 관리형 애플리케이션 환경을 제공한다.
* 큰 관점에서 보면 개발자는 코드를 제공하고, EB가 인프라를 처리한다.
* EB는 완전히 커스터마이즈 가능하다. 내부적으로는 CloudFormation으로 프로비저닝된 AWS 서비스들을 사용한다.
* EB를 사용하려면 애플리케이션 차원의 지원이 필요하며, 개발자가 해야 할 작업도 있다. 공짜로 얻어지는 것이 아니며, 비기술적인 일반 사용자가 다루기 쉬운 서비스도 아니다.

## Platforms

* EB는 여러 언어의 코드를 받을 수 있으며, 이를 플랫폼이라고 부른다.
* EB는 기본 제공 언어, Docker, 그리고 커스텀 플랫폼을 지원한다.
* 기본 지원 언어: Go, Java SE, Java Tomcat, .NET Core (Linux), .NET (Windows), Node.js, PHP, Python, Ruby
* Docker 옵션:

  * Single Container Docker
  * Multi-Container Docker(ECS)
* Pre-configured Docker:

  * 아직 네이티브 지원되지 않는 런타임을 제공하는 방식
  * 예: GlassFish 기반 Java
* Packer를 사용해 자체 커스텀 플랫폼을 만들어 Beanstalk와 함께 사용할 수 있다.

## EB Terminology

* **Elastic Beanstalk Application**:

  * 애플리케이션과 관련된 여러 요소를 담는 논리적 컨테이너/폴더
* **Application Version**:

  * 애플리케이션의 배포 가능한 코드에 붙는 특정 라벨 버전
  * 소스 번들은 S3에 저장된다.
* **Environments**:

  * 특정 버전을 실행하기 위한 인프라와 설정의 컨테이너
* 각 환경은 다음 둘 중 하나이다.

  * **Web Server Tier**: 최종 사용자와 통신하도록 설계된 계층
  * **Worker Tier**: Web Tier에서 전달된 작업을 처리하는 계층
* Web Server Tier와 Worker Tier는 SQS 큐를 통해 통신한다.
* 각 환경은 특정 시점에 하나의 버전을 실행한다.
* 각 환경은 자체 CNAME을 가지며, CNAME Swap을 통해 두 환경의 DNS를 교환할 수 있다.

## Deployment Policies

* **All at once**

  * 모든 인스턴스에 한 번에 배포한다.
  * 빠르고 단순하지만 짧은 서비스 중단이 발생한다.
  * 테스트 및 개발 환경에 적합하다.

* **Rolling**

  * 애플리케이션 코드를 배치 단위로 순차 배포한다.
  * 이전 배치가 정상 상태일 때만 다음 배치로 진행하므로 더 안전하다.
  * 선택한 배치 크기만큼 일시적으로 처리 용량이 줄어든다.
  * 동시에 서비스에서 빠져도 되는 인스턴스 수를 기준으로 배치 크기를 정해야 한다.

* **Rolling with additional batch**

  * Rolling 배포와 동일하지만, 추가 배치를 하나 더 두어 배포 중에도 용량을 유지한다.
  * 실제 트래픽이 있는 프로덕션 환경에 권장된다.
  * 배포 시간은 더 오래 걸리지만, 용량 저하 없이 더 안전하게 배포할 수 있다.

* **Immutable**

  * 새 버전의 애플리케이션을 위해 임시 ASG를 새로 생성한다.
  * 검증이 끝나면 기존 스택을 제거한다.
  * 문제 발생 시 원래 인스턴스가 기존 상태로 남아 있으므로 롤백이 쉽다.
  * 인스턴스를 거의 2배 사용하므로 비용이 가장 높다.

* **Traffic Splitting**

  * Immutable 배포처럼 새 인스턴스를 생성한다.
  * 기존 버전과 새 버전 사이에 트래픽을 분산한다.
  * 애플리케이션 A/B 테스트가 가능하다.
  * 용량 저하는 없지만 추가 비용이 발생한다.

* **Blue/Green**

  * EB가 자동으로 지원하지는 않는다.
  * 두 환경 사이에서 수동으로 CNAME Swap을 수행해야 한다.
  * 언제 새 환경으로 전환할지 완전히 제어할 수 있다.

## EB and RDS

* EB에서 RDS에 접근하려면 EB 환경 내부에 RDS 인스턴스를 생성할 수 있다.

* 이렇게 하면 RDS는 해당 환경에 연결된다.

* 환경을 삭제하면 데이터베이스도 함께 삭제된다.

* 데이터베이스를 환경에 연결하면 다음 환경 속성을 사용할 수 있다.

  * `RDS_HOSTNAME`
  * `RDS_PORT`
  * `RDS_DB_NAME`
  * `RDS_USERNAME`
  * `RDS_PASSWORD`

* 다른 방법으로는 EB 외부에서 RDS 인스턴스를 별도로 생성하는 것이다.

* 이 경우 위 환경 속성들은 자동 제공되지 않으므로 직접 생성해야 한다.

* 이 방식에서는 RDS 수명 주기가 EB 환경과 분리된다.

* 기존 RDS를 EB 환경에서 분리하는 절차:

  1. 스냅샷 생성
  2. Delete Protection 활성화
  3. 동일한 애플리케이션 버전으로 새 EB 환경 생성
  4. 새 환경이 DB에 연결 가능한지 확인
  5. 환경 전환(CNAME 또는 DNS)
  6. 기존 환경 종료

     * 이 과정에서 RDS 종료를 시도한다.
  7. CloudFormation 콘솔에서 `DELETE_FAILED` 스택을 찾은 뒤, 수동 삭제하면서 남아 있는 리소스는 retain 처리

## Customizing via `.ebextensions`

* `.ebextensions` 폴더를 사용해 EB 환경을 커스터마이즈할 수 있다.
* 이 폴더 안에 있는 YAML 또는 JSON 형식의 `.config` 파일은 EB 확장 설정 파일로 인식된다.
* 이 설정 파일들은 CloudFormation 정의이며, EB 환경 자체의 설정을 변경하거나 새로운 리소스를 프로비저닝하는 데 사용된다.
* EB는 `.config` 파일에 지정된 내용을 바탕으로 CloudFormation을 사용해 환경 내 추가 리소스를 생성한다.
* 이 파일들은 다음 섹션을 가질 수 있다.

  * `option_settings`

    * 리소스 옵션 설정
    * 예: EB 환경에서 ALB 대신 NLB 사용
  * `Resources`

    * CloudFormation으로 새 리소스 생성
    * 예: 환경용 OpenSearch 클러스터 구성
  * 추가 섹션

    * `packages`
    * `sources`
    * `files`
    * `users`
    * `groups`
    * `commands`
    * `container_commands`
    * `services`

## EB with HTTPS

* EB에서 HTTPS를 사용하려면 로드 밸런서에 SSL 인증서를 적용해야 한다.
* 이는 EB 콘솔에서 설정하거나 `.ebextensions/securelistener-[alb|nlb].config` 기능으로 구성할 수 있다.
* 또한 보안 그룹을 설정해 SSL 연결을 허용할 수 있다.

## Environment Cloning

* 클로닝은 기존 환경을 복제하여 새로운 EB 환경을 만드는 기능이다.
* 환경을 클론하면 옵션, 환경 변수, 리소스, 기타 설정을 수동으로 다시 구성할 필요가 없다.
* 클론 시 RDS 인스턴스 정의도 복사되지만, 데이터 자체는 기본적으로 복사되지 않는다.
* 클로닝에는 환경 리소스에 대해 EB 콘솔/CLI/API 외부에서 수행한 비관리형 변경 사항은 포함되지 않는다.
* 명령줄에서 환경을 복제하려면 다음 명령을 사용한다.

  * `eb clone <ENV>`

## EB and Docker

### Single Container Mode

* 하나의 Docker 호스트에서 하나의 컨테이너만 실행할 수 있다.
* 이 모드는 ECS가 아니라 Docker가 설치된 EC2를 사용한다.
* 이 모드를 사용하려면 몇 가지 구성이 필요하다.

  * `Dockerfile`

    * 새 컨테이너 이미지를 빌드하는 데 사용
  * `Dockerrun.aws.json` (version 1)

    * 기존 Docker 이미지를 사용할 때 사용
    * 포트, 볼륨 및 기타 Docker 속성을 설정 가능
  * `docker-compose.yml`

    * Docker Compose를 사용할 경우 필요

### Multi-Container Mode

* Elastic Beanstalk는 ECS를 사용해 클러스터를 생성한다.
* ECS는 클러스터 내에 프로비저닝된 EC2 인스턴스와 고가용성을 위한 ELB를 사용한다.
* EB는 ECS 태스크, 클러스터 생성, 태스크 정의, 태스크 실행을 관리한다.
* 애플리케이션 소스 번들 루트에 `Dockerrun.aws.json` version 3 파일을 제공해야 한다.
* 이미지는 ECR 같은 컨테이너 레지스트리에 저장되어 있어야 한다.
