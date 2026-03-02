## 1) 암호화 접근(Encryption Approaches)

### 저장 시 암호화(Encryption at Rest)

* 목적: 디스크/스토리지 도난, 물리적 접근, 스냅샷 유출 같은 **물리·정적 위협**에 대비
* 데이터는 저장 매체에 **암호문 형태**로 저장되므로, 저장장치에 접근하더라도 평문을 바로 읽기 어렵다
* 대표 예: EBS/S3/RDS/DynamoDB의 SSE, EBS 암호화, RDS 암호화 등

### 전송 중 암호화(Encryption in Transit)

* 목적: 두 지점 간 전송 중 **도청/변조(MITM)** 방지
* 일반적으로 **TLS(HTTPS)** 로 구현
* 대표 예: ALB/NLB TLS, CloudFront HTTPS, API Gateway TLS, VPC 내부 mTLS 등

> 메모의 “at rest는 보통 한 party” 같은 표현은 시험 관점에선 굳이 구분하지 않는 편이 안전합니다. 핵심은 **위협 모델(물리 vs 전송 경로)** 입니다.

---

## 2) 암호화 기본 개념(Encryption Concepts)

* **평문(Plaintext)**: 암호화되지 않은 원본 데이터
* **알고리즘(Algorithm)**: 평문과 키를 받아 암호문을 만드는 절차(예: AES 등)
* **키(Key)**: 암호화/복호화에 쓰는 비밀 값

  * “키는 비밀번호다”는 표현은 *비유로는 가능*하지만, 실제로는 **비밀번호와 키는 다를 수 있음**(키는 랜덤 바이트, 비밀번호는 사람이 기억 가능한 문자열인 경우가 많음).
* **암호문(Ciphertext)**: 암호화 결과 데이터

---

## 3) 대칭키 암호화(Symmetric Encryption)

* **동일한 키**로 암호화와 복호화를 수행
* 대표 알고리즘: **AES-256**
* 장점: 빠르고 대용량에 적합 → **저장 시 암호화/데이터 암호화(DEK)**에 주로 사용
* 보완점: 키 전달/보관이 어려움 → 전송 구간 자체는 보통 TLS로 해결

> 메모의 “대칭키는 전송 중에 비추천”은 *대체로 맞는 방향*인데, 정확히 말하면 “대칭키 자체도 TLS 내부에서 쓰지만, **키 교환을 안전하게 하기 위해 비대칭/키교환**이 필요”가 핵심입니다.

---

## 4) 비대칭키 암호화(Asymmetric Encryption)

* **공개키/개인키** 한 쌍
* 공개키로 암호화한 데이터는 **개인키로만 복호화** 가능(기본 개념)
* 키 교환, 서명, 인증에 유리
* 대표 알고리즘: RSA(전통적), (현대 TLS는 ECDHE/ECDSA 등 타원곡선 기반이 많음)

---

## 5) 서명(Signing)

* 목적: **무결성 + 송신자 인증 + 부인방지(상황에 따라)**
* 개인키로 “서명” 생성 → 공개키로 검증
* “메시지 자체를 암호화”가 아니라, 보통은 **해시(요약값)에 서명**하는 형태로 이해하면 안전

---

## 6) 스테가노그래피(Steganography)

* 암호화와 별개 개념: 데이터를 다른 데이터(이미지/오디오 등)에 **숨기는 기법**
* 시험에서는 보안 개념 구분 문제로 가끔 등장

---

## 7) AWS KMS (Key Management Service)

### KMS 개요

* **리전(Regional) 단위 서비스**
* 암호화 키(정확히는 *KMS Key*) 생성/보관/관리 및 암호연산 제공
* **대칭키/비대칭키** 모두 지원(사용 사례가 다름)
* **키 재료(key material)는 KMS 경계 밖으로 내보내지지 않는다**(일반적인 KMS key 기준)

  * 단, “**퍼블릭 키**”는 비대칭키의 특성상 배포/조회가 가능(공개키니까).
* **FIPS 140-2 Level 2** 준수(시험 포인트)

> “Keys never leave KMS”는 **대칭키 키 재료는 밖으로 export 불가**라는 의미로 잡으면 정확합니다. 비대칭은 공개키는 밖으로 나갈 수 있고, 개인키는 KMS/HSM 경계 내에 유지되는 구조로 보는 게 안전합니다.

---

## 8) KMS 키(구 CMK) 설명

* KMS가 관리하는 핵심 객체는 **KMS Key**
* “논리적 리소스”로서:

  * Key ID/ARN, 상태(enabled/disabled/pending deletion), 설명, 키 정책(Key Policy), 생성일 등 메타데이터 포함
* 각 KMS Key는 **물리적 키 재료(key material)** 로 백업됨

  * AWS KMS가 생성하거나(import), 일부 경우 외부 키 스토어/커스텀 키 스토어 등 옵션이 존재
* KMS `Encrypt/Decrypt`로 직접 처리 가능한 페이로드는 **최대 4KB**
  → 대용량은 **Envelope Encryption(봉투 암호화)** 패턴을 쓴다

---

## 9) DEK(Data Encryption Key)와 봉투 암호화(Envelope Encryption)

### DEK 생성

* `GenerateDataKey`로 **KMS Key로부터 DEK를 파생(생성)**
* KMS는 응답으로 보통 2가지를 줌:

  1. **Plaintext DEK** (평문 데이터키)
  2. **Ciphertext DEK** (KMS Key로 암호화된 데이터키 = “Encrypted DEK”)

### 저장/복호화 흐름

* 애플리케이션은 **Plaintext DEK로 로컬에서 데이터(대용량)를 암호화**
* 암호화된 데이터와 **Ciphertext DEK를 함께 저장**(side-by-side)
* 복호화 시:

  * Ciphertext DEK를 `Decrypt`로 KMS에 보내 Plaintext DEK를 얻고
  * 그 Plaintext DEK로 데이터를 로컬 복호화

### 중요한 시험 포인트

* KMS는 **DEK를 저장하지 않는다**(요청 시 생성/복호화만 수행)
* 애플리케이션은 Plaintext DEK를 **가능한 빨리 폐기(메모리 상 최소화)**

---

## 10) KMS 키 추가 개념(시험에 자주 나오는 것)

* **리전 격리**: 기본적으로 KMS Key는 리전 단위. (옵션으로 **Multi-Region Key** 지원)
* 키 소유/관리 유형:

  * **AWS Owned keys**: AWS가 소유/관리(사용자 보통 직접 제어 불가)
  * **AWS Managed keys**: AWS가 생성/관리하지만 사용자 계정에 귀속(일부 제어 제한)
  * **Customer Managed keys**: 사용자가 생성/정책/회전/삭제 등 폭넓게 제어
* **키 회전(Rotation)**:

  * Customer Managed Key는 회전 옵션을 켤 수 있음(정책/감사 요구사항과 연결됨)
  * 회전되더라도 **이전 키 재료로 암호화된 데이터는 복호화 가능**
* **Alias**:

  * 사람이 읽기 쉬운 별칭
  * **리전 단위**로 관리됨
* **Key Policy(리소스 정책)**:

  * KMS에서 가장 중요한 제어 지점
  * 특히 “키 정책에 계정 소유자 루트/관리 권한이 명시적으로 허용되어야 한다” 같은 형태의 출제가 잦음
  * 교차 계정(Cross-account) 접근도 보통 **Key Policy** 로 풀어냄
