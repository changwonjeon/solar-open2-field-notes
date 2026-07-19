---
title: Solar Ralph Loop 스킬 인수 체크리스트
type: checklist
description: Solar 구현물과 축소 실행을 검증하는 단계별 acceptance criteria
tags: [solar-open2, validation, ralph-loop]
timestamp: 2026-07-19
producer: Codex
---

# Solar Ralph Loop 스킬 인수 체크리스트

## A. Provenance와 격리

- [ ] 실제 skill 파일은 Solar Open 2 세션이 생성했다.
- [ ] Codex staging 파일이 `_Upstage`에 그대로 복사되지 않았다.
- [ ] 구현 기록에 모델, harness, 실행 시각과 baseline commit이 있다.
- [ ] 비밀값, 개인 세션 원문과 로컬 사용자 식별자가 추적 파일에 없다.

## B. Skill 구조와 발견

- [ ] `.claude/skills/solar-ralph/SKILL.md`가 존재한다.
- [ ] `.claude/skills/git-checkpoint/SKILL.md`가 존재한다.
- [ ] 두 skill만 `.gitignore` 예외로 추적되고 다른 `.claude` 상태는 무시된다.
- [ ] 새 `claude-upstage` 세션이 두 skill을 발견한다.
- [ ] `/solar-ralph`와 `/git-checkpoint` 직접 호출이 성공한다.
- [ ] `$ralph` 문자열이나 Codex/OMX runtime에 의존하지 않는다.

## C. Preflight

- [ ] detached HEAD, dirty baseline과 upstream 부재가 타이머 전에 실패한다.
- [ ] task spec path와 SHA-256이 state에 기록된다.
- [ ] model이 `solar-open2`, harness가 `claude-upstage`로 기록된다.
- [ ] recorder, heartbeat와 event log가 agent보다 먼저 시작된다.
- [ ] skill discovery smoke test 실패 시 본 실행을 시작하지 않는다.

## D. 자율 작업 사이클

- [ ] 한 번에 ready P0 하나만 active가 된다.
- [ ] 변경 전에 acceptance criteria와 test command를 기록한다.
- [ ] 구현 후 관련 테스트를 실제 실행한다.
- [ ] 테스트 성공 뒤에만 checkpoint commit한다.
- [ ] 성공 후 사용자 입력 없이 다음 ready P0를 선택할 수 있다.
- [ ] 같은 failure signature 두 번 뒤 축소 경로 또는 deferred로 전환한다.

## E. Git 안전성

- [ ] 실행 branch가 `experiment/solar-ralph-*` 패턴이다.
- [ ] approved pathspec만 stage한다.
- [ ] `git add .`, remote Git과 destructive Git 명령을 사용하지 않는다.
- [ ] `_private`, credentials, env와 raw transcript를 거부한다.
- [ ] 사용자 기존 변경이 섞이면 commit 대신 `needs-operator`가 된다.
- [ ] commit hash가 P0 state와 event log에 동일하게 기록된다.
- [ ] 실행 중 원격 branch는 변경되지 않는다.

## F. 기록·복구·종료

- [ ] transcript, events, failure ledger, state와 artifact manifest가 run-id 아래 생성된다.
- [ ] heartbeat가 pane 문자열 길이가 아닌 hash/liveness/event를 사용한다.
- [ ] 정상 tool 실행 중 continuation prompt를 삽입하지 않는다.
- [ ] session 재개 시 state, HEAD와 worktree를 reconciliation한다.
- [ ] 불일치는 쓰기를 중단하고 operator에게 보고한다.
- [ ] deadline 또는 완료 시 handoff가 생성된다.
- [ ] handoff가 미완료, 실패와 사람 확인 항목을 숨기지 않는다.

## G. 단계별 실행 gate

| 단계 | 통과 기준 | 실패 시 |
| --- | --- | --- |
| Static | frontmatter, references, script syntax, 금지 명령 검사 통과 | 구현 수정 |
| Discovery | 새 Solar 세션에서 두 skill 직접 호출 | 3시간 실행 금지 |
| Single cycle | 임시 P0 편집·테스트·local commit·state 일치 | 원인 분류 후 수정 |
| Recovery | 강제 종료 후 중복 없이 재개 | state 계약 수정 |
| Failure injection | dirty/test/commit/permission 오류를 안전하게 처리 | hard fail 경계 수정 |
| 10-minute soak | 두 작업 주기, 로그·heartbeat·종료 정상 | 30분 실행 금지 |
| 30-minute rehearsal | checkpoint, recorder, deadline와 handoff 정상 | 3시간 실행 금지 |
| 3-hour run | 별도 사용자 승인 후에만 시작 | 결과를 실패 근거로 보존 |

## 최종 판정

- [ ] 모든 A~F 항목과 30분 rehearsal이 통과했다.
- [ ] 남은 위험과 실험 조건이 문서화됐다.
- [ ] 사람이 baseline push와 최종 diff 검토 책임을 이해했다.
- [ ] 위 조건이 충족된 경우에만 `ready-for-3-hour-run`으로 표시한다.
