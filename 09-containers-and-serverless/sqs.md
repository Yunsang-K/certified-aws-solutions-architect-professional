## SQS (Amazon Simple Queue Service)

* SQS는 **완전관리형 메시지 큐(Managed Message Queue)** 서비스입니다.
* **퍼블릭 AWS 서비스**이며, 기본적으로 **고가용성(HA)과 높은 처리량**을 제공하도록 설계되어 있습니다.
* 큐 타입은 2가지입니다.

  * **Standard**: 순서 보장 “최선(best effort)” 수준
  * **FIFO**: 순서 보장(선입선출), 중복 처리 제어 기능 제공
* SQS **기본 메시지 최대 크기**는 **256 KB**입니다.

---

## Visibility Timeout

* 소비자가 메시지를 `ReceiveMessage`로 가져오면, 그 메시지는 일정 시간 **다른 소비자에게 보이지 않게 숨김 처리**됩니다.
* 이 숨김 기간이 **VisibilityTimeout**이며, 그 시간 동안 소비자는 처리를 완료하고 **성공 시 메시지를 삭제(DeleteMessage)** 해야 합니다.
* 삭제하지 않으면 VisibilityTimeout 만료 후 메시지는 다시 “가시 상태”가 되어 **다른 소비자가 재처리**할 수 있습니다.

  * Standard에서는 이 특성 때문에 **at-least-once(최소 1회 전달)**가 기본입니다.

---

## Dead-Letter Queue (DLQ)

* 반복 실패하는 메시지를 분리하기 위한 큐입니다.
* 메시지는 수신될 때마다 `ReceiveCount`(ApproximateReceiveCount)가 증가하고,

  * 소스 큐에 설정한 `maxReceiveCount`를 초과하면 해당 메시지가 **DLQ로 이동**합니다.
* DLQ를 통해 다음을 할 수 있습니다.

  * 실패 메시지에 대한 **알람/모니터링**
  * 원인 분석 및 재처리(리드라이브) 등 별도 파이프라인 구성

---

## 확장/연동

* SQS는 워크로드 디커플링(결합도 감소) 및 스파이크 흡수에 유용합니다.
* 대표 연동:

  * **Lambda 이벤트 소스 매핑(Event Source Mapping)** 으로 메시지 기반 처리
  * **Auto Scaling 기반 소비자(EC2/컨테이너) 수평 확장** 패턴에서 큐를 버퍼로 사용

---

## Standard vs FIFO

### Standard Queue

* **At-least-once delivery**: 중복 전달 가능
* **Best-effort ordering**: 순서 보장 불가(가능하면 유지하려고 노력하는 수준)
* 매우 높은 처리량(사실상 제한이 느슨함)

### FIFO Queue

* **순서 보장**(동일 Message Group 단위로 순서 보장)
* **중복 처리 제어**: 중복 제거(deduplication) 기능 제공
* 처리량 제한이 존재(시험에 자주 나옴)

  * 배치 사용 시 더 높은 TPS 가능
  * “High Throughput mode(FIFO)” 옵션이 존재
* FIFO 큐 이름은 반드시 **`.fifo`** 접미사가 필요

> SAP 포인트: FIFO는 “전역 순서”가 아니라 보통 **Message Group 단위의 순서 보장**으로 설명하는 게 더 정확합니다.

---

## 과금(Billing)

* SQS는 **요청(Request) 단위 과금**입니다.
* `ReceiveMessage` 1회로 **1~10개 메시지**를 받을 수 있으며(조건/설정에 따라), 수신 데이터 합계 제한이 있습니다.
* **짧은 폴링을 자주 하면 요청 수가 늘어 비용이 비효율적**일 수 있습니다.

---

## 폴링(Polling)

* **Short Polling**: 즉시 반환, 메시지가 없으면 0개를 바로 반환 가능
* **Long Polling**: `WaitTimeSeconds`(최대 20초) 동안 메시지를 기다렸다가 반환

  * 요청 수를 줄여 **비용과 빈 응답을 감소**시키는 데 유리

---

## 암호화/보안

* SQS는 **전송 중 암호화(TLS)** 및 **저장 시 암호화(KMS)** 를 지원합니다.
* 접근 제어:

  * IAM **Identity Policy**
  * SQS **Queue Policy**(리소스 정책)로 **교차 계정 접근** 허용 가능

---

## Extended Client Library(대용량 메시지)

* 기본 메시지 크기 제한은 **256 KB**입니다.
* 더 큰 페이로드가 필요하면 **Extended Client Library** 패턴을 사용합니다.

  * 실제 payload는 **S3에 저장**
  * SQS 메시지에는 **S3 객체 참조(포인터)**만 포함
  * 수신 시 라이브러리가 S3에서 payload를 가져와 “메시지처럼” 처리
  * 삭제 시 SQS 메시지뿐 아니라 **S3 payload도 함께 삭제**하도록 구성 가능
* 대용량은 최대 **2 GB**까지 처리 가능(구현체/언어는 라이브러리별로 다름)

---

## Delay Queue

* 메시지 전달을 지연시키는 기능입니다.
* 큐 레벨에서 `DelaySeconds`를 설정하면, 메시지는 그 시간 동안 **가시 상태가 되지 않음**(최대 15분).
* 메시지별로 지연을 주는 **per-message delay**도 가능(단, FIFO는 제한이 있을 수 있어 시험에서는 “제약 있음”으로 기억하는 편이 안전)

---

## (SAP에서 같이 묻는 보완 포인트 5개)

1. **Standard는 중복 가능 → 멱등성(idempotency) 설계**가 핵심
2. **Visibility Timeout vs 처리시간**: 처리시간보다 짧으면 중복 처리 유발
3. **DLQ는 장애 격리 + 알람 + 재처리(리드라이브) 설계**로 묶어서 출제
4. **Long Polling은 비용/성능 최적화 기본값**으로 많이 권장됨
5. 대용량 메시지는 “S3 + 포인터” 패턴(Extended Client)로 정리

