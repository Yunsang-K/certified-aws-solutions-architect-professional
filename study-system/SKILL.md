# AWS SAP Study Skill

ChatGPT가 AWS Certified Solutions Architect - Professional(SAP-C02) 학습 세션을 실행하기 위한 운영 규칙이다.

## 목표

사용자는 아래 루틴을 ChatGPT가 대신 실행하기를 원한다.

1. 레포 파일 1~2개 읽기
2. 서비스 비교표 만들기
3. SAP 스타일 문제 10문제 출제
4. 오답노트와 배운점 정리

## 기본 소스

- Repository: `Yunsang-K/certified-aws-solutions-architect-professional-korean`
- Progress: `study-system/progress.json`
- Study plan: `study-system/study-plan.md`
- Weak tags: `study-system/weak-tags.json`
- Notion map: `study-system/notion-map.md`

## 세션 시작 규칙

사용자가 `AWS SAP 세션 시작`, `오늘 세션 시작`, `progress.json 보고 이어서 진행해줘`라고 말하면 다음 순서로 진행한다.

1. `study-system/progress.json`을 먼저 확인한다.
2. `current_files`를 오늘 범위로 삼는다.
3. `current_files`가 비어 있으면 `study-system/study-plan.md`에서 다음 미완료 파일 1~2개를 고른다.
4. 해당 GitHub 레포 파일을 실제로 읽는다.
5. 읽지 않은 파일을 읽었다고 말하지 않는다.

## 세션 진행 순서

1. 오늘 범위 파일을 SAP-C02 시험 관점으로 요약한다.
2. 혼동 가능한 서비스를 비교표로 정리한다.
3. SAP-C02 스타일 시나리오 문제 10개를 출제한다.
4. 사용자가 답하면 채점한다.
5. 오답에 대해 틀린 이유, 배운점, 약점 태그를 정리한다.
6. 세션 종료 시 진행도 업데이트 초안을 만든다.

## 저장 규칙

GitHub 또는 Notion에 쓰기 작업을 하기 전에는 사용자의 명시적 승인을 받는다.

승인 문구 예시:

- `GitHub에 저장해`
- `이번 세션 저장해`
- `Notion에 기록해`
- `이대로 만들어`

## 문제 출제 규칙

- 단순 암기형이 아니라 시나리오형으로 출제한다.
- 요구사항, 제약조건, 비용, 운영부담, 보안, 가용성, 복구 목표를 포함한다.
- 각 문제에는 참조 파일명을 붙인다.
- 레포 근거가 부족하면 AWS 공식 문서를 보조 근거로 사용한다고 명시한다.

## 오답노트 형식

```text
문제 키워드:
- ...

내가 고른 답:
- ...

정답:
- ...

틀린 이유:
- ...

배운점:
- ...

약점 태그:
- ...

재출제 필요:
- yes/no
```
