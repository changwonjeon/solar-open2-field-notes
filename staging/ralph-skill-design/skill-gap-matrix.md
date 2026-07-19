---
title: Solar Ralph Loop 기능·스킬 간극표
type: design
description: 필요한 기능을 skill, script, hook과 state로 배치하기 위한 결정표
tags: [solar-open2, claude-code, skills, ralph-loop]
timestamp: 2026-07-19
producer: Codex
---

# Solar Ralph Loop 기능·스킬 간극표

## 최소 구성 결정

실제 구현은 두 개의 project skill과 결정론적 runtime script로 제한한다.

1. `/solar-ralph`: 자율 작업·검증·복구·종료 계약
2. `/git-checkpoint`: 안전한 local checkpoint commit 계약
3. runtime scripts: launch, heartbeat, recorder, deadline과 artifact manifest

기존 `run-ralpthon`은 위 스킬을 호출하는 launcher 설계로 대체하거나 축소한다. 별도의 recover/finalize skill은 만들지 않고 `/solar-ralph`의 references와 mode argument로 통합한다.

## 기능 배치

| 기능 | 현재 상태 | 배치 | 이유 |
| --- | --- | --- | --- |
| Goal 해석과 P0 선택 | Goal 전체 주입 | `/solar-ralph` | 판단이 필요한 agent workflow |
| 반복·축소 경로 | 외부 continue prompt | `/solar-ralph` | failure signature와 task 상태를 이해해야 함 |
| 테스트 선택 | Goal에만 서술 | `/solar-ralph` reference | 과제별 검증 계약 |
| local commit | 명시적 절차 없음 | `/git-checkpoint` | 좁은 Git 권한과 재사용 가능한 gate |
| remote push | Goal에서 금지 | 사람 전용 | 인증·유출·네트워크가 실험을 오염시킴 |
| process launch | tmux script | runtime script | 결정론적 실행 필요 |
| deadline | watchdog | runtime script | 모델 판단에 맡기지 않음 |
| heartbeat | pane 길이 휴리스틱 | runtime script | 상태/hash 기반 측정 필요 |
| permission | 화면에 `y` 입력 | settings + 최소 script | broad auto-accept 금지 |
| transcript 기록 | recorder 미연결 | runtime script | 모든 실행에서 자동 생성 |
| checkpoint 상태 | log keyword 추정 | state JSON + Git | 명시적 상태가 신뢰 가능 |
| resume | continue prompt | `/solar-ralph resume` | 상태·commit reconciliation 필요 |
| handoff | Goal에만 정의 | `/solar-ralph finalize` | deadline과 미완료 상태를 정직하게 요약 |

## 만들지 않을 스킬

- tmux 전용 skill: launch/stop/status는 script interface가 더 안정적이다.
- auto-permission skill: skill의 `allowed-tools`는 필요한 명령만 허용하고 화면 keyword 자동 승인은 제거한다.
- auto-push skill: 실험 중 원격 변경은 금지한다.
- generic coding skill: Solar Open 2의 일반 코딩 능력을 별도 지침으로 과도하게 보정하지 않는다.
- Codex compatibility shim: `.codex/skills` 결과물을 만드는 과제와 Claude Code runtime skill을 혼동하지 않는다.

## 후보별 필수 책임

### `/solar-ralph`

- 공통 task spec과 Solar runtime 계약을 읽는다.
- state를 초기화하거나 기존 state와 HEAD를 reconciliation한다.
- ready P0 하나만 선택하고 완료 조건을 먼저 적는다.
- 구현 후 관련 테스트를 실행하고 증거를 state에 기록한다.
- 검증된 변경만 `/git-checkpoint`에 전달한다.
- 같은 failure signature가 두 번 나오면 failure ledger에 기록하고 축소 작업으로 전환한다.
- deadline 10분 전 신규 기능을 금지하고 finalize한다.

### `/git-checkpoint`

- branch, HEAD, upstream, status와 identity를 확인한다.
- 허용 경로 밖 변경, 비밀 패턴, 대용량 파일과 생성 로그를 검사한다.
- 사용자가 만든 기존 변경과 Solar 변경을 구분할 수 없으면 commit하지 않는다.
- 명시된 테스트 증거가 없으면 commit하지 않는다.
- `git add`는 승인된 pathspec만 사용하고 `git add .`를 금지한다.
- local commit만 만들고 resulting hash를 state에 반환한다.

## 추가 조사 항목

- Solar provider에서 Claude Code skill tool 호출이 완전하게 작동하는지
- `allowed-tools` frontmatter가 `claude-upstage` 환경에서도 동일하게 적용되는지
- context compaction 뒤 invoked skill의 핵심 계약이 유지되는지
- 내장 `/loop`가 한 세션에서 skill invocation을 반복할 때 state를 보존하는지
- wrapper를 수정하지 않고 project skill과 permission settings만으로 30분 실행이 가능한지
