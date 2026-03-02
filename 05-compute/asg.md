## ASG(Auto Scaling Group) 핵심 정리 

### 1) ASG 개요

* **Auto Scaling Group(ASG)** 은 **EC2 인스턴스를 자동으로 확장/축소(Scale out/in)** 하기 위한 기능이다.
* **Self-healing(자가 치유)** 아키텍처를 구현할 수 있다. (비정상 인스턴스를 감지하고 교체)
* ASG는 **Launch Template(LT)** 또는 **Launch Configuration(LC)** 에 정의된 설정을 사용한다.
* ASG는 기본적으로 **단일 Launch Template/Configuration 버전**(혹은 지정된 버전)을 기준으로 인스턴스를 생성한다.

### 2) 용량(Capacity) 3요소

ASG에는 항상 다음 3가지 값이 중요하다.

* **Minimum size (Min)**: 최소 유지 인스턴스 수
* **Desired capacity (Desired)**: 목표로 유지하려는 인스턴스 수
* **Maximum size (Max)**: 최대 확장 한계

관계:

* 일반적으로 **Min ≤ Desired ≤ Max** (Desired는 Min보다 작을 수 없고 Max보다 클 수 없다)

### 3) ASG의 “기본 역할”

* ASG의 가장 기본적인 역할은 **실행 중 인스턴스 수를 Desired 값으로 유지**하는 것이다.

  * 인스턴스가 종료/비정상 → **자동으로 대체(Replace)** 하여 Desired를 맞춘다.

### 4) “어디에/무엇을”의 분리 (시험에서 자주 나오는 관점)

* **ASG = 언제/어디에** 인스턴스를 둘지 정의

  * VPC에 연결되고, **어떤 Subnet(AZ 포함)에 배치할지** 등을 결정
* **Launch Template = 무엇을** 띄울지 정의

  * AMI, 인스턴스 타입, 보안그룹, IAM role, user data 등

---

## 5) Scaling Policy (스케일링 정책)

* **Scaling Policy** 는 메트릭(CPU, 연결 수 등)에 따라 **Desired capacity를 변경**하는 규칙이다.
* ASG는 스케일링 정책이 없어도 동작한다.

  * 정책이 없으면 Min/Desired/Max가 **고정(정적)** 으로 유지되고 자동 증감은 없다.

### 스케일링 유형

* **Manual Scaling**: Desired를 사람이 직접 조정
* **Scheduled Scaling**: 특정 시간대에 맞춰 조정(예: 평일 9~18시)
* **Dynamic Scaling**: 메트릭 기반으로 자동 조정
* **Predictive Scaling**: 과거 트래픽 패턴을 학습해 선제적으로 조정

---

## 6) Dynamic Scaling 세부 유형

Dynamic Scaling에는 대표적으로 3가지 방식이 있다.

### (1) Simple Scaling

* 단순 임계치 기반
* 예: “CPU > 50%이면 +1”, “CPU < 50%이면 -1”
* 단점: 변화 폭이 크거나 급격할 때 **유연성이 낮음**

### (2) Step Scaling

* 임계치 구간(스텝)에 따라 증감 폭을 다르게
* 예:

  * CPU 50~60%: 변화 없음
  * 60~70%: +1
  * 70~80%: +2
  * 80% 이상: +3
* 일반적으로 Simple보다 더 실무적이고 시험에서도 **권장되는 흐름**
* (노트에도 있음) AWS는 보통 **Simple보다 Step을 권장**하는 맥락이 많다.

### (3) Target Tracking

* “특정 메트릭을 목표값으로 유지”하도록 자동 조정
* 예: “ASG 평균 CPU를 40%로 유지”
* 지원 메트릭 예:

  * `CPUUtilization`
  * `AvgNetworkIn`, `AvgNetworkOut`
  * `ALBRequestCountPerTarget`
* 모든 메트릭이 타깃 트래킹에 지원되는 것은 아니다.

추가 예시(자주 나오는 패턴):

* **SQS 기반 스케일링**: `ApproximateNumberOfMessagesVisible` 로 큐 적체량 기준 확장

---

## 7) Cooldown Period (쿨다운)

* 스케일링 액션 이후 **다음 스케일링을 바로 또 하지 않도록 대기하는 시간(초 단위)**.
* 너무 잦은 스케일 인/아웃(플래핑)을 줄이기 위해 사용한다.

---

## 8) Health Check & Load Balancer 연동

* ASG는 인스턴스 헬스체크를 모니터링하고, 실패 시 **자동 교체**한다.
* 기본 헬스체크: **EC2 헬스체크**
* 추가로 **ELB/ALB 헬스체크**를 연동할 수 있다.

  * ASG가 인스턴스를 **Target Group에 자동 등록/해제** 가능
  * ELB 헬스체크는 L7(애플리케이션 레벨)로 더 “서비스 상태”를 잘 반영할 수 있음

### Health Check 종류 3가지

* **EC2 (기본)**
* **ELB**
* **Custom** (외부 시스템이 Healthy/Unhealthy를 마킹)

#### EC2 헬스체크에서 Unhealthy로 보는 예

* Stopping / Stopped / Terminated / Shutting-down / Impaired(상태 체크 실패)

#### ELB 헬스체크

* 인스턴스가 실행 중이면서, **ELB 헬스체크도 통과**해야 Healthy

### Health Check Grace Period

* 신규 인스턴스 부팅/부트스트랩 시간을 주기 위해, 일정 시간 헬스체크 판정을 유예
* 기본값: **300초**
* 앱 기동이 느리면 늘려야 불필요한 교체를 줄일 수 있다.

---

## 9) Scaling Processes (프로세스 Suspend/Resume)

ASG에는 동작 단위를 제어하는 “프로세스”가 있고, 필요 시 중지할 수 있다.

* **Launch / Terminate**

  * Launch 중지: 스케일 아웃 불가
  * Terminate 중지: 스케일 인 불가
* **AddToLoadBalancer**: 로드밸런서 등록
* **AlarmNotification**: CloudWatch 알람 반응 제어
* **AZRebalance**: AZ 간 인스턴스를 균등 배치
* **HealthCheck**: 헬스체크 수행 여부
* **ReplaceUnhealthy**: 비정상 교체 여부
* **ScheduledActions**: 스케줄 액션 수행 여부
* **Standby**: 특정 인스턴스를 Standby로 두어 용량 계산/트래픽 처리에서 제외하는 등 운영 제어

---

## 10) Lifecycle Hooks (라이프사이클 훅)

* 스케일 인/아웃 과정 중 **중간에 멈춰서 사용자 정의 작업을 실행**할 수 있게 해준다.

  * 예: 인스턴스 기동 후 에이전트 설치, 설정 완료될 때까지 대기
  * 예: 종료 전 로그 업로드, 연결 드레이닝, 상태 저장
* **timeout**을 지정할 수 있고, 시간이 지나면 계속 진행하거나(또는 포기) 동작이 결정된다.

  * 기본 timeout이 길게 잡히는 경우가 많음(노트: 36000s)
* 훅 완료 후 `CompleteLifecycleAction` 호출로 프로세스를 재개한다.
* EventBridge 또는 SNS 알림과 연동 가능

---

## 11) ASG 운영 고려사항

* ASG 자체는 **무료**이며, 비용은 **실제로 실행된 EC2 인스턴스에 대해서만** 발생한다.
* 과도한 스케일링을 막기 위해 **Cooldown/적절한 정책**을 사용한다.
* 더 세밀한 조정을 위해 **작은 인스턴스 여러 대**가 유리한 경우가 있다(그라뉼러리티).
* ASG는 ALB와 자연스럽게 통합된다.
