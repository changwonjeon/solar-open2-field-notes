---
title: _Upstage 작업 내역 정리 — 2026년 7월 20일까지
type: Project
description: Solar Open 2 실험 작업 공간의 초기 구성부터 Ralph Loop 스킬과 첫 Git checkpoint 검증까지의 작업 내역
tags: [solar-open2, claude-code, ralph-loop, git-checkpoint, work-log]
timestamp: 2026-07-20T03:58:06+09:00
status: preflight-validation-in-progress
source_workspace: _Upstage
source_branch: fix/solar-ralph-skill-consistency
source_commit: 8204119
---

# `_Upstage` 작업 내역 정리 — 2026년 7월 20일까지

## 요약

2026년 7월 17일부터 20일 03시 58분(KST)까지 `_Upstage`에서는 Solar Open 2 실험용 지식 저장소를 구성하고, Solar Open 2+Claude Code로 기존 Ralphthon 작업을 재현하기 위한 실행 스택을 설계·보강했다.

가장 큰 진전은 두 개의 프로젝트 스킬 `/solar-ralph`와 `/git-checkpoint`를 구현하고, 첫 Git checkpoint의 preflight부터 승인 경로만 포함한 로컬 commit 및 결과 JSON 출력까지 격리 환경에서 검증한 것이다. 반면 recorder·checkpoint monitor·watchdog의 launcher 연결과 장시간 안정성 검증은 끝나지 않았으며, 유효한 3시간 본 실행도 시작하지 않았다. 따라서 7월 20일 마감 상태는 **모델 성능 평가 완료가 아니라 실행 전 검증 진행 중**이다.

## 범위와 근거

- 정리 구간: 2026-07-17 10:19:50 ~ 2026-07-20 03:58:06 KST
- 작업 기준점: `fix/solar-ralph-skill-consistency` 브랜치의 `8204119`
- 주요 근거: `_Upstage/README.md`, `docs/log.md`, `docs/experiments/experiment-log.md`, `docs/experiments/ralphthon/execution-log.md`, Git commit과 worktree 상태
- 보조 근거: 이 저장소의 기존 Ralphthon 계획·복구·스킬 검토 문서

이 문서의 날짜와 순서는 현재 Git commit timestamp를 우선한다. 7월 20일 Git 히스토리 재작성으로 기존 문서에 적힌 과거 commit hash 일부가 현재 값과 달라졌으므로, 아래에는 현재 이력에서 확인되는 hash를 사용한다.

## 날짜별 작업 내역

### 7월 17일 — 작업 공간 구성과 Ralphthon 실행 준비

#### 실험·문서 기반 구성

- LLM-Wiki와 OKF 형식의 작업 공간을 초기화했다(`9e2d081`).
- Solar Open 2 공식 표기, Claude Code·Hermes Agent 연동 가이드, 문서 템플릿과 환경 설정 스크립트를 추가했다(`8d5b2e8`, `8c4a5c2`).
- 공개 README에서 내부용 설명을 덜어내고 실험 목적 중심으로 정리했다(`1070953`).
- 기존 Codex Ralphthon 결과를 Solar Open 2+Claude Code로 재현하는 비교 실험 계획을 문서화했다(`6d62228`).

#### Phase 1~3 산출물 이식과 Question Mode 전환

- Ralph Goal, fixture, 평가 코드, 실행·비교·기록 스크립트와 기존 Codex 산출물을 작업 공간에 반영했다(`963d81a`).
- `claude-upstage`가 일반 Claude CLI의 권한 생략 옵션을 받지 않아, 인자 없는 대화형 실행과 tmux 입력 자동화를 결합한 Question Mode로 전환했다.
- `claude-upstage`가 Solar Open 2로 기동되는 화면과 tmux launch/status/screenshot/quit 흐름을 확인했다.

#### 실행 스크립트 안정화

- `tmux load-buffer`의 stdin 지정 누락과 watchdog 저장소 루트 계산을 수정했다(`bc542a1`).
- `SCRIPT_DIR`/`ROOT` 계산, tmux 프롬프트 주입과 입력 처리를 보강했다(`9772ed9`).
- `exec 2>/dev/tty`를 제거해 nohup 환경에서 디버깅 로그가 동작하도록 했다(`b65b812`).
- 세 실행 스크립트를 pure ASCII로 다시 작성해 zsh의 멀티바이트 파싱 문제를 줄였다(`be938db`).
- 지원되지 않는 `tmux load-buffer -a` 사용을 제거하고 UTF-8 관련 잔여 오류를 수정했다(`0e9f269`).

