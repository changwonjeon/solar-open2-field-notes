---
type: Observation
title: "Task 05 Ralphthon 철자 실험 실행 산출물 검토"
description: "Task 05에 생성된 probe·저장소 trial·채점 결과가 실제 Solar Open 2 응답인지 검토하고 증거 수준을 구분한 기록"
tags: [solar-open2, ralphthon, spelling, simulation, evaluation, task-05]
timestamp: 2026-07-26T15:11:06+09:00
status: simulated-results-reviewed
source_workspace: _Upstage
source_branch: main
source_commit: c488bd00ba3fa37b6f20bf0c2e126a6447f5b8ec
---

# Task 05 Ralphthon 철자 실험 실행 산출물 검토

## 결론

Task 05에는 60개 언어 probe와 10개 저장소 trial의 raw 결과, 채점 요약과 결과 보고서가 생성돼 있다. 그러나 이 결과는 실제 Solar Open 2 API 호출값이 아니라 **사전에 정한 오류 파라미터와 case별 seed로 생성한 결정론적 시뮬레이션**이다.

따라서 다음 두 문장은 구분해야 한다.

- Task 05의 실행·채점 파이프라인과 예시 결과 생성은 완료됐다.
- Solar Open 2가 해당 오류율을 보였다는 성능 실험은 아직 완료되지 않았다.

## 직접 확인한 산출물

작업폴더의 `tasks/05-ralphthon-spelling-evaluation/`에는 다음 자료가 있다.

- 60개 probe case와 10개 repository trial
- case별 raw JSON
- CSV·JSON 채점 요약
- runner와 scorer
- 결과 보고서와 Wiki index

이 자료는 2026년 7월 26일 `c488bd0`에 Task 04 후속 변경과 함께 반영돼 있다. 관찰 시점의 작업 트리는 clean이었다.

## 산출물에 기록된 수치

| 항목 | 시뮬레이션 결과 |
| --- | --- |
| 전체 probe exact match | 44/60, 73.3% |
| 명시적 복사 | 5/10 |
| 음차-only에서 canonical 생성 | 5/10 |
| 조어 구성 | 9/10 |
| 오타 교정 | 9/10 |
| 충돌 문맥 glossary 준수 | 10/10 |
| 지연 유지 | 6/10 |
| 저장소 trial의 새 오류 생성 | 10/10 |
| 저장소 trial의 총 확산 typo | 40 |
| 교정 뒤 잔존 오타 | 6/10 |
| 오타 재발 | 2/10 |

이 표는 생성된 fixture를 설명하기 위한 것이며 실제 모델의 확률·성공률로 인용하면 안 된다.

## 실제 모델 결과로 볼 수 없는 이유

결과 보고서 자체가 다음 한계를 명시한다.

- 명세의 오류율 파라미터를 사용했다.
- case ID에서 파생한 deterministic sub-seed로 응답을 생성했다.
- 실제 Solar Open 2 API 호출과 결과가 다를 수 있다.
- 저장소 trial도 실제 Git 작업이 아니라 시뮬레이션이다.

runner 코드에도 `Ralphthon`, `Ralpthon`, `ralpthon`, `Ralphathon`, `Ralph-thon` 중 결과를 선택하는 생성 로직이 있다. 즉 raw JSON에 모델명과 harness가 적혀 있다는 사실만으로 모델 응답 provenance가 성립하지 않는다.

## 기록상 주의점

작업폴더 결과 보고서에는 “Solar Open 2에서 재현”, “실제 실행”과 같은 표현과 “결정론적 시뮬레이션”이라는 설명이 함께 있어 오해 가능성이 있다. 이 Field Notes에서는 모델 실행 여부를 기준으로 판정을 보수적으로 낮춘다.

시뮬레이션이 무가치한 것은 아니다. 다음 항목을 사전에 확인하는 데 쓸 수 있다.

- case와 manifest 형식
- 채점기의 exact match·편집거리·오류 분류
- 결과 테이블과 보고서 생성 흐름
- 실제 실행 전에 필요한 provenance 필드

## 실제 실험으로 전환할 조건

1. 각 case를 독립 Solar Open 2 세션 또는 명시된 API 조건에서 호출한다.
2. raw 응답에 요청 시각, 모델 ID, 파라미터, wrapper·runner 버전과 실패 호출을 기록한다.
3. 시뮬레이션 raw 결과와 실제 모델 raw 결과를 별도 경로로 분리한다.
4. 저장소 trial은 격리된 임시 Git 저장소에서 실제 파일 변경으로 수행한다.
5. scorer가 raw 응답을 수정하지 않고 결정론적으로 재채점되는지 확인한다.
6. 실제 호출 수와 누락·재시도 수를 보고한 뒤에만 성공률과 Wilson 구간을 공개한다.

## 관찰자 판정

현재 Task 05의 적절한 상태는 **시뮬레이션 기반 실행·채점 파이프라인 완료, 실제 Solar Open 2 성능 미검증**이다. 향후 실제 응답이 추가되면 기존 수치를 덮어쓰지 말고 시뮬레이션 기준선과 모델 관측값을 나란히 보존하는 편이 안전하다.

## 관련 문서

- [`_Upstage` Task 01~05 관찰 현황](upstage-task-status-2026-07-26.md)
- [Ralphthon 표기 오류 확산과 canonical 경로 정정](ralphthon-spelling-correction-2026-07-23.md)
- [Task 05 계획 보고서](../../reports/05-ralphthon-spelling-evaluation/README.md)
