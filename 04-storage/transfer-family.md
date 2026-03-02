# AWS Transfer Family

* **완전관리형(Managed) 파일 전송 서비스**로, 파일을 **Amazon S3 또는 Amazon EFS**로 업로드/다운로드할 수 있게 해줍니다. ([Amazon Web Services, Inc.][1])

* S3/EFS가 “네이티브로 제공하지 않는 방식의 파일 전송 프로토콜”을 **관리형 서버(Server)** 형태로 제공해, 기존 레거시/파트너 연동 요구(FTP 계열, AS2 등)를 맞출 때 사용합니다. ([Amazon Web Services, Inc.][2])

* 지원 프로토콜

  * **FTP (File Transfer Protocol)**: 평문 전송(미암호화)
  * **FTPS (FTP over TLS)**: TLS로 암호화된 FTP
  * **SFTP (SSH File Transfer Protocol)**: SSH 기반 파일 전송
  * **AS2 (Applicability Statement 2)**: B2B에서 구조화된 데이터(EDI 등) 교환에 쓰이는 표준 프로토콜 ([Amazon Web Services, Inc.][1])

* 인증/ID 연동(Identity Provider) 옵션(대표)

  * **서비스 매니지드(Service managed)**
  * **AWS Directory Service 연동**
  * **커스텀 IDP**(Lambda/API Gateway 등으로 인증/권한부여 로직 구성)

* **Managed File Transfer Workflows(MFTW)**: 업로드/다운로드 같은 파일 이벤트를 트리거로, **서버리스 워크플로우**(검증/변환/이동/알림 등)를 붙일 수 있는 기능

---

## Transfer Family 엔드포인트(Endpoint) 타입

Transfer Family에서는 “서버(Server)”를 만들고, 그 서버가 **S3/EFS를 특정 프로토콜로 노출하는 프런트 엔드 접점** 역할을 합니다.
접근 방식은 **엔드포인트 구성(Endpoint type)** 에 따라 달라집니다.

### 1) Public (퍼블릭 엔드포인트)

* AWS 퍼블릭 영역에서 동작하며 **인터넷에서 직접 접근**
* **네트워크 구성 요소(VPC/서브넷/보안그룹 등) 설정 불필요**
* (정리) 일반적으로 **SFTP 용도로 가장 단순**하게 쓰는 옵션으로 많이 설명됩니다.

> 참고: FTP는 퍼블릭 경로로는 사용할 수 없고, **FTP는 “VPC hosted + Internal access”가 필수**입니다. ([AWS 문서][3])

### 2) VPC – Internet Access (VPC 내 + 인터넷 공개)

* **VPC 안에서 동작**하며, 인터넷에서 접근 가능(보통 **고정 IP(EIP)** 를 사용)
* **Security Group / NACL**로 접근 제어 가능
* 온프레미스에서 DX/VPN으로 VPC에 붙어있다면 내부에서도 동일하게 접근 가능

### 3) VPC – Internal (VPC 내 + 내부 전용)

* **VPC 내부 전용**(인터넷 비공개)
* **DX/VPN/피어링/TGW 등으로 VPC에 연결된 네트워크**에서 접근
* **Security Group / NACL** 적용 가능

### 프로토콜/엔드포인트 제약(핵심만)

* **FTP**: 반드시 **VPC hosted 엔드포인트의 Internal access** 필요 ([AWS 문서][3])
* **AS2**: 일반적으로 **VPC 기반(Internet/Internal)에서 운영**하는 패턴이 표준적으로 안내됩니다(파트너/B2B 연결과 네트워크 제어 요구 때문). ([Amazon Web Services, Inc.][1])

---

## 기타 특징/비용 포인트

* **멀티 AZ 기반으로 확장성과 내구성**을 제공(관리형 서비스 특성상 고가용성 설계 부담을 줄임) ([Amazon Web Services, Inc.][1])
* 과금은 보통 **프로비저닝된 서버(또는 엔드포인트) 시간 단위 + 데이터 전송/처리량**이 핵심 축입니다(정확한 항목은 요금표 기준으로 확인 필요).

---

## 대표 사용 시나리오

* 파트너/레거시 시스템이 **SFTP/FTPS/FTP/AS2** 같은 전통 프로토콜을 요구하는데, 저장은 **S3/EFS**로 가져가고 싶은 경우 ([Amazon Web Services, Inc.][2])
* 사내 표준 전송 플랫폼(MFT)로 통합해서 **인증/감사/운영을 일원화**하고 싶은 경우
* 업로드 직후 **검증→변환→다른 버킷/접두어로 이동→알림** 같은 처리를 **MFT Workflows**로 자동화하고 싶은 경우

[1]: https://aws.amazon.com/aws-transfer-family/faqs/?utm_source=chatgpt.com "AWS Transfer Family FAQs | Amazon Web Services"
[2]: https://www.amazonaws.cn/en/amazon-transfer-family/faqs/?utm_source=chatgpt.com "Amazon Transfer Family FAQs"
[3]: https://docs.aws.amazon.com/ja_jp/transfer/latest/userguide/create-server-ftp.html?utm_source=chatgpt.com "FTP 対応サーバーの作成 - AWS Transfer Family"
