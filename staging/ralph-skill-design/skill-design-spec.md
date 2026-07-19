---
title: Solar Ralph Loop 스킬 구현 명세
type: specification
description: Solar Open 2가 구현할 두 스킬과 runtime의 결정 완료 요구사항
tags: [solar-open2, skill-spec, git, ralph-loop]
timestamp: 2026-07-19
producer: Codex
---

# Solar Ralph Loop 스킬 구현 명세

## 설치 결과

Solar Open 2는 `_Upstage/.claude/skills/` 아래에 다음 구조를 만든다.

```text
.claude/skills/
├── solar-ralph/
│   ├── SKILL.md
│   └── references/
│       ├── runtime-contract.md
│       ├── state-contract.md
│       └── failure-and-handoff.md
└── git-checkpoint/
    ├── SKILL.md
    └── scripts/
        └── preflight.sh
```

`SKILL.md`는 500줄보다 짧게 유지하고 상세 계약은 한 단계 reference로만 연결한다. project `.gitignore`의 기존 `/.claude/` 규칙은 다음 선택적 규칙으로 교체한다. session·plan·credential 파일은 계속 무시한다.

```gitignore
/.claude/*
!/.claude/skills/
/.claude/skills/*
!/.claude/skills/solar-ralph/
!/.claude/skills/solar-ralph/**
!/.claude/skills/git-checkpoint/
!/.claude/skills/git-checkpoint/**
```

## `/solar-ralph` 인터페이스

수동 호출만 허용한다.

```text
/solar-ralph start <task-spec-path> <deadline-seconds>
/solar-ralph step
/solar-ralph resume
/solar-ralph finalize <reason>
```

### Start

1. repository root, branch와 clean baseline을 확인한다.
2. task spec을 읽고 변경하지 않는다.
3. P0별 `pending|active|passed|failed|deferred` 상태를 만든다.
4. baseline HEAD와 monotonic 시작 시각을 기록한다.
5. 첫 ready P0를 선택해 `step`을 수행한다.

### Step

1. active P0가 있으면 먼저 재개하고 없으면 ready P0 하나를 선택한다.
2. acceptance criteria와 실행할 검증 명령을 state에 먼저 기록한다.
3. 최소 변경으로 구현한다.
4. 정적 검사와 관련 테스트를 실행한다.
5. 성공하면 `/git-checkpoint`를 호출하고 P0를 passed로 바꾼다.
6. 실패하면 signature를 정규화해 ledger에 추가한다.
7. 같은 signature가 두 번이면 원 접근을 중단하고 축소 경로 또는 deferred로 전환한다.
8. deadline 여유가 있으면 다음 step을 계속한다.

### Resume

- state의 last commit이 현재 HEAD의 ancestor인지 확인한다.
- worktree 변경과 active P0가 일치하면 검증부터 다시 시작한다.
- 불일치하거나 출처 불명 변경이 있으면 쓰기를 중단하고 `needs-operator` 상태를 남긴다.
- transcript 기억만 믿지 않고 task spec, state, ledger와 Git을 다시 읽는다.

### Finalize

- 신규 구현을 중단한다.
- 마지막 관련 테스트와 artifact manifest 검사를 실행한다.
- P0별 상태, commit, 변경 파일, 테스트, 실패, 미확인과 수동 후속 작업을 handoff에 기록한다.
- 미완료를 완료로 표현하지 않는다.
- commit되지 않은 변경은 별도 목록으로 남기며 자동 push하지 않는다.

## 상태 계약

runtime 결과 디렉터리에는 다음 파일을 둔다.

```text
data/results/ralpthon/solar/<run-id>/
├── run-state.json
├── failure-ledger.jsonl
├── events.jsonl
├── transcript.log
├── artifact-manifest.json
└── HANDOFF.md
```

`run-state.json` 필수 필드:

- `schema_version`
- `run_id`, `model`, `harness`
- `task_spec_path`, `task_spec_sha256`
- `baseline_commit`, `branch`
- `started_at`, `deadline_at`, `status`
- `active_p0`, `p0_items[]`
- `last_checkpoint_commit`
- `operator_interventions`

각 P0에는 id, status, attempts, acceptance criteria, test commands, last result, commit과 timestamps를 둔다. 비밀값과 prompt 전문은 JSON에 넣지 않는다.

## `/git-checkpoint` 인터페이스

```text
/git-checkpoint <p0-id> <approved-path>...
```

다음 gate를 모두 통과할 때만 local commit한다.

1. detached HEAD가 아니고 `experiment/solar-ralph-*` branch다.
2. baseline에 upstream이 설정돼 있고 실행 시작 뒤 fetch/pull은 하지 않는다.
3. approved path 밖의 변경은 stage하지 않는다.
4. `_private`, credential, `.env`, session transcript 원문과 비밀 패턴을 제외한다.
5. 10 MiB 이상 새 파일과 symlink를 거부한다.
6. state에 기록된 test command가 성공했다.
7. `git diff --cached --check`가 성공한다.
8. commit message는 `checkpoint(<p0-id>): <summary>` 형식이다.
9. commit 후 hash와 tree status를 JSON으로 반환한다.

금지 명령은 `push`, `pull`, `fetch`, `reset --hard`, `checkout --`, `clean`, `rebase`, `commit --amend`, force 옵션과 broad `git add .`이다.

## Runtime 요구사항

- launcher는 skill 발견 smoke test 뒤에만 deadline을 시작한다.
- recorder, heartbeat와 state monitor를 agent session보다 먼저 시작한다.
- pane length 대신 content hash, process liveness와 event timestamp로 활동을 판단한다.
- 활동 중에는 continuation prompt를 보내지 않는다.
- permission은 승인된 tool pattern으로 설정하고 화면 keyword에 `y`를 보내지 않는다.
- deadline 도달 시 `/solar-ralph finalize deadline`을 요청하고 grace period 뒤 process를 종료한다.
- 어느 component가 종료돼도 종료 원인과 exit code를 events에 남긴다.

## 사용자 사전 Git 준비

3시간 타이머 전 사람이 다음을 완료한다.

1. 공개 가능한 변경만 baseline commit으로 만든다.
2. `experiment/solar-ralph-YYYYMMDD-HHMM` branch를 만든다.
3. `git push -u origin <branch>`로 baseline과 upstream을 확인한다.
4. working tree가 완전히 clean인지 확인한다.
5. 이후 runtime에는 remote Git 명령을 허용하지 않는다.
