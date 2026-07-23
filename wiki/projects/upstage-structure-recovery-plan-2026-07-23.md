---
title: _Upstage 구조 복구 실행 계획
type: Work Plan
description: 누락된 Ralphthon 원본을 복구하고 tasks 중심 구조, OKF 문서와 링크를 안전하게 정합화하기 위한 단계별 실행 계획
tags: [solar-open2, migration, recovery, llm-wiki, okf, tasks]
timestamp: 2026-07-23T01:09:00+09:00
status: in-progress
executor: Solar Open 2 with Claude Code CLI
reviewer: Codex
source_workspace: _Upstage
source_branch: main
source_head_at_planning: 6e8c5d10a4eca26b52c33462ca90ee94baea60ca
---

# `_Upstage` 구조 복구 실행 계획

## 현재 상태

2026년 7월 23일 01:09 KST 기준, 사용자가 Solar Open 2에 아래 단계별 프롬프트를 전달해 `_Upstage` 구조 복구 작업을 진행하고 있다. 이 문서는 **완료 보고가 아니라 실행 중인 계획과 안전 계약**이다.

- 이전 구조 개편 기준: `7024b1b`
- 구조 개편 전 기준: `7024b1b^`
- 계획 수립 당시 HEAD: `6e8c5d1`
- 상속된 누락 후보: `7024b1b^`의 Ralphthon Source path 42개
- 당시 HEAD 대비 추가 미보존 후보: 삭제 예정 47개 중 36개
- 위 수치는 감사 시점 관찰값이며, 실제 복구에서는 경로 수와 고유 blob 수를 다시 계산한다.
- 42개와 36개를 무조건 78개의 고유 파일로 합산하지 않는다. 동일 blob, alias와 과거 경로 중복을 분리한다.

## 목표

1. 과거 Ralphthon Source의 고유 blob을 100% 보존한다.
2. 현재 HEAD에서 삭제되는 tracked 자산의 canonical 목적지를 확정한다.
3. 실험을 `tasks/` 단위의 Source / Wiki / Output 계층으로 정리한다.
4. 원본과 현재 실행 코드를 구분하고 canonical copy를 하나만 둔다.
5. README, AGENTS, OKF index/log와 실제 filesystem을 일치시킨다.
6. 실제 broken internal link를 0개로 만든다.
7. 모든 검증 gate 통과 전에는 stage, commit 또는 push하지 않는다.

## 보호 대상

복구 전후 hash를 비교하며 다음 자료를 보호한다.

- `tasks/02-meeting-minutes/source/original/`의 원본 9개
- `tasks/02-meeting-minutes/docs/meeting-minutes/`의 회의록
- `tasks/02-meeting-minutes/output/`의 산출물
- 기존 `_inbox/` 자료
- 기존 미추적 파일 전체
- `_private/`
- `src/data/results/`
- `.claude/settings.local.json`
- credential, secret와 ignored result 파일

## 공통 금지 사항

- `git reset --hard`, `git clean`, broad checkout
- 승인되지 않은 파일 삭제·이동·덮어쓰기
- `_private/` 또는 비밀값의 열람·출력
- 기존 미추적 파일의 일괄 staging
- `git add -A`를 이용한 무차별 staging
- 자동 commit과 remote push
- 충돌이나 분류가 애매한 상태에서의 임의 진행

## 1단계 — Read-only 상태 동결과 migration manifest

### 목적

파일을 전혀 수정하지 않고 복구 대상과 최종 목적지를 확정한다.

### 조사 범위

- `7024b1b^`의 다음 tree와 blob:
  - `docs/experiments/ralphthon/fixtures/`
  - `docs/experiments/ralphthon/src/`
  - `docs/experiments/ralphthon/tests/`
- 계획 당시 HEAD와 현재 worktree 간 삭제 예정 파일
- `.codex/.codex`, `.omx/.omx` 중복 nesting
- Ralphthon shell script 중복
- 보호 대상 미추적 파일의 path, size, blob hash

### 산출물

