---
type: Index
title: Solar Open 2 Claude Code 실사용 성능 보고서
description: Claude Code의 모델 백엔드로 Solar Open 2를 사용한 네 가지 태스크의 근거 기반 평가 색인
tags: [solar-open2, claude-code, evaluation, field-report]
timestamp: 2026-07-23T00:00:00+09:00
---

# Solar Open 2 Claude Code 실사용 성능 보고서

이 보고서 묶음은 Solar Open 2를 Claude Code CLI의 모델 백엔드로 사용했을 때 Claude 계열 모델을 어느 범위까지 대체할 수 있는지 실제 작업 기록을 바탕으로 평가한다. 일반 벤치마크 점수 대신 요구사항 이해, 도구 사용, 환경 적응, 오류 복구, 자기검증, 처리 시간과 사용자 개입 비용을 살핀다.

태스크마다 목적과 성공 기준이 다르므로 하나의 종합 점수로 서열화하지 않는다. 측정되지 않은 항목은 0점이 아니라 `N/A`로 표시하며, Solar Open 2의 모델 행동과 CLI·Plugin·운영 환경 문제를 구분한다. 다만 사용자가 실제로 부담한 변환과 감독은 대체 비용에 포함한다.

## 보고서

- [Task 01 — Ralphthon 재현](01-ralpthon/README.md): Codex Ralph Loop 결과를 Solar Open 2 + Claude Code로 재현하기 위한 이식 과정, 장애 해결과 현재 실행 준비도
- [Task 02 — 회의록 작성](02-meeting-minutes/README.md): 한국어 다문서 취합·구조화·요약 품질과 사실 검수 비용
- [Task 03 — Wiki 구조 재편](03-wiki-restructure/README.md): Task 01 원본 보존과 task 구조 정규화 과정의 정확성·지시 준수·자기검증
- [Task 04 — 토크나이저 비교](04-tokenizer-comparison/README.md): 비교 앱 프로토타입 구현과 핵심 수치 검증의 신뢰성

## 판정 범례

| 판정 | 의미 |
| --- | --- |
| 대체 가능 | 일반적인 검수만으로 해당 범위의 실무 투입 가능 |
| 조건부 대체 가능 | 작업 범위 제한과 독립 검증을 전제로 활용 가능 |
| 보조 도구 수준 | 초안·프로토타입에는 유용하지만 자율 완료를 맡기기 어려움 |
| 판정 불가 | 유효한 실행 또는 비교 증거가 부족함 |

## Task 01과 Task 03의 관계

Task 01은 랄프톤에서 Codex가 수행한 Ralph Loop를 Solar Open 2 + Claude Code로 재현하려는 실험이다. Task 03은 이후 저장소를 task 중심 구조로 바꾸면서 Task 01의 원본과 실행 자산을 복구·재배치한 작업이다.

Task 03에서 발생한 원본 보존과 경로 문제는 Task 03의 migration 품질로 평가한다. 그 결과 Task 01의 현재 실행 가능성이 달라진 사실은 Task 01 보고서에도 기록하되 같은 오류를 두 태스크에서 중복 감점하지 않는다.

## 증거 원칙

- 모델의 완료 보고보다 실제 파일, Git diff, 테스트와 세션 기록을 우선한다.
- 기존 Codex 산출물, 사용자 결정, Solar Open 2 작업과 후속 Codex 감사를 구분한다.
- 파일 생성 시각이나 커밋 시각을 모델 처리 시간으로 간주하지 않는다.
- 세션 구간에 사용자 대기나 병렬 실행이 포함되면 순수 모델 처리 시간과 구별한다.
- 직접 비교가 없으면 Claude 모델과 동등하거나 우수하다고 결론내리지 않는다.
