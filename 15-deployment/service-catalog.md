# Service Catalog

* 서비스 카탈로그는 IT 팀이 생성한 문서 또는 데이터베이스로, 제품들을 체계적으로 정리해 둔 모음이다.
* 조직 내 여러 팀이 서비스 과금 모델(service-charge model)을 사용할 때 활용된다.
* 주요 제품 정보에는 다음이 포함된다.

  * 제품 소유자(Product Owner)
  * 비용(Cost)
  * 요구 사항(Requirements)
  * 지원 정보(Support Information)
  * 의존성(Dependencies)
* IT 부서와 고객 측의 프로비저닝 승인 절차를 정의한다.
* 비용 관리와 대규모 서비스 제공을 효율적으로 운영하기 위해 설계되었다.

## AWS Service Catalog

* 관리자가 미리 정의한 제품을 최종 사용자가 배포할 수 있도록 제공하는 포털이다.
* 최종 사용자 권한을 제어할 수 있다.
* 관리자는 CloudFormation을 사용해 제품을 정의하고, 해당 제품을 배포하는 데 필요한 권한도 함께 구성할 수 있다.
* 관리자는 제품들을 포트폴리오(Portfolio)로 구성하고, 이를 최종 사용자에게 노출할 수 있다.
* Service Catalog 아키텍처:
  ![Service Catalog architecture](images/AWSServiceCatalog.png)

**AWS Service Catalog는 관리자가 표준화된 AWS 리소스 제품을 정의해 두고, 사용자는 허용된 범위 내에서 셀프서비스 방식으로 배포할 수 있게 하는 서비스**다.
