## Kubernetes 101 보완/수정 포인트

### 1) “우리가(We) used to automate …” 표현

* 의미상 “우리는 ~에 사용한다”가 자연스러워서 아래처럼:

  * **Kubernetes is used to automate the deployment, scaling, and management of containerized applications.**

### 2) Control Plane 구성요소 (누락/시험포인트)

Control plane 구성요소에서 **빠지면 자주 감점/오답 유도**되는 게 있습니다.

* **kube-apiserver**: OK (클러스터의 “프론트도어”)
* **etcd**: OK (클러스터 상태 저장소)
* **kube-scheduler**: OK
* **kube-controller-manager**: OK
* **cloud-controller-manager**: “옵션”이라고는 했지만, **클라우드 환경에서는 보통 존재**(클라우드 연동 로직).
* ✅ **(추가 권장) CoreDNS**: 쿠버네티스 내부 DNS. EKS에서도 핵심 애드온으로 취급되는 경우가 많음.
* ✅ **(추가 권장) controller의 예시 정교화**

  * Endpoint Controller는 요즘은 **EndpointSlice** 기반으로 많이 언급됨(버전/구현 따라 다름).

### 3) kubelet 설명 문장 다듬기

원문: “the agent which interacts with the control plane is.”

* 아래처럼:

  * **kubelet: the node agent that interacts with the control plane via the Kubernetes API.**

### 4) kube-proxy / Service / Ingress 관계 (시험 단골)

* **Service**: Pod 집합에 대한 “안정적인 가상 IP/이름” 제공(ClusterIP/NodePort/LoadBalancer).
* **kube-proxy**: Service 구현을 위해 iptables/ipvs 규칙 등을 구성(환경에 따라 동작 방식은 다름).
* **Ingress**: L7 라우팅 규칙 “리소스”.
* **Ingress Controller**: Ingress를 실제 LB/프록시로 구현하는 컨트롤러(예: AWS Load Balancer Controller).

> 시험에서 자주 꼬는 포인트:
> “Ingress가 트래픽을 받아준다”가 아니라 **Ingress Controller가 구현**한다는 점.

### 5) PV/PVC 표현 보강

* PV는 “볼륨 객체”이고, 실제로는 보통 **PVC(PersistentVolumeClaim)** 로 요청해서 바인딩해 사용합니다.

  * PV만 써두면 흐름이 반쪽이라 PVC 한 줄 추가가 좋습니다.

---

## EKS 101 보완/수정 포인트

### 1) “EKS Cluster = EKS Control Plane and EKS Notes”

* 오타: **Notes → Nodes**
* 그리고 개념적으로는:

  * **EKS cluster = control plane (managed by AWS) + data plane (worker nodes / Fargate)**

### 2) EKS Control Plane이 “무엇을 AWS가 관리하나”를 명확히

시험에서 묻는 포인트라 한 줄로 정리하면 좋습니다.

* AWS가 관리: **API server, etcd, control plane 구성요소의 HA/업그레이드/패치/스케일링**
* 고객이 관리: **워커 노드(EC2 기반이면), 애플리케이션/애드온, 네트워킹 설계(VPC/Subnet/Security Group 등)**

### 3) Node 타입 비교에서 빠진 “중요 차이점”

* **Managed Node Group**: EC2지만 EKS가 노드 그룹 단위로 프로비저닝/업데이트/교체 지원
* **Self-managed**: ASG/업데이트/보안패치/드레이닝 전략까지 전부 직접
* **Fargate**: 노드 관리 없음(다만 제약 있음)

  * DaemonSet 제한, privileged 컨테이너 제한 등(정확한 제약은 버전/정책 따라 달라질 수 있어 “제약이 있다” 정도로만 메모 추천)

### 4) “EKS on Outposts / Anywhere / Distro” 문장 정확도 보강

지금도 충분히 맞지만, 시험용으로는 아래처럼 분리하면 명확합니다.

* **EKS (AWS Cloud)**: AWS 리전에서 완전관리형
* **EKS on Outposts**: 온프레 Outposts 랙 위에서 EKS를 사용(운영 주체/업데이트 책임 경계가 포인트)
* **EKS Anywhere**: 온프레/베어메탈/VM 등에서 EKS 스타일로 운영(관리 도구/배포 모델)
* **EKS Distro**: EKS 기반 오픈소스 배포판(Anywhere의 기반 구성요소)

### 5) Storage 부분: “CSI Driver” 한 줄 추가 추천

EKS에서 EBS/EFS/FSx를 PV로 쓰는 건 보통 **CSI 드라이버**를 통해 이뤄집니다.

* 예: EBS CSI driver, EFS CSI driver

---

## 너 문서에 바로 추가하면 좋은 “EKS 시험용 핵심 6줄”

아래 6줄만 추가해도 SAP에서 체감이 큽니다.

* EKS control plane (API server, etcd 등)은 **AWS가 관리**하며 **멀티 AZ로 HA** 제공
* Worker nodes는 **EC2(Self/Managed Node Group)** 또는 **Fargate**로 구성
* IAM 연동은 보통 **IRSA(IAM Roles for Service Accounts)** 패턴이 핵심
* Ingress는 리소스, 실제 구현은 **Ingress Controller(AWS Load Balancer Controller)** 가 수행
* PV는 보통 **PVC로 요청**하고, AWS 스토리지는 **CSI driver**로 붙는다
* Service(특히 LoadBalancer)는 AWS에선 보통 **NLB/ALB와 매핑**되며 컨트롤러가 관여한다
