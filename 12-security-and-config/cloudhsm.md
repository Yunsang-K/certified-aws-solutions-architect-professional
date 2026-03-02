## CloudHSM 

### 1) KMS vs CloudHSM 핵심 차이 (시험 포인트)

* **KMS**

  * “공유(멀티테넌트)형” 관리형 키 관리 서비스.
  * AWS가 **하드웨어/소프트웨어 운영을 포함해 서비스 전반을 관리**하고, 다른 AWS 서비스와 **통합이 매우 강함**.
  * 내부적으로 HSM 기반이지만, 사용자는 HSM을 직접 소유/운영하지 않음.
* **CloudHSM**

  * **진짜 싱글 테넌트(전용) HSM**을 AWS 데이터센터에 “호스팅”해주는 형태. ✅ **시험에서 자주 나오는 포인트**
  * AWS는 HSM 하드웨어는 프로비저닝하지만 **키/유저/정책/운영은 고객 책임**이며, AWS가 고객 키에 접근하지 않는 모델.
  * **분실/운영 실수로 접근을 잃으면 복구가 쉽지 않다**(운영 책임이 크다).

### 2) 규정/컴플라이언스

* **CloudHSM: FIPS 140-2 Level 3 준수** ✅(시험 포인트)
* **KMS: 전반적으로 FIPS 140-2 Level 2 수준으로 분류되는 경우가 많음**(서비스/모드에 따라 표현이 달라질 수 있으나 시험에서는 “CloudHSM이 L3”를 강하게 기억하면 됨)

### 3) 접근 방식(API)과 통합성

* CloudHSM은 **산업 표준 API**로 접근:

  * **PKCS#11**
  * **JCE (Java Cryptography Extensions)**
  * **Microsoft CNG (CryptoNG)**
    ✅ “AWS 서비스와의 네이티브 통합이 약하고(의도적으로), 표준 API로 붙는다”가 포인트
* 반대로 KMS는 S3, EBS, RDS, Lambda, Secrets Manager 등 **거의 모든 AWS 서비스와 기본 통합**이 강함.

### 4) KMS + CloudHSM 연결: Custom Key Store

* **KMS는 CloudHSM을 “Custom Key Store”로 사용 가능**

  * 즉, KMS API/통합성은 유지하면서 **키 재료(키가 존재하는 HSM)를 CloudHSM에 두는 구성**이 가능.

---

## 아키텍처 포인트 (시험형 문장으로 기억)

* CloudHSM은 **AWS가 관리하는 VPC 영역에 HSM이 존재**하고, 고객 VPC로는 **ENI(Elastic Network Interface)** 형태로 “주입/연결”된다.
* **고가용성(HA)**:

  * 단일 HSM이 아니라 **여러 HSM을 여러 AZ에 두고 클러스터 구성**해야 함.
* **클라이언트 필요**:

  * EC2(또는 앱 서버)에 **CloudHSM client/라이브러리**를 설치해서 HSM에 접근.

---

## 사용 사례 / 제한 사항 (기출 느낌)

### 제한

* **S3 SSE(서버사이드 암호화)에 CloudHSM을 직접 연결해 쓰는 건 불가**

  * 네이티브 통합이 없기 때문(예외적으로 KMS Custom Key Store 경유는 가능).

### 가능한 사용

* **클라이언트 사이드 암호화**(S3 업로드 전에 앱에서 CloudHSM으로 키 연산/암호화)
* **TLS/SSL 오프로딩**(웹 서버의 개인키 보호/연산)
* **RDS Oracle TDE**에서 CloudHSM 사용 가능
* **사설 CA(인증기관) 프라이빗 키 보호**

---

## 시험에서 바로 고르는 키워드 트리거

* “**전용 HSM / 싱글 테넌트**” → CloudHSM
* “**FIPS 140-2 Level 3** 요구” → CloudHSM
* “**PKCS#11 / JCE / CNG로 붙어야 함**” → CloudHSM
* “**S3/EBS/RDS 등 AWS 서비스와 즉시 통합되는 키 관리**” → KMS
* “**KMS를 쓰되 키를 우리 HSM에 둬야 함**” → KMS **Custom Key Store(CloudHSM)**
