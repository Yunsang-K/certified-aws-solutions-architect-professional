# Amazon Macie 

* **데이터 보안 및 개인정보(프라이버시) 보호 서비스**입니다.
* Macie는 **S3 버킷에 저장된 데이터를 발견(Discover)·모니터링(Monitor)·보호(Protect)** 하기 위한 서비스입니다.
* 활성화 후 대상 버킷을 지정하면, Macie가 데이터를 자동으로 분석해 **PII, PHI, 금융 정보** 등으로 분류합니다.
* Macie는 **데이터 식별자(Data Identifier)** 를 사용합니다. 식별자는 2종류가 있습니다.

  * **관리형 데이터 식별자(Managed Data Identifier)**: AWS 제공 내장 식별자. **머신러닝, 패턴 매칭** 등을 사용해 민감정보를 탐지하며, 여러 국가의 민감정보 유형을 폭넓게 탐지하도록 설계되어 있습니다.
  * **사용자 정의 데이터 식별자(Custom Data Identifier)**: 고객이 직접 생성하며, 계정에 귀속되는 **정규식(Regex) 기반** 식별자입니다.
* **Discovery Job(데이터 발견 작업)**: 데이터 식별자를 사용해 S3 데이터를 스캔하고 민감 콘텐츠를 탐지합니다. 탐지 결과는 **Finding(파인딩)** 으로 생성되며, 다른 AWS 서비스와 연동할 수 있습니다(예: **Security Hub**로 집계 → **EventBridge**로 전달 → 자동 조치(Remediation) 구현).
* Macie는 **멀티 계정 아키텍처**를 지원합니다.

  * 1개 계정이 **관리자(Administrator) 계정**이 되어, 여러 **멤버(Member) 계정**의 Macie를 중앙에서 관리하며 민감 데이터를 탐지합니다.
  * 이 멀티계정 구성은 **AWS Organizations** 기반 또는 Macie에서 계정을 **직접 초대(Invite)** 하는 방식으로 구현할 수 있습니다.

---

## Macie 식별자(Identifiers)

* **데이터 발견 작업(Data Discovery Jobs)** 은 객체(Object)에 민감 데이터가 포함되어 있는지 분석하며, 이때 **데이터 식별자**를 사용합니다.
* **관리형 데이터 식별자(Managed Data Identifiers)**

  * AWS가 생성·관리합니다.
  * 자격증명, 금융, 건강, 개인 식별 정보(주소, 여권 등) 등 **다양한 민감 데이터 유형**을 탐지하는 데 사용됩니다.
* **사용자 정의 데이터 식별자(Custom Data Identifiers)**

  * 계정 사용자/소유자가 직접 생성할 수 있습니다.
  * **정규식 패턴**으로 매칭합니다.
  * **선택적 키워드(Optional Keywords)** 를 추가할 수 있습니다(정규식 매치 근처에 특정 키워드가 있어야 탐지되도록).
  * **최대 매치 거리(Maximum Match Distance)**: 키워드가 정규식 매치와 **얼마나 가까워야** 하는지 설정합니다.
  * **무시 단어(Ignore Words)** 를 포함해 오탐을 줄일 수 있습니다.

---

## Macie 파인딩(Findings)

* Macie는 2가지 유형의 파인딩을 생성합니다.

  * **정책 파인딩(Policy Findings)**: Macie 활성화 이후, 버킷의 정책/설정이 변경되어 **보안이 약화**되는 경우 생성
  * **민감 데이터 파인딩(Sensitive Data Findings)**: 식별자를 통해 **민감 데이터가 탐지**되면 생성

### 정책 파인딩 예시

* `Policy:IAMUser/S3BlockPublicAccessDisabled`
  버킷 단위의 **Block Public Access 설정이 모두 비활성화**됨
* `Policy:IAMUser/S3BucketEncryptionDisabled`
  버킷의 **기본 암호화 설정이 초기화**되어, 신규 객체가 **S3 관리형 키(SSE-S3)** 로 자동 암호화되는 기본 동작으로 돌아감
* `Policy:IAMUser/S3BucketPublic`
  ACL 또는 버킷 정책 변경으로 **익명 사용자** 또는 **모든 인증된 IAM 사용자**에게 접근이 허용됨
* `Policy:IAMUser/S3BucketSharedExternally`
  ACL 또는 버킷 정책 변경으로, 조직(Organizations) 외부의 **외부 AWS 계정과 공유**되도록 변경됨

### 민감 데이터 파인딩 예시

* `SensitiveData:S3Object/Credentials`
  AWS 시크릿 액세스 키, 프라이빗 키 등 **자격 증명 데이터**가 포함됨
* `SensitiveData:S3Object/CustomIdentifier`
  하나 이상의 **사용자 정의 식별자** 기준에 매칭되는 텍스트가 포함됨
* `SensitiveData:S3Object/Financial`
  계좌번호, 신용카드 번호 등 **금융 정보**가 포함됨
* `SensitiveData:S3Object/Multiple`
  둘 이상의 **민감 데이터 범주**가 포함됨
* `SensitiveData:S3Object/Personal`
  여권번호/운전면허번호 같은 **PII**, 건강보험/의료 식별번호 같은 **PHI**, 또는 PII+PHI 조합이 포함됨
