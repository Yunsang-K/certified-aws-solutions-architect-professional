## AWS Systems Manager (SSM)

* AWS 및 온프레미스 인프라를 관리하고 제어할 수 있게 해주는 서비스이다.
* SSM은 **에이전트 기반 서비스**이므로, Windows 및 Linux 기반 AMI에는 SSM Agent가 설치되어 있어야 한다.
* SSM은 인벤토리 관리 기능을 제공한다. 예를 들어 설치된 애플리케이션, 파일, 네트워크 설정, 하드웨어 정보, 서비스 등의 정보를 수집할 수 있다. 또한 자산 패치도 수행할 수 있다.
* 명령 실행도 가능하며, 인스턴스의 원하는 상태(desired state)도 관리할 수 있다. 예를 들어 특정 포트를 차단하도록 구성할 수 있다.
* 설정값과 시크릿을 저장할 수 있는 **Parameter Store**도 제공한다.
* 마지막으로 **Session Manager**를 통해, 프라이빗 VPC 내부의 EC2 인스턴스에도 안전하게 접속할 수 있다.

## 에이전트 아키텍처

* 인스턴스를 SSM으로 관리하려면 해당 인스턴스에 **SSM Agent**가 설치되어 있어야 한다.
* 또한 SSM 서비스와 통신할 수 있도록 필요한 권한이 포함된 **EC2 인스턴스 역할(Instance Role)** 이 연결되어 있어야 한다.
* 인스턴스는 SSM 서비스와의 네트워크 연결도 필요하다. 이는 **인터넷 게이트웨이(IGW)** 또는 **VPC 엔드포인트(VPCE)** 를 통해 구성할 수 있다.
* 온프레미스 환경에서는 **Managed Instance Activation**을 생성해야 한다.
* 각 Activation마다 **Activation Code**와 **Activation ID**가 발급된다.

## SSM Run Command

* 관리 대상 인스턴스에서 명령을 실행할 수 있는 기능이다.
* **Command Document**를 사용해 명령을 정의하고, 인스턴스에 설치된 에이전트를 통해 실행한다.
* 이 과정은 SSH나 RDP 프로토콜 없이 수행된다.
* Command Document는 개별 인스턴스, 태그 기반의 여러 인스턴스, 또는 리소스 그룹 단위로 실행할 수 있다.
* Command Document는 재사용 가능하며, 입력 파라미터를 받을 수도 있다.
* **Rate Control** 기능을 통해 대량의 인스턴스에 명령을 실행할 때 실행 속도를 제어할 수 있다.

  * **Concurrency**: 동시에 몇 개의 인스턴스에서 명령을 실행할지 정의한다.
  * **Error Threshold**: 개별 명령 실패를 몇 개까지 허용할지 정의한다.
* 명령 실행 결과는 S3로 보낼 수 있고, SNS 알림으로도 전송할 수 있다.
* 명령은 EventBridge와 연동할 수 있다.

## SSM Patch Manager

* EC2 또는 온프레미스에서 실행 중인 Linux 및 Windows 인스턴스를 패치할 수 있게 해준다.

### 주요 개념

* **Patch Baseline**
  여러 개를 정의할 수 있으며, 어떤 패치와 핫픽스를 설치해야 하는지 정의한다.

* **Patch Groups**
  어떤 리소스를 패치 대상으로 할지 지정한다.

* **Maintenance Windows**
  패치를 수행할 수 있는 시간대를 정의한다.

* **Run Command**
  패치 수행의 기반 기능이다. 패치에 사용되는 명령은 `AWS-RunPatchBaseline` 이다.

* **Concurrency** 및 **Error Threshold**
  대규모 패치 실행 시 동시성 및 허용 실패 수를 제어한다.

* **Compliance**
  패치 적용 후, System Manager가 예상 상태와 비교하여 규정 준수 여부를 확인할 수 있다.

### Patch Baseline 종류

* **Linux**

  * `AWS-[OS]DefaultPatchBaseline`
  * 명시적으로 어떤 패치를 설치할지 정의한다.
  * 예시:

    * `AWS-AmazonLinux2DefaultPatchBaseline`
    * `AWS-UbuntuDefaultPatchBaseline`
  * 일반적으로 보안 업데이트 및 중요 업데이트를 포함한다.

* **Windows**

  * `AWS-DefaultPatchBaseline`
  * `AWS-WindowsPredefinedPatchBaseline-OS`
  * `AWS-WindowsPredefinedPatchBaseline-OS-Application`
  * Microsoft 애플리케이션용 패치까지 포함할 수 있다.
