# AWS Compute Optimizer (SAP-C02 관점 요약 번역)

* **AWS Compute Optimizer**는 AWS 리소스의 **구성(Configuration)**과 **사용률(Utilization) 지표**를 분석해서 **라이트사이징(rightsizing) 권장사항**을 제공하는 서비스입니다.
* 목적은 **비용 절감**과 **워크로드 성능/효율 개선**을 동시에 노리는 **최적화 추천(Optimization Recommendations)** 생성입니다.

## 지원 리소스(및 전제 조건)

Compute Optimizer가 추천을 제공할 수 있는 대표 대상은 다음과 같습니다.

* **EC2 인스턴스**
* **Auto Scaling Groups(ASG)**
* **EBS 볼륨**
* **Lambda 함수**
* **ECS / ECS on Fargate**
* **상용 소프트웨어 라이선스(Commercial Software Licenses)**
* **RDS DB 인스턴스 및 스토리지**

## 동작 방식 핵심

* **옵트인(Opt-in) 서비스**: 기본 활성화가 아니라, 사용자가 활성화해야 분석이 시작됩니다.
* 옵트인 후 **CloudWatch에서 수집된 지표**를 기반으로 리소스의 스펙과 사용률을 분석합니다.
* 일반적으로 **최근 14일치 지표**를 기반으로 추천을 생성합니다.

## 추천 품질(정확도) 향상 옵션

* **Recommendation preferences(추천 기본설정)**를 활성화해 추천을 더 “공격적/보수적”으로 조정하거나,
* **Enhanced infrastructure metrics(유료 고급 인프라 지표)** 같은 추가 지표를 켜서 **추천의 정밀도**를 높일 수 있습니다.

시험 관점에서 기억할 포인트는 **“옵트인 + CloudWatch 기반 + 라이트사이징 추천(비용/성능 최적화)”**입니다.
