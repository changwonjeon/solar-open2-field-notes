---
title: Solar Open 2 수작업 전달 프롬프트
type: playbook
description: Codex 설계를 Solar 세션에 단계적으로 전달하는 사용자용 프롬프트
tags: [solar-open2, handoff, claude-upstage]
timestamp: 2026-07-19
producer: Codex
---

# Solar Open 2 수작업 전달 프롬프트

## 사용 원칙

- 각 단계는 새 결과를 검토한 뒤 다음 단계로 진행한다.
- Solar가 조사하지 않은 내용을 사실로 받아쓰게 하지 않는다.
- Step 1~2에서는 파일을 수정하지 못하게 한다.
- 실제 구현은 Solar 세션 안에서만 수행한다.
- Codex staging 문서를 `_Upstage`에 복사하지 않는다. 필요한 부분은 사용자가 prompt로 전달한다.

## Prompt 1 — 읽기 전용 재조사

```text
이 저장소에서 Solar Open 2 + Claude Code로 3시간 Ralph Loop를 실행하려 했지만 본 실행이 성립하지 않았다.

지금은 어떤 파일도 수정하지 말고 다음을 조사해라.
1. RALPH_GOAL.md와 원본 Codex 실행 스크립트가 요구하는 반복·권한·checkpoint·Git·deadline 계약
2. 현재 .claude/skills/run-ralphthon, Solar 실행 스크립트와 로그가 실제로 제공하는 계약
3. Claude Code가 현재 발견하는 skill 목록에 ralph 관련 skill이 있는지
4. recorder, checkpoint monitor와 watchdog이 launcher에 실제 연결돼 있는지
5. Git branch, upstream, tracked/untracked 변경과 .claude ignore 상태

결과를 확인됨/추정/미검증으로 나누고, 근거 경로와 명령 결과를 제시해라. 모델 성능 실패와 실행환경 실패를 분리해라.
```

## Prompt 2 — 간극과 최소 구성 제안

```text
앞선 조사 결과를 바탕으로 Ralph Loop를 성립시키는 최소 구성을 설계해라. 모든 문제를 skill로 만들지 말고 다음으로 분류해라.
- Claude Code project skill
- deterministic script 또는 hook
- persistent state file
- 사용자 사전 작업

기본 후보는 /solar-ralph와 /git-checkpoint 두 skill이다. recover와 finalize는 /solar-ralph의 mode/reference로 통합할 수 있는지 검토해라. remote push는 실행 중 금지하고 local checkpoint commit만 허용해라.

아직 파일은 수정하지 말고, 최종 파일 트리·각 구성요소 책임·호출 인터페이스·실패 처리·테스트 계획을 제시해라.
```

## Prompt 3 — 설계 검증

```text
제안한 설계를 현재 Claude Code와 claude-upstage 환경에서 검증해라. 아직 구현하지 마라.

반드시 확인할 것:
- project skill 발견 경로와 직접 호출명
- skill context가 compaction 뒤에도 필요한 계약을 유지하는 방법
- allowed tools와 permission 설정의 실제 적용 가능성
- wrapper가 지원하지 않는 CLI 옵션에 의존하는 부분
- Git checkpoint가 사용자 기존 변경을 침범하지 않는 방법
- session 중단 후 state와 HEAD를 reconciliation하는 방법
- 10분/30분 축소시험에서 관찰할 증거

막히는 항목은 가정하지 말고 blocking risk로 표시해라. 검증 결과에 따라 설계를 수정한 최종 구현 계획을 출력해라.
```

## Prompt 4 — Solar 구현

```text
승인된 최종 계획대로 구현해라.

제약:
- 실제 작성자는 Solar Open 2 via claude-upstage다.
- .claude/skills에는 /solar-ralph와 /git-checkpoint만 만든다.
- 상세 계약은 각 skill이 직접 연결하는 references/scripts로 분리한다.
- 기존 .claude 전체 ignore를 해제하지 말고 두 project skill만 선택적으로 추적 가능하게 한다.
- remote git 명령, destructive git 명령과 broad git add는 구현·실행하지 않는다.
- 기존 사용자 변경을 덮어쓰지 않는다.
- 구현 후 변경 파일, 설계 판단과 미해결 위험을 보고하되 아직 commit하지 않는다.
```

## Prompt 5 — 정적 검증과 짧은 시험

```text
구현한 skill과 runtime을 검증해라. 먼저 정적 검증과 discovery smoke test를 수행하고, 통과하면 작은 임시 P0로 편집→테스트→local checkpoint commit 한 사이클을 시험해라.

실제 Ralph 과제나 3시간 타이머는 시작하지 마라. remote push도 하지 마라.

검증 결과를 acceptance checklist에 맞춰 pass/fail/evidence로 보고하고, 실패하면 같은 우회 수정을 반복하지 말고 failure signature와 원인을 기록해라.
```

## Prompt 6 — 축소 실행 준비

```text
사람이 baseline과 experiment branch/upstream을 준비한 상태인지 읽기 전용으로 확인해라. 준비되지 않았으면 실행하지 말고 정확한 preflight 실패를 보고해라.

준비됐으면 10분 soak test 계획과 종료·복구 시험 절차를 출력해라. 사용자 명시 승인 전에는 session을 시작하거나 파일을 수정하지 마라.
```
