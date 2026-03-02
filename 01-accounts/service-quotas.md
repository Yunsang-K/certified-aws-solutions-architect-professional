# AWS Service Quotas (서비스 할당량)

* **AWS 계정에서 특정 리소스(“어떤 것”)를 얼마나 사용할 수 있는지**를 정의하는 제한 값
* 예시

  * 리전(Region)별 **특정 시점에 생성 가능한 EC2 인스턴스 수**
  * AWS 계정 전체에서 생성 가능한 **IAM 사용자 수**
* 대부분의 서비스는 **리전별 기본 할당량(default per-Region quota)**이 존재함
* **글로벌 서비스(Global service)**의 경우 리전이 아니라 **계정 단위(per-account)** 할당량으로 적용될 수 있음
* 많은 서비스 할당량은 필요에 따라 **증가 요청**이 가능함
* 일부 할당량은 **변경 불가**(예: IAM 사용자 수는 계정당 **최대 5,000명**)
* 요청하는 증가 폭이 클수록 **승인(심사) 시간이 더 오래 걸릴 수 있음**
* 서비스 엔드포인트 및 기본 할당량 정보:

  * [https://docs.aws.amazon.com/general/latest/gr/aws-service-information.html](https://docs.aws.amazon.com/general/latest/gr/aws-service-information.html)

## Service Quotas 서비스(콘솔)

* AWS 콘솔에서 **Service Quotas**로 이동하면,

  * 모니터링하고 싶은 할당량을 모아 **대시보드**를 구성할 수 있음
  * 일부 서비스는 여기서 **할당량 증가 요청**을 직접 제출할 수 있음
  * **Quota request template** 기능으로, AWS Organizations 내 **신규 계정 생성 시 적용할 목표 할당량 값**을 미리 정의할 수 있음
  * 특정 할당량 사용률에 대해 **CloudWatch Alarm**을 생성할 수 있음

## 할당량 증가의 레거시 방식

* **AWS Support 티켓**을 생성하고 “Service quota increase” 유형으로 요청 제출

## CLI로도 증가 요청 가능

* AWS CLI로 할당량 증가 요청을 수행할 수 있음

  * 참고(API/CLI 문서): [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/service-quotas/request-service-quota-increase.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/service-quotas/request-service-quota-increase.html)
