---
type: Benchmark
title: "Task 05 성능 평가 — Ralphthon 철자 인식 오류 재현"
description: "Solar Open 2의 신조어 철자 추론·보존·교정과 저장소 작업 중 오타 확산을 평가하기 위한 계획 보고서"
tags: [solar-open2, claude-code, ralphthon, spelling, evaluation]
timestamp: "2026-07-24T00:00:00+09:00"
status: planned
model: "Upstage Solar Open 2"
harness: "claude-upstage / Claude Code CLI"
---

# Task 05 성능 평가 — Ralphthon 철자 인식 오류 재현

## 현재 상태

> **계획 수립 완료·실험 미실행.** 이 문서는 향후 Solar Open 2 실행 결과를 기록할 보고서 자리다. 현재 결과와 성능 판정은 모두 `N/A`이며, 실행 전에는 수치를 채우지 않는다.

정확한 표기는 **Ralphthon**, canonical slug는 `ralphthon`이다. 앞선 Solar Open 2 기반 저장소 작업에서는 잘못된 `ralpthon` 표기가 task·Wiki·실행 스크립트·Skill과 결과 폴더로 확산됐다. Task 05는 이 현상을 통제된 조건에서 다시 확인하기 위해 계획됐다.

실제 실행 명세는 별도 `_Upstage` 작업 공간의 `tasks/05-ralphthon-spelling-evaluation/EXECUTION_PLAN.md`에 둔다. Codex는 실행 명세와 이 계획 보고서를 준비하며, fixture·모델 응답·채점 결과는 Solar Open 2 실험 산출물과 분리한다.

## 평가 질문

1. canonical 철자를 명시하면 Solar Open 2가 `Ralphthon`을 정확히 복사하고 유지하는가?
2. 한글 음차 “랄프톤”만 제시하면 어떤 영문 철자를 생성하는가?
3. `Ralph + thon`이라는 조어 정보를 제공하면 결과가 달라지는가?
4. 잘못된 `ralpthon`과 authoritative `Ralphthon`이 충돌할 때 어느 쪽을 따르는가?
5. 저장소 구조화 작업에서 초기 오타가 파일명·경로·본문으로 확산되는가?
6. 사용자가 정확한 철자를 알려준 뒤 오타가 제거되고 재발하지 않는가?

## 실험 설계

- Solar Open 2 내부 대조만 사용한다.
- 여섯 철자 조건을 각각 독립 세션에서 10회 실행한다.
- 저장소 확산 trial은 임시 Git 저장소 10개에서 실행한다.
- 단순 언어 probe에는 파일 도구와 프로젝트 기억을 차단한다.
- 익숙한 단어, 유사 신조어와 혼동 철자를 내부 대조군으로 사용한다.
- 원본 응답과 실행 manifest를 보존하고 별도 결정론적 채점기로 평가한다.

## 판정 원칙

“랄프톤”만 보고 창안자가 선택한 `Ralphthon`을 맞히는 문제에는 언어적으로 유일한 정답이 없다. 따라서 음차-only 조건은 오답률이 아니라 생성 철자 분포로 해석한다.

다음은 재현 가능한 오류로 분류할 수 있다.

- canonical 철자를 명시했는데도 다르게 복사한 경우
- authoritative glossary보다 잘못된 다수 표기를 따른 경우
- 명시적 교정 후 현재 경로나 본문에 오타가 남거나 다시 생긴 경우

이 실험 결과만으로 `Ralphthon`이 학습 데이터에 포함됐는지는 판단하지 않는다.

## 결과

| 평가 항목 | 결과 | 상태 |
| --- | --- | --- |
| 명시적 철자 exact match | N/A | 미실행 |
| 음차-only 생성 분포 | N/A | 미실행 |
| 조어 힌트 조건 | N/A | 미실행 |
| 오타 교정 준수율 | N/A | 미실행 |
| 충돌 문맥 glossary 준수율 | N/A | 미실행 |
| 지연 문맥 철자 보존율 | N/A | 미실행 |
| 저장소 오타 확산 | N/A | 미실행 |
| 교정 후 잔존·재발 | N/A | 미실행 |

## 향후 결과 기록 요건

본 실행 뒤 이 보고서에 다음 근거를 연결한다.

- 모델 ID, wrapper hash, Claude Code 버전과 Git 기준점
- case별 비식별 raw 응답
- 실제 반복 수, 실패 호출과 partial run 여부
- exact match, 편집거리, 오류 유형과 Wilson 95% 구간
- 저장소 trial의 경로·본문별 확산 건수
- 대표 실패 사례와 반례
- Solar Open 2 자기보고와 독립 채점 결과의 차이

최종 평가는 신조어 지식 자체보다 **명시된 canonical 명칭을 보존하고, 충돌하는 자료에서 권위 규칙을 따르며, 저장소 변경 뒤 오류를 검증하는 에이전트 능력**을 중심으로 작성한다.

## 관련 문서

- [Ralphthon 표기 오류 확산과 canonical 경로 정정](../../wiki/projects/ralphthon-spelling-correction-2026-07-23.md)
- [Task 01 — Ralphthon 재현](../01-ralphthon/README.md)
- [Solar Open 2 Claude Code 실사용 성능 보고서](../index.md)