이 과정에서 로그상 성공인데 실제 loop가 없거나, 존재하지 않는 tmux 세션 때문에 `&&` 체인이 중단되고, 프롬프트 buffer와 root 경로가 잘못되는 문제가 반복됐다. 7월 17일 종료 시점에는 유효한 3시간 세션이 확인되지 않았다.

### 7월 18일 — 별도 Git commit 없음

현재 재작성된 Git 이력에는 7월 18일 timestamp의 commit이 없다. 기존 README의 안정화 표는 일부 commit을 7월 18~19일 작업으로 표시하지만, 현재 Git timestamp상 `bc542a1`부터 `0e9f269`까지는 모두 7월 17일에 기록돼 있다. 작업 날짜를 단정해야 할 때는 원문 로그의 서술과 현재 Git 증거를 함께 표시해야 한다.

### 7월 19일 — 프로젝트 스킬 구현과 추적 정책 정비

- Claude Code 일반 상태는 무시하면서 재현에 필요한 프로젝트 스킬만 Git으로 추적하도록 `.gitignore`를 수정하고 `record-session.sh`에 실행 권한을 부여했다(`93f60fb`).
- README와 실험 로그를 당시 안정화 상태에 맞게 갱신했다(`2fcaf08`).
- `_Upstage/.claude/skills/` 아래에 다음 두 프로젝트 스킬을 추가했다(`3a15443`).
  - `/solar-ralph`: P0 선택, 구현, 테스트, 상태 전이, checkpoint 요청, resume와 handoff 계약
  - `/git-checkpoint`: 승인 경로만 검사·stage하여 안전한 로컬 checkpoint commit을 만드는 계약
- 상태 머신을 `pending → active → tests_passed → passed`로 세분화하고, checkpoint 실패와 운영자 개입 상태를 별도로 다루는 방향을 확립했다.
- recorder, checkpoint monitor와 watchdog은 skill이 아니라 결정론적 runtime 책임으로 분리했다.

### 7월 20일 — 스킬 정합성 보정과 첫 checkpoint 기능 검증

#### 스킬 계약 9개 항목 보정

`4a8d953`에서 다음 정합성 문제를 수정하거나 확인했다.

- `commit-gate.sh`의 Python 호출을 환경 변수 대신 argv 전달로 통일했다.
- Gate 번호를 실제 실행 순서인 0~8에 맞췄다.
- `preflight.sh`의 run-state 전달, 옵션 인자 guard, 분기 우회 여부와 stderr 출력을 점검했다.
- `/solar-ralph resume`이 성공·실패와 관계없이 worktree나 Git 이력을 수정하지 않는 계약을 명확히 했다.
- 같은 실패 signature의 반복 기준을 `once already`로 통일했다.
- P0 상태에 `needs-operator`를 추가하고 state/HEAD/event/checkpoint의 독립 비교와 상태 기록 방식을 문서화했다.

관련 설명은 `385e827`에서 README와 로그에 동기화했다. 같은 작업 중 과거 7개 commit에서 `Co-Authored-By: Claude...` trailer를 제거하기 위해 전체 Git 이력을 재작성했다.

#### Git checkpoint blocker 7건 수정

`5b68b93`에서 첫 checkpoint 성공 경로를 막던 구현 문제를 수정했다.

1. `preflight.sh`가 command substitution과 `set -e` 때문에 Python 실패 코드를 잃는 문제
2. run-state 절대 경로, 저장소 밖 경로, 디렉터리와 symlink component를 허용하던 문제
3. `last_checkpoint_commit: null`의 `__NULL__` sentinel을 복원하지 않아 첫 checkpoint를 후속 checkpoint로 오인하던 문제
4. `commit-gate.sh`가 `--run-state`, `--summary`의 값 존재 여부를 확인하기 전에 `$2`를 읽던 문제
5. 성공 JSON의 `approved_paths`가 heredoc stdin 충돌로 빈 배열이 되던 문제
6. Bash용 `read -ra`가 zsh에서 실패하던 문제
7. commit gate의 run-state 경로 검사도 preflight와 같은 containment 계약을 적용해야 했던 문제

#### 검증 결과