다음 열을 가진 전체 migration 표를 Solar Open 2가 응답으로 제시한다.

| 기준 commit | 기존 경로 | blob ID | 현재 위치 | 최종 제안 위치 | 작업 | 근거 |
| --- | --- | --- | --- | --- | --- | --- |

함께 보고할 수치:

1. 과거 path 수
2. 고유 blob 수
3. 현재 보존된 고유 blob 수
4. 복구가 필요한 고유 blob 수
5. canonical copy로 통합할 alias 수

### 중단 조건

- 누락 blob에 최종 목적지가 없음
- 보호 대상 manifest가 불완전함
- 경로 충돌을 발견함
- 작업 도중 파일을 수정함

1단계 결과는 사용자 또는 Codex가 검토하고 승인하기 전까지 실행 단계로 넘어가지 않는다.

## 2단계 — Ralphthon 원본 복구

### 범위

- `7024b1b^`에서 누락된 fixtures
- Python package
- tests/evaluators
- 현재 HEAD에서 삭제 예정인 `.codex`, `.omx`
- 기존 Ralph 프로젝트 관리 문서

### 기본 canonical 위치

```text
tasks/01-ralphthon/source/codex-original/
├── fixtures/
├── package/
├── tests/
├── .codex/
├── .omx/
├── project-docs/
└── manifest.md
```

### 실행 계약

- 승인된 매핑에 있는 경로만 생성한다.
- Git object에서 원본을 복구한다.
- 원본 내용은 수정하지 않는다.
- 대상이 이미 존재하면 먼저 blob hash를 비교한다.
- 같은 경로에 다른 내용이 있으면 중단한다.
- 아직 문서·링크·README는 수정하지 않는다.
- stage, commit, push하지 않는다.

### 완료 gate

- 과거 Source 고유 blob 보존율 100%
- HEAD 삭제 자산 고유 blob 보존율 100%
- 복구 전후 blob ID 일치
- 경로 충돌 0개
- 보호 자료 hash 무변경
- 승인 범위 밖 변경 0개

하나라도 실패하면 3단계로 진행하지 않는다.

## 3단계 — `tasks/` 구조 정규화

### 목표 구조

```text
tasks/
├── 01-ralphthon/
│   ├── README.md
│   ├── AGENTS.md
│   ├── CLAUDE.md
│   ├── source/
│   │   ├── codex-original/
│   │   └── solar-adaptation/
│   ├── data/
│   ├── docs/ralphthon/
│   └── output/
└── 02-meeting-minutes/
    ├── README.md
    ├── AGENTS.md
    ├── CLAUDE.md
    ├── source/original/
    ├── data/
    ├── docs/meeting-minutes/
    └── output/
```

### canonical 규칙

- Codex 원본: `tasks/01-ralphthon/source/codex-original/`
- 현재 Solar 적응 실행 코드: 루트 `src/scripts/ralphthon/`
- task `docs/`에는 실행 가능한 shell script를 두지 않는다.
- 동일 원본을 여러 위치에 복제하지 않는다.
- `.codex/.codex`, `.omx/.omx` 중복 nesting을 만들지 않는다.
- 중복 제거 전 canonical copy 존재, blob 일치와 참조 경로를 확인한다.

### task-local schema

각 task의 `AGENTS.md`에 다음을 명시한다.

- 원본 Source는 읽기 전용
- `docs/`는 OKF Wiki
- `output/`은 생성 산출물
- 원본을 수정하거나 output으로 덮어쓰지 않음
- ingest 시 index/log 갱신
- 대량 이동은 사용자 승인 필요

각 `CLAUDE.md`에는 `@AGENTS.md` 한 줄만 둔다.

## 4단계 — 문서·OKF·링크 정합화

### 수정 대상

- 루트 `README.md`, `AGENTS.md`
- `docs/AGENTS.md`, `docs/index.md`, `docs/log.md`, `docs/experiment-log.md`
- task별 README/index/AGENTS/CLAUDE
- Wiki 하위 `index.md`
- 실제 구조 변경으로 깨진 상대 링크
- 회의록의 `input_sources`

