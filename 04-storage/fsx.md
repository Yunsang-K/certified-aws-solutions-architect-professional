# FSx

## Amazon FSx for Windows File Server

* **FSx for Windows**는 완전관리형 **네이티브 Windows 파일 서버/파일 공유(SMB)** 서비스
* Windows 환경과의 통합을 목적으로 설계됨
* **AWS Managed Microsoft AD(AWS Directory Service)** 또는 **Self-Managed AD**와 통합 가능
* **탄력적/고가용성(HA)** 제공

  * VPC 내 **Single-AZ 또는 Multi-AZ** 배포 가능
  * Single-AZ여도 백엔드 복제(Replication)로 **하드웨어 장애 내성**을 확보
* 백업: **클라이언트 측 기능 + AWS 측 기능** 모두 활용 가능

  * AWS 측에서 **자동 백업** 및 **온디맨드(수동) 백업** 지원
* 접근 경로: **VPC Peering, VPN, Direct Connect(DX)** 통해 접근 가능
* 기능: **중복 제거(Deduplication)**, **DFS(Distributed File System)로 확장**, **KMS 기반 저장 시 암호화**, **전송 중 암호화 강제** 지원
* **VSS(Volume Shadow Copy Service)**로 파일 “이전 버전(Previous Versions)” 조회/복구 가능
* 성능: **처리량 8 MB/s ~ 2 GB/s**, **수십만 IOPS**, **1ms 미만 지연** 수준
* 핵심 특징 요약

  * **VSS**: 사용자 주도 복구(이전 버전 보기)
  * **SMB**로 접근 가능한 네이티브 파일 시스템
  * Windows 권한 모델(ACL) 사용
  * **DFS**: 스케일아웃 파일 공유 구조(여러 파일 공유를 단일 엔터프라이즈 구조로 묶기)
  * 완전관리형: 파일 서버 운영/관리 부담 감소
  * AWS Managed DS 또는 자체 디렉터리와 통합

---

## Amazon FSx for Lustre

* **고성능 워크로드용 파일 시스템**
* **Lustre 파일 시스템의 관리형 구현**

  * HPC 중심, **Linux 클라이언트(POSIX)** 대상
* 활용 분야: **머신러닝, 빅데이터, 금융 모델링** 등
* **수백 GB/s**급 처리량까지 확장 가능, **서브 밀리초 지연** 제공
* 배포 타입 2종

  * **Persistent**: **단일 AZ 내 HA**, 자가 치유(Self-healing) 제공, 장기 저장에 권장
  * **Scratch**: 단기/임시 목적의 최고 성능 지향, **복제 없음(Replication 없음)**
* 온프레미스 연계: **VPN 또는 Direct Connect(DX)** 통해 사용 가능
* **S3 리포지토리 연동**

  * 파일은 S3에 저장되고, 실제 접근 시점에 FSx for Lustre로 **지연 로딩(Lazy load)**
* S3 동기화: 변경 사항은 자동 동기화가 아님

  * `hsm_archive` 명령으로 **파일 시스템 ↔ S3 동기화**
* Lustre 구성 요소

  * **MST**: 메타데이터가 저장되는 Metadata Targets
  * **OST**: 데이터(오브젝트)가 저장되는 Object Storage Targets(각 OST는 최대 **1.17 TiB**)
* 성능(베이스라인/크레딧) 개요

  * 최소 **1.2 TiB**, 이후 **2.4 TiB** 단위로 확장
  * Scratch: 기본 **TiB당 200 MB/s**
  * Performance 옵션: **TiB당 50 / 100 / 200 MB/s**
  * 두 타입 모두 **크레딧 기반으로 TiB당 최대 1300 MB/s까지 버스트** 가능
* 배포 타입 상세

  * **Scratch**

    * 단기/임시, 순수 성능 지향
    * **HA/복제 미제공** → 하드웨어 장애 시 해당 HW에 있던 데이터 손실 가능
    * 파일 시스템이 커질수록 서버/디스크 수 증가 → 장애 확률 증가
  * **Persistent**

    * **단일 AZ 내 복제**
    * 하드웨어 장애 시 **자동 복구(Self-heal)**
  * 백업: 두 타입 모두 S3로 백업 가능

    * 수동 또는 자동(보존 기간 **0~35일**)

---

## Amazon FSx for NetApp ONTAP

* NetApp ONTAP 기반의 **완전관리형 스토리지**
* NetApp 데이터 관리 기능 제공

  * 스토리지 효율: **압축, 중복 제거, 컴팩션, 씬 프로비저닝**
  * 저비용/탄력형 **Capacity Pool 티어링**
  * 데이터 보호: **스냅샷, SnapVault, FSx 네이티브 백업**
  * DR: **SnapMirror + Amazon FSx Backups**
  * 캐싱: **FlexCache, Global File Cache**
  * 멀티프로토콜: Linux/Windows 모두 접근 가능
  * 기타: **안티바이러스 스캔** 등
* 정책 기반 티어 간 데이터 이동

  * 2개 스토리지 티어

    * **Primary storage**: 프로비저닝되는 고성능 SSD(최대 **192 TB**), 활성 데이터셋 용도
    * **Capacity pool storage**: 완전 탄력형, **PB**까지 확장 가능, 저비용(저빈도 접근용)
  * 티어링 활성화 시 접근 패턴 기반으로 자동 이동
* 대표 사용 사례

  * SnapVault 기반 **백업/아카이브**
  * SnapMirror 기반 **리전 간 DR 복제**
  * FlexCache로 **리전/온프레미스 간 데이터 근접 캐싱**
  * Amazon WorkSpaces의 공유 NAS 또는 로밍 프로파일 저장소로 활용

---

## Amazon FSx for OpenZFS

* 오픈소스 **OpenZFS** 기반의 완전관리형 파일 스토리지
* **NFS**(Network File System) 프로토콜로 접근
* AWS Graviton + 최신 디스크/네트워크 기술 기반
* 사용 사례

  * 온프레미스 ZFS 또는 Linux 파일 서버 데이터를 AWS로 마이그레이션
  * Linux/Windows/macOS 워크로드 전반

    * 빅데이터/분석, 코드/아티팩트 저장소, DevOps, 웹 콘텐츠 관리, EDA 프론트엔드, 유전체(Genomics), 미디어 처리 등
* 보안

  * 생성 시 **저장 데이터 암호화(At Rest)** 자동 활성화
  * 저장 시 암호화: **AES-256**
  * 전송 중 암호화(In Transit): 전송 중 암호화를 지원하는 EC2에서 접근 시 자동 암호화 지원