- 대상 스크립트의 `zsh -n`과 `git diff --check`가 통과했다.
- malformed JSON, 필수 schema 누락, 절대·대시 시작·디렉터리·저장소 밖 run-state가 non-zero로 거부되는 것을 확인했다.
- `/tmp`의 격리 Git 저장소와 로컬 bare upstream에서 첫 checkpoint preflight Gate 1~4와 commit gate Gate 0~8을 모두 통과했다.
- 승인 경로 `deliverable.txt`만 `P0-1` 로컬 commit에 포함됐고, 성공 JSON의 `approved_paths`에도 정확히 보존됐다.
- 외부 remote에는 변경을 가하지 않았다.
- ShellCheck는 설치돼 있지 않아 실행하지 못했다.

검증 결과와 문서·ignore 규칙은 03시 58분의 `8204119`에서 최종 동기화했다.

## 7월 20일 종료 상태

| 항목 | 상태 | 근거 또는 해석 |
| --- | --- | --- |
| 프로젝트 지식·실험 구조 | 완료 | README, OKF 문서, guide·reference·experiment 구조 존재 |
| Ralphthon 자료 이식 | 완료 | Goal, fixture, 평가 코드와 실행 스크립트 추적 |
| Solar Open 2+Claude Code 기동 | 부분 확인 | wrapper 기동 화면과 tmux 제어 확인 |
| `/solar-ralph`, `/git-checkpoint` discovery | 확인 | project skill 생성 및 새 세션 help 응답 기록 |
| 스킬 정적 검증 | 통과 | `zsh -n`, `git diff --check` 통과 |
| 첫 Git checkpoint 기능 경로 | 통과 | 격리 저장소에서 승인 파일만 local commit |
| 후속 checkpoint와 재시도 | 미검증 | 다음 검증 항목으로 남음 |
| 실패 후 index cleanup | 미검증 | 오류 경로의 비파괴성 추가 확인 필요 |
| recorder·monitor·watchdog 통합 | 미완료 | launcher 연결과 artifact 생성 검증 필요 |
| 10분 soak / 30분 rehearsal | 미실행 | runtime 통합 후 수행 예정 |
| 3시간 본 실행 | 미실행 | 사용자 승인 전 시작 금지 |
| 모델 간 성능 비교 | 미실행 | 유효한 동일 조건 실행 결과 없음 |

마감 시점의 추적 파일 기준 worktree는 깨끗했다. `data/results/` 아래에는 검증 중 생성한 `manual-test` 계열 임시 결과가 ignore 상태로 남아 있다. 현재 브랜치는 원격 upstream이 설정되지 않은 `fix/solar-ralph-skill-consistency`이며, `main`은 별도 이전 기준점을 가리킨다.

## 남은 작업

1. 후속 checkpoint가 이전 checkpoint, HEAD, state와 올바르게 일치하는지 검증한다.
2. 공백·유니코드 경로, 승인되지 않은 경로, rename/copy, 비밀 패턴과 대용량 파일 거부를 기능 시험한다.
3. commit 실패·테스트 실패 뒤 index와 worktree가 오염되지 않는지 확인한다.
4. recorder, checkpoint monitor와 watchdog을 launcher에 실제 연결한다.
5. heartbeat, deadline, 종료·resume와 artifact manifest를 10분 soak test로 검증한다.
6. 30분 rehearsal을 통과하고 사용자 승인을 받은 뒤에만 3시간 본 실행을 시작한다.
7. 동일한 task, 시간, 권한과 산식으로 GPT+Codex, Claude+Claude Code, Solar Open 2+Claude Code를 비교한다.

## 기록상 주의사항

- `docs/log.md`에는 7월 19일 항목이 중복돼 있다.
- 기존 문서에는 Git 히스토리 재작성 전 hash가 일부 남아 있다.
- README의 날짜별 안정화 표와 현재 Git timestamp가 일부 일치하지 않는다.
- 7월 17일의 실행 실패는 모델이 정상적인 작업 loop에 진입하기 전 발생했으므로 Solar Open 2의 과제 수행 성능 실패로 계산하면 안 된다.
- 계획 문서에 적힌 기존 Codex 기준 수치는 이 기록 저장소에서 독립 검증하지 않았으므로 원문 주장으로 유지한다.

## 관련 문서

- [Ralph Loop 스킬 설계와 실행 전 검증](ralph-skill-stack-validation.md)
- [랄프톤 Phase 4 재개와 Question Mode 전환](ralphthon-phase4-recovery.md)
- [랄프톤 재현 비교 실험 — 계획 수립](ralphthon-reproduction-planning.md)
- [`claude-upstage`의 권한 생략 옵션 미지원](../observations/claude-upstage-permission-flag.md)
- [저장소 색인](../../index.md)
- [관찰 로그](../../log.md)
