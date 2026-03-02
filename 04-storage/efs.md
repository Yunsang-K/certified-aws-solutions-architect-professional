# EFS(Elastic File System)

* **네트워크 기반 파일 시스템**으로, **Linux 기반 인스턴스에 마운트**할 수 있다.
* **여러 EC2 인스턴스가 동시에 마운트**해서 공유 스토리지로 사용할 수 있다.
* EFS는 **NFSv4 프로토콜**을 구현한 서비스다.
* Linux OS에서 특정 디렉터리(폴더)로 **마운트 포인트를 지정**해 사용한다.
* 스토리지는 EC2 인스턴스의 수명 주기와 **분리되어 독립적으로 유지**된다(인스턴스를 종료해도 데이터는 남음).
* **VPC 내부의 Mount Target**을 통해 접근하는 **프라이빗 서비스**이며, 기본적으로 **생성된 VPC 범위 내에서 격리**된다.
* **하이브리드 네트워킹(VPN, Direct Connect)** 을 통해 VPC 외부(온프레미스 등)에서도 접근할 수 있다.
* **Lambda에서도 EFS 접근 가능**하나, Lambda가 **VPC 네트워킹**을 사용하도록 구성되어야 한다.
* **Mount Target**은 VPC CIDR 범위의 IP를 제공한다. 고가용성을 위해 **VPC 내 사용 중인 각 AZ마다 Mount Target을 생성**하는 것이 권장된다.

## 성능 모드(Performance Modes) 2가지

* **General Purpose(기본값)**: 지연 시간에 민감한 워크로드에 적합
* **Max I/O**: 더 높은 **집계 처리량(aggregate throughput)** 확장에 유리하지만 **지연 시간이 더 큼**

## 처리량 모드(Throughput Modes) 2가지

* **Bursting(기본값)**: 사용량 기반으로 버스트(개념적으로 EBS gp2의 버스팅과 유사)
* **Provisioned**: 파일 시스템 크기와 무관하게 **필요 처리량을 고정 지정**

## 스토리지 클래스(Storage Classes) 3가지

* **Standard**: 자주 접근/수정되는 데이터
* **Infrequent Access(IA)**: 분기당 몇 회 수준의 낮은 접근 빈도 데이터에 비용 최적화
* **Archive**: 연간 몇 회 수준의 매우 낮은 접근 빈도 데이터에 비용 최적화

## Lifecycle Policy

* 라이프사이클 정책으로 **Standard ↔ IA**, **Standard ↔ Archive** 등 **클래스 간 자동 전환**을 구성할 수 있다.
