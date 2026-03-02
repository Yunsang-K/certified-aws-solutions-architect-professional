# Elastic Transcoder 및 AWS Elemental MediaConvert

* **MediaConvert**는 비교적 최신 AWS 서비스로, **Elastic Transcoder를 대체**하는 방향의 서비스다.
* **MediaConvert는 Elastic Transcoder가 제공하던 기능을 포함하면서 더 많은 기능(상위 호환, superset)**을 제공한다.
* 두 서비스 모두 **파일 기반(File-based) 비디오 트랜스코딩** 서비스다.
* 두 서비스 모두 **서버리스 트랜스코딩**이며, **사용량 기반 과금(pay-per-use)**이다.
* 작업 제출 방식:

  * **Elastic Transcoder(ET)**: 파이프라인(pipeline)에 잡(job)을 추가
  * **MediaConvert(MC)**: 큐(queue)에 잡(job)을 추가
* 처리 흐름:

  * 입력 파일은 **S3에서 로드**
  * 트랜스코딩 처리 후 결과물을 **S3에 저장**
* **MediaConvert는 EventBridge를 통한 잡 상태/완료 시그널링**을 지원한다.
* ET/MC 아키텍처 예시:

  * `[ET/MC architecture](images/ElasticTranscoder&MediaConvert.png)`
* 사용 케이스:

  * **서버리스 + 이벤트 기반(event-driven) 미디어 처리 파이프라인**에서 미디어 변환(트랜스코딩)이 필요할 때 사용한다.

## ET vs MC 선택 기준

* **Elastic Transcoder는 레거시(legacy)**이므로, **기본 선택은 MediaConvert**가 맞다.
* **MediaConvert**는 다음 측면에서 더 유리하다:

  * 더 많은 **코덱/포맷 지원**
  * 대규모 처리량 및 **병렬 처리(동시 처리)**에 더 적합한 설계
  * **Reserved(예약) 요금** 옵션 지원(비용 최적화 가능)
* 코덱 관련 정정:

  * ~~ET가 WebM(VP8/VP9), animated GIF, MP3, Vorbis, WAV에 필수~~
  * **MediaConvert가 위 항목도 지원**하므로, 특별한 제약이 없다면 **MediaConvert를 우선** 선택하면 된다.