### 핵심 수정 사항

- README와 AGENTS에서 옛 `projects/ralph-loop/`, `docs/experiments/...` 구조를 현재 `tasks/` 구조로 갱신한다.
- `docs/index.md`를 과거 Experiments index가 아닌 프로젝트 공통 Docs bundle index로 복원한다.
- task 링크는 `docs/` 기준 `../tasks/...`를 사용한다.
- 회의록 provenance는 실제 `tasks/02-meeting-minutes/source/original/`을 가리킨다.
- `docs/log.md`에 구조 개편, 누락 발견, 복구 수치, canonical 경로와 검증 결과를 기록한다.
- 역사 기록의 당시 경로와 현재 경로를 구분한다.
- 중복된 7월 19일 로그는 정보 손실 없이 통합한다.

### OKF 정책

- Wiki 콘텐츠에는 parse 가능한 YAML frontmatter와 비어 있지 않은 `type`이 필요하다.
- `AGENTS.md`, `CLAUDE.md`는 Schema이므로 OKF 콘텐츠 검사에서 제외한다고 규칙에 명시한다.
- `CLAUDE.md`에는 frontmatter를 넣지 않는다.

### 링크 정책

링크를 다음으로 분류한 뒤 실제 broken link만 수정한다.

1. 실제 broken link
2. template placeholder
3. planned document
4. archive의 역사적 경로
5. 외부 URL

작성되지 않은 문서를 만족시키기 위한 빈 파일은 만들지 않는다.

## 5단계 — Read-only 최종 검증

최종 단계에서는 파일을 수정하지 않고 다음 gate를 평가한다.

| Gate | 합격 조건 |
| --- | --- |
| 과거 Source 보존 | 고유 blob 100% |
| HEAD 자산 보존 | 고유 blob 100% |
| 의도하지 않은 삭제 | 0개 |
| canonical 중복 | 0개 |
| 보호 자료 | 사전·사후 hash 일치 |
| Wiki OKF 위반 | 0개 |
| 실제 broken internal link | 0개 |
| 문서-트리 불일치 | 0개 |
| task 진입점 | 모두 존재 |
| shell 정적 검사 | `zsh -n` 통과 |
| Python/test | PASS 또는 근거 있는 NOT RUN |
| diff 검사 | `git diff --check` 통과 |
| staging 안전성 | 보호·ignored 파일 제외 가능 |

하나라도 FAIL이면 commit 준비로 넘어가지 않는다.

## 계획된 commit 분할

모든 gate가 PASS하고 사용자가 승인한 경우에만 다음 단위로 stage와 commit을 제안한다.

1. `restore: recover immutable Ralphthon source archive`
2. `refactor: organize experiments under task workspaces`
3. `docs: align OKF wiki and links with task structure`
4. `docs: record migration manifest and validation`

각 commit은 명시적 pathspec을 사용하며, 자동 push하지 않는다.

## 진행 상태

| 단계 | 상태 | 완료 증거 |
| --- | --- | --- |
| 1. Read-only manifest | 진행 중 | Solar Open 2 결과 검토 대기 |
| 2. 원본 복구 | 대기 | 보존율 100% 필요 |
| 3. 구조 정규화 | 대기 | 2단계 gate 필요 |
| 4. 문서·OKF·링크 | 대기 | 구조 확정 필요 |
| 5. 최종 검증 | 대기 | 전체 gate report 필요 |
| `_Upstage` commit/push | 금지 | 사용자 최종 승인 필요 |

진행 상태는 Solar Open 2의 실제 결과를 검증한 뒤에만 갱신한다. 모델의 완료 선언만으로 PASS 처리하지 않는다.

## 관련 문서

- [`tasks/` 상위 구조 재편 후속 감사](upstage-task-restructure-review-2026-07-23.md)
- [최초 LLM-Wiki·OKF 구조 개편 감사](upstage-okf-restructure-review-2026-07-22.md)
- [저장소 색인](../../index.md)
- [관찰 로그](../../log.md)
