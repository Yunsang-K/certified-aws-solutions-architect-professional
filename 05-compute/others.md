# Other Compute Related Products

## AWS Outposts

* **AWS Outposts**는 AWS 인프라, 서비스, API, 도구를 **고객 온프레미스(자체 데이터센터/사무실)** 로 확장해 주는 **완전 관리형 서비스**입니다.
* **Outpost**는 고객 사이트에 배치되는 **AWS 컴퓨팅 및 스토리지 용량 풀(pool)** 입니다.
* AWS는 이 용량을 **하나의 AWS 리전 구성 요소처럼** 운영, 모니터링, 관리합니다.
* Outposts에서 **서브넷**을 생성할 수 있으며, 이를 지정해 다음과 같은 AWS 리소스를 생성할 수 있습니다.

  * EC2 인스턴스
  * EBS 볼륨
  * ECS 클러스터
  * RDS 인스턴스
* Outpost 서브넷의 인스턴스는 **동일 VPC 내부에서 프라이빗 IP** 를 사용해 AWS 리전의 다른 인스턴스와 통신할 수 있습니다.
* 모든 AWS 서비스가 Outposts에서 지원되는 것은 아닙니다. 지원 서비스 목록은 문서에서 확인합니다.

## AWS Wavelength

* **AWS Wavelength**는 통신사(캐리어)의 **5G 엣지 네트워크** 안에 **AZ(가용 영역)처럼** AWS 컴퓨팅/스토리지 서비스를 배치하는 방식입니다.
* Wavelength는 표준 AWS 컴퓨팅 및 스토리지 서비스를 **통신사 5G 네트워크 엣지** 로 확장합니다.
* VPC를 하나 이상의 **Wavelength Zone** 으로 확장할 수 있습니다.
* 이후 **초저지연 또는 엣지 탄력성**이 필요한 애플리케이션을 Wavelength Zone 내에서 실행하도록 EC2 같은 리소스를 배치할 수 있습니다.
* Wavelength에서 사용할 수 있는 AWS 리소스 예시는 다음과 같습니다.

  * EC2 Auto Scaling
  * EKS 클러스터
  * ECS 클러스터
  * EC2 Systems Manager
  * CloudWatch, CloudTrail
  * CloudFormation
  * Application Load Balancer
* Wavelength의 서비스들은 **VPC의 일부**이며, **신뢰성 있는 연결**을 통해 AWS 리전과 연결됩니다.

### 시험 관점 한 줄 정리 (SAP-Pro 스타일)

* **Outposts**: “온프레미스에 AWS 하드웨어를 들여놓고, 리전과 동일 운영 모델로 하이브리드 구축”
* **Wavelength**: “통신사 5G 엣지에 AWS를 배치해 초저지연 모바일/엣지 워크로드 처리”
