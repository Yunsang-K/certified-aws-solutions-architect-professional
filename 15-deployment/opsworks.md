# AWS OpsWorks

* OpsWorks는 **구성 관리(Configuration Management) 서비스**로, AWS가 관리형으로 제공하는 **Chef 및 Puppet 구현체**입니다.
* OpsWorks는 다음 **3가지 모드** 중 하나로 동작합니다.

  * **Puppet Enterprise**: AWS 관리형 **Puppet Master 서버**를 생성할 수 있습니다. (Desired State 아키텍처)
  * **Chef Automate**: AWS 관리형 **Chef 서버**를 생성할 수 있습니다. (IaC와 유사하며, Ruby 기반의 단계적 작업 집합)
  * **OpsWorks**: 서버를 직접 관리하지 않는 AWS의 Chef 구현입니다. 기본적인 Chef 기능을 제공하며 관리 오버헤드가 적습니다.
* 일반적으로 **Chef 또는 Puppet 사용이 요구되는 경우에만** 선택하는 것이 좋습니다. 예를 들어 마이그레이션 상황에서 그렇습니다.
* 또 다른 사용 사례는 **자동화 요구 사항이 있는 경우**입니다.
* 문제에서 **Recipes, Cookbook, Manifest** 같은 표현이 나오면, 위 세 가지 옵션 중 하나를 떠올리면 됩니다.

## OpsWorks 모드

* **Stacks**

  * OpsWorks의 핵심 구성 요소입니다.
  * CloudFormation 스택과 유사한 **리소스 컨테이너**입니다.

* **Layers**

  * 스택 내에서 **특정 역할이나 기능**을 나타냅니다.
  * 예를 들어 로드 밸런서 계층, 데이터베이스 계층, 웹 애플리케이션이 실행되는 EC2 인스턴스 계층 등이 있습니다.

* **Recipes** 및 **Cookbooks**

  * 레이어에 적용됩니다.
  * 패키지 설치, 애플리케이션 배포, 스크립트 실행, 재구성 작업 수행 등에 사용할 수 있습니다.
  * **Cookbook**은 여러 **Recipe의 모음**이며, GitHub 등에 저장할 수 있습니다.

* **Lifecycle Events**

  * 레이어에서 실행되는 특별한 이벤트입니다.
  * 예시는 다음과 같습니다.

    * **Setup**
    * **Configure**: 일반적으로 인스턴스가 스택에 추가되거나 제거될 때 실행됩니다. 기존 인스턴스를 포함한 **모든 인스턴스**에서 실행됩니다.
    * **Deploy**
    * **Undeploy**
    * **Shutdown**

* **Instances**

  * 컴퓨팅 인스턴스입니다. (EC2 인스턴스 또는 온프레미스 서버)
  * 다음과 같은 유형이 있습니다.

    * **24/7 인스턴스**: 수동으로 시작
    * **시간 기반(Time-Based) 인스턴스**: 스케줄에 따라 시작 및 중지
    * **부하 기반(Load-Based) 인스턴스**: 시스템 메트릭에 따라 자동으로 켜지거나 꺼짐 (ASG와 유사)

* **인스턴스 자동 복구(Auto-healing)**

  * 어떤 이유로든 인스턴스에 장애가 발생하면 OpsWorks가 자동으로 재시작합니다.

* **Apps**

  * S3 같은 리포지토리에 저장할 수 있습니다.
  * 각 앱은 하나의 **OpsWorks App**으로 표현되며, 애플리케이션 유형과 리포지토리에서 인스턴스로 배포하는 데 필요한 정보를 포함합니다.

* **OpsWorks 아키텍처**

  * ![OpsWorks architecture](images/AWSOpsWorks.png)
