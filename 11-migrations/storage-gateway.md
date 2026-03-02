# Storage Gateway

* 일반적으로 온프레미스(사내)에서 VM(가상 어플라이언스) 또는 하드웨어 어플라이언스로 실행됨
* 온프레미스 스토리지와 AWS 사이를 연결하는 브리지 역할
* iSCSI, NFS, SMB 형태로 스토리지를 제공(노출)
* AWS 측에서는 EBS, S3, Glacier와 연동
* 주요 사용처: 마이그레이션, 스토리지 확장, 스토리지 티어링, DR, 백업 시스템 대체

---

## Volume Gateway

* 2가지 운영 방식 제공

### 1) Volume Stored 모드(Stored Volumes)

* 가상 어플라이언스가 온프레미스 서버에 iSCSI 볼륨을 제공(NAS/SAN 장비처럼 동작)
* 서버는 해당 볼륨 위에 파일 시스템을 생성하고 일반 디스크처럼 사용
* 이 볼륨은 **온프레미스 용량을 소비**
* Storage Gateway의 로컬 스토리지가 **주 스토리지(Primary)** 이며 데이터는 **로컬에 완전 저장**
* Upload buffer: 로컬에 쓰인 데이터는 업로드 버퍼에도 복사되고, Storage Gateway 엔드포인트를 통해 **비동기적으로 클라우드로 업로드**
* 업로드된 데이터는 S3에 **EBS 스냅샷 형태**로 저장되며, 이후 **EBS 볼륨으로 변환 가능**
* **전체 디스크 백업(Full disk backup)** 용도로 적합하며 RTO/RPO가 좋음
* **데이터센터 용량 확장(Extension)은 불가**: 데이터 전체 사본이 로컬에 존재해야 함
* (그림) Volume Stored Mode architecture

### 2) Volume Cached 모드(Cached Volumes)

* 기본 구조는 Stored 모드와 유사
* 데이터의 주 저장 위치가 온프레미스가 아니라 **AWS S3**
* 로컬에는 **자주 접근하는 데이터만 캐시**로 보관(Primary 데이터는 S3에 위치)
* 데이터는 S3의 AWS 관리 영역에 저장되어 **S3 콘솔에서 직접 보이지 않을 수 있음**(Storage Gateway 콘솔에서 확인)
* 데이터는 **Raw block 형태**로 저장
* 저장된 데이터로 **EBS 볼륨 생성 가능**
* Volume Cached 모드는 **데이터센터 확장(Data center extension)** 아키텍처를 가능하게 함
* (그림) Volume Cached Mode architecture

---

## Tape - VTL 모드

* VTL(Virtual Tape Library, 가상 테이프 라이브러리)
* 테이프 백업 예: LTO-9(Linear Tape Open) 미디어는 테이프 1개당 Raw 24TB 저장 가능
* Tape Loader(로봇): 로봇 팔이 테이프 삽입/제거/교체
* Library 구성: 1개 이상 드라이브 + 1개 이상 로더 + 슬롯
* (그림) Traditional tape backup architecture
* (그림) Storage Gateway Tape(VTL) Mode architecture
* 가상 테이프는 **100 GiB ~ 5 TiB**
* Storage Gateway 1대가 최대 **1PB** 데이터(가상 테이프 **1500개**)까지 처리 가능
* 사용하지 않는 가상 테이프는 백업 소프트웨어에서 Export 처리(라이브러리에 없는 상태로 표시) → 물리 테이프의 “eject 후 외부 보관”과 유사
* Export된 가상 테이프는 Glacier 기반의 **Virtual Shelf**로 아카이브됨
* VTL 모드의 Storage Gateway는 **iSCSI 테이프 라이브러리/테이프 체인저/드라이브**처럼 동작(레거시 테이프 백업 시스템을 에뮬레이션)
* 사용 사례:

  * 온프레미스 데이터 저장을 AWS로 확장
  * 과거(히스토리) 테이프 백업 세트 마이그레이션

---

## File 모드(File Gateway)

* File 모드에서 Storage Gateway는 **파일 단위로 관리**
* File Gateway는 온프레미스 파일 스토리지와 **S3를 연결**
* File Gateway에서 1개 이상의 마운트 포인트(Share)를 생성하고 **NFS 또는 SMB**로 제공
* File Gateway는 **S3 버킷에 직접 매핑**되며, 버킷/오브젝트는 **AWS 콘솔에서 가시성 확보 가능**
* Read/Write 캐시를 사용해 LAN 수준에 가까운 성능을 목표로 함
* (그림) File Gateway architecture
* Windows 환경에서는 **AD 인증**으로 File Gateway 접근 가능
* 여러 기여자(여러 온프레미스 Share) 환경에 사용 가능
* File Gateway의 파일 경로는 S3의 오브젝트 이름에 직접 매핑됨
* `NotifyWhenUploaded`: 오브젝트 변경 시 다른 게이트웨이에 알리는 API
* File Gateway는 **오브젝트 잠금(Object Lock) 미지원**

  * 한 게이트웨이가 다른 게이트웨이의 파일을 덮어쓸 수 있음
  * 따라서 다른 Share는 Read-only로 두거나 파일 접근을 엄격히 통제해야 함
* 백엔드 S3 버킷은 **CRR(Cross-Region Replication)** 적용 가능
* 라이프사이클 정책으로 스토리지 클래스 간 자동 이동 가능
