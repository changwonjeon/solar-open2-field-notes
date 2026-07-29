---
type: Experiment
title: "Hermes Agent + Solar Open 2 저장소 감사와 제한 복구"
description: "네 차례 감사 교정과 독립 검증을 거쳐 상대링크, 누락 산출물, Task 00 색인과 README 구조를 복구한 실사용 기록"
tags: [solar-open2, hermes-agent, repository-audit, okf, self-correction]
timestamp: "2026-07-29T23:51:00+09:00"
status: completed
---

# Hermes Agent + Solar Open 2 저장소 감사와 제한 복구

## 목적

Solar Open 2를 Hermes Agent의 백엔드로 사용해 `_Upstage`의 Markdown/OKF
지식베이스를 감사하고, 확인된 결함만 제한적으로 수정할 수 있는지
평가했다. 단순한 최종 산출물뿐 아니라 오류 인정, 피드백 반영,
도구 출력과 보고서의 일치 여부를 함께 관찰했다.

작업 대상은 `/home/redux80/_Upstage/tasks/00-hermes/`이며, 이 문서는
2026년 7월 29일 23시 51분 KST까지의 과정을 종합한다.

## 진행 흐름

### 기록 체계 생성

Hermes는 Task 00에 세션, context snapshot, playbook, model, reference와
색인을 분리하는 기록 체계를 만들었다. 첫 세션 문서와 OKF 작성
playbook, OKF 참고 자료가 생성됐다.

초기 문서는 context snapshot과 LLM-Wiki 가이드를 산출물로 링크했지만
실제 파일은 없었다. Task 00 및 공통 문서의 상대경로와 README 구조에도
불일치가 있었다.

### 1차 감사

첫 읽기 전용 감사 보고서는 심각도, 수정 제안, 재현 명령과 오탐 가능성을
구조화했다. 반면 이미 존재하는 `docs/index.md`의 Task 00을 누락으로
오판했고 링크와 frontmatter를 전수 검사하지 않았다. 하위
`AGENTS.md`의 적용 범위도 일부 잘못 해석했다.

### 2~4차 교정

구체적인 반례와 검증 조건을 제공하자 다음 항목은 점진적으로 개선됐다.

- Task 00 색인 누락 오판 철회
- `/docs/AGENTS.md`와 `/tasks/**`의 스코프 구분
- Task 05 상위 링크의 올바른 `../../../` 경로 확인
- 세션 템플릿 링크를 생성될 문서 위치에서 판정
- 이전 `write_file` 호출의 읽기 전용 절차 위반 인정

그러나 각 교정 보고서에는 새로운 문제가 남았다.

- 링크 분류 중복과 합계 맞추기
- 모집단 밖 Schema를 면제 수에 포함
- 실제 실행하면 0개 파일을 반환하는 manifest 명령
- 주장한 52/0 대신 380개 대상·327개 실패를 내는 YAML 명령
- 재추출 없이 이전 합계에서 중복 수를 빼 링크 occurrence 총수를 변경

감사 데이터는 최종 `INCOMPLETE`, 절차 준수는 `NON_COMPLIANT`로
판정했다.

## 제한 수정

감사 범위를 더 확장하는 대신 Codex가 독립 검증한 링크 8개만 네 파일에서
수정하도록 허용했다.

| 범위 | 결과 |
| --- | --- |
| Task 00 공통 docs 링크 | 4개 정정 |
| Task 05의 Task 01 링크 | 2개 정정 |
| Task 05 output 링크 | 2개 정정 |
| 대상 존재 검사 | 8/8 통과 |
| `git diff --check` | 통과 |

실제 링크 수정은 정확했지만 Hermes 완료 보고의 Git 상태와 수정 후
SHA-256 표에는 오류와 placeholder가 남았다. 작업 결과와 보고 품질을
분리해 평가했다.

## 누락 산출물 복원

누락됐던 다음 두 문서를 사후 복원했다.

- `tasks/00-hermes/context-snapshots/2026-07-29-initial-context.md`
- `tasks/00-hermes/references/llm-wiki-guide.md`

Snapshot은 첫 세션 당시의 원본이 아니라는 점과
`provenance: reconstructed-after-session`을 명시했다. LLM-Wiki 문서는
외부 표준의 공식 설명이 아닌 Task 00 로컬 운영 가이드로 작성했다.

복원 과정에서는 Task 00 log 동기화 실패, 미래 timestamp와 README 구조
손상이 발생했다. 후속 제한 작업으로 log·index·timestamp를 고쳤으나,
README에서 Task 01 행이 잘못 배치되고 Task 02 행이 사라지는 회귀가 한
차례 더 발생했다.

마지막에는 README의 Task 00~02 블록 전체를 검증된 정답 구조로
교체했다. Task 00~03 루트 행, Task 01·02 자식 구조와 교차 오염 부재를
독립적으로 확인했다.

## 최종 결과

| 항목 | 최종 상태 |
| --- | --- |
| 검증된 상대링크 8개 | 완료 |
| 누락 snapshot 및 LLM-Wiki 가이드 | 사후 복원 완료 |
| Task 00 index와 log | 정합화 완료 |
| 신규 문서 timestamp와 provenance | 정정 완료 |
| 루트 README Task 00~02 구조 | 복구 완료 |
| YAML parsing | 신규 문서 2/2 통과 |
| `git diff --check` | 통과 |
| Git staging·commit·push | 없음 |

최종 README SHA-256은
`7cf0c28476be9ee9db5a1b5b1a9a1dd7d8b3dfe68b6fbcf34034d7715ac8e2c4`다.

## 평가

Solar Open 2 + Hermes Agent는 좁고 명시적인 수정 계약 아래에서는
실제 파일을 정확히 고칠 수 있었다. 특히 경로 후보와 기대 occurrence,
허용 파일, 완료 gate를 고정한 뒤에는 링크 수정이 성공했다.

반면 넓은 저장소 감사와 자체 검증 보고에서는 그럴듯한 수치와 재현
명령이 실제 실행 결과와 반복적으로 어긋났다. 수정 범위가 넓어졌을 때는
README 구조 회귀도 발생했다. 따라서 현재 효과적인 운영 방식은 다음과
같다.

1. 감사와 수정을 분리한다.
2. 모델 보고보다 결정론적 검사 결과를 우선한다.
3. 한 번에 허용하는 파일과 변경 occurrence를 고정한다.
4. untracked 파일은 Git diff 외에 hash와 직접 내용을 검사한다.
5. 완료 선언 뒤 다른 에이전트 또는 사람이 독립 검증한다.

이번 실험은 Hermes의 초기 정확성이 아니라, 구체적 피드백을 받은 뒤
오류를 부분적으로 인정하고 제한된 작업을 결국 완수하는 과정을
보여준다. 자율적인 최종 감사자보다는 **검증 계약이 붙은 실행
에이전트**로 사용할 때 더 안정적이었다.

## 시각 자료

![Solar Open 2 Hermes 감사와 Codex 검토](../../assets/screenshots/2026-07-29-hermes-solar-codex-reviewing.png)

캡처에는 Solar Open 2 Hermes 세션의 `/context` 표시와 Codex의 교정
프롬프트가 함께 담겨 있다. 표시값은 context 97,071/262,144 tokens
(37%), 세션 경과 8분이며 추정 사용량으로만 기록한다.

## 관련 문서

- [Task 00 상세 관찰 보고서](../../reports/00-hermes/README.md)
- [Solar Open 2 실사용 성능 보고서 색인](../../reports/index.md)
- [Field Notes 변경 로그](../../log.md)
