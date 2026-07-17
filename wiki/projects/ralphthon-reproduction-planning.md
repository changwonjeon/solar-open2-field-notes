---
type: Project
title: 랄프톤 재현 비교 실험 — 계획 수립
description: Solar Open 2와 Claude Code로 기존 Ralph Loop 작업을 재현하려는 계획의 작업폴더 관찰 기록
tags: [solar-open2, claude-code, codex, ralphthon, ralph-loop, experiment, planning]
timestamp: 2026-07-17T11:50:35+09:00
resource: https://github.com/changwonjeon/solar-open2-experiments/commit/6d62228f2d808d33c8921b34b506e40125073e2e
status: planning
source_workspace: _Upstage
source_commit: 6d62228f2d808d33c8921b34b506e40125073e2e
---

# 랄프톤 재현 비교 실험 — 계획 수립

## 요약

작업폴더에는 랄프톤에서 Codex CLI로 수행했던 Ralph Loop를 **Solar Open 2를 백엔드로 사용하는 Claude Code**에서 재현하고 비교하기 위한 계획이 작성되어 있다.

2026년 7월 17일 11시 50분(KST) 기준으로 계획 문서화는 커밋됐지만, 실험 자료 복사와 실행 환경 구성은 아직 시작된 증거가 없다. 따라서 현재 단계는 **계획 수립 완료, Phase 1 대기**로 판단한다.

## 관찰 근거

작업폴더의 `main` 브랜치는 관찰 시점에 변경사항이 없는 깨끗한 상태였다. 최신 커밋은 다음과 같다.

| 항목 | 확인값 |
| --- | --- |
| 커밋 | `6d62228f2d808d33c8921b34b506e40125073e2e` |
| 시각 | 2026-07-17 11:50:35 KST |
| 제목 | `docs: 랄프톤(Solar Open2) 비교 실험 위키 추가 및 README 업데이트` |
| 변경 규모 | 3개 파일, 234줄 추가 |

이 커밋에서 확인된 변경은 다음과 같다.

- `README.md`: 비교 실험의 목적, 프롬프트, 비교 방식과 진행 상태 추가
- `docs/log.md`: 계획 수립 및 후속 단계 기록 추가
- `docs/notes/notes/ralphthon-solar-comparison.md`: 207줄 분량의 비교 실험 계획 추가

> 작업폴더 원문에는 `Solar Open2` 표기가 남아 있다. 이 기록에서는 공식 명칭인 **Solar Open 2**를 사용하고, 파일명과 커밋 제목은 증거 보존을 위해 원문 그대로 인용한다.

## 실험 목적

계획 문서가 정의한 목적은 기존 Ralphthon 작업의 목표 문서와 실행 구조를 Solar Open 2 + Claude Code 환경에 옮기고 다음 항목을 비교하는 것이다.

- 목표 문서의 P0 요구사항 이해도
- 큰 작업을 단계로 나누는 능력
- 자율적인 구현과 도구 사용
- 오류 발생 후 복구 방식
- 생성된 코드와 문서의 품질
- 사용자 개입 없이 작업을 지속하는 능력

비교는 정성 평가, 실행 과정 기록, 정량 지표 측정의 세 축으로 설계되어 있다.

## 계획된 단계

| 단계 | 계획 내용 | 관찰 상태 |
| --- | --- | --- |
| Phase 1 | 기존 Ralphthon 핵심 자료를 작업용 사본으로 복사 | 대기 |
| Phase 2 | Codex 실행 스크립트를 Claude Code용으로 조정 | 미시작 |
| Phase 3 | 세션 로그, 체크포인트와 비교 지표 수집기 구축 | 미시작 |
| Phase 4 | Solar Open 2 기반 Ralph Loop 실행 | 미시작 |
| Phase 5 | Codex 결과와 비교하고 최종 보고서 작성 | 미시작 |

## 계획된 평가 항목

계획 문서는 다음 지표를 제안한다.

- P0 완료율
- 기대 산출물 생성률
- 스키마 준수율
- 중복 방지 여부
- 전체 및 단계별 실행 시간
- 오류 발생 후 자동 복구율
- 리뷰 결과의 정밀도·재현율·F1
- 실행 중 사용자 개입 횟수

이 지표는 아직 Solar Open 2 실행 결과가 없는 **측정 계획**이다.

## 원문 주장과 검증 상태

작업폴더 계획에는 기존 Codex 실행의 기준값으로 `P0 14/14`, `29/29 files`, `schema 30/30`, `0 duplicate`, `4/4 recovery`, `F1 1.0`, 약 3시간, 사용자 개입 0회가 제시되어 있다.

이번 관찰에서는 해당 수치의 근거 파일을 작업폴더 안에서 확인하지 않았다. 따라서 이는 현재 **작업폴더 계획 문서가 제시한 기준값**이며, 독립적으로 검증된 결과로 취급하지 않는다. 후속 기록에서는 원본 결과 파일과 계산 방법을 확인한 뒤 검증 여부를 갱신해야 한다.

## 현재 존재하지 않는 산출물

관찰 시점에 다음 계획 경로는 작업폴더에 존재하지 않았다.

```text
docs/experiments/ralphthon/
src/scripts/ralpthon/
data/results/ralpthon/
data/datasets/ralphthon-mock/
```

Git이 추적하는 Ralphthon 관련 파일은 계획 문서인 `docs/notes/notes/ralphthon-solar-comparison.md` 하나뿐이었다. 이 사실을 근거로 실제 자료 복사나 실행이 완료됐다고 보지 않는다.

## 후속 관찰 기준

다음 관찰에서는 아래 증거가 실제로 생성됐는지 확인한다.

1. Ralphthon 목표·fixture·스크립트의 작업용 사본
2. Solar Open 2와 Claude Code에 맞게 조정된 실행 명령
3. 실행 시작 및 종료 시각
4. 전체 세션 로그와 오류 기록
5. P0별 체크포인트와 Git 변경 이력
6. 생성 파일 목록과 스키마 검증 결과
7. 사용자 개입 여부
8. 비교 지표의 산식과 원본 데이터

## 관련 문서

- [저장소 색인](../../index.md)
- [관찰 로그](../../log.md)
- [기존 Ralphthon 공개 저장소](https://github.com/changwonjeon/20260712-ralphthon-submit)

