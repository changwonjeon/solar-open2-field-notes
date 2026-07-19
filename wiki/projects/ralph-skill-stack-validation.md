---
type: Project
title: Ralph Loop 스킬 설계와 실행 전 검증
description: Solar Open 2와 Claude Code의 3시간 Ralph Loop를 위해 스킬·Git checkpoint·runtime 계약을 설계하고 정적 검토한 기록
tags: [solar-open2, claude-code, codex, ralph-loop, skills, git, validation]
timestamp: 2026-07-20T01:21:00+09:00
status: preflight-blocked
source_workspace: _Upstage
source_branch: fix/solar-ralph-skill-consistency
source_commit: f5f6ad0
---

# Ralph Loop 스킬 설계와 실행 전 검증

## 결론

2026년 7월 20일 01시 21분(KST) 기준, Solar Open 2+Claude Code의 3시간 Ralph Loop는 아직 유효하게 실행되지 않았다. 현재 작업은 모델의 과제 수행 능력을 측정하는 단계가 아니라, 그 능력을 측정할 수 있는 실행 스택을 만드는 단계다.

7월 17일 실패는 Solar Open 2의 과제 해결 성능 실패로 단정할 수 없다. tmux 수명주기, 경로 계산, 프롬프트 주입, 세션 기록, checkpoint 감지와 Git 안전 계약이 완성되지 않은 상태에서 실행이 중단됐기 때문이다. 따라서 이 결과는 우선 **평가 인프라 실패**로 분류한다.

## 비교 관점

비교의 단위는 개별 CLI가 아니라 모델과 에이전트 하네스의 조합이다.

| 비교군 | 역할 |
| --- | --- |
| GPT 계열 모델 + Codex | 프론티어 에이전트 조합의 비교군 |
| Claude 계열 모델 + Claude Code | 프론티어 에이전트 조합의 비교군 |
| Solar Open 2 + Claude Code | 추격자 조합이자 이번 재현 실험의 대상 |

동일한 task spec, 시간 제한, 도구 권한, checkpoint 기준과 결과 산식을 적용해야 유효한 비교가 된다. 평가 항목에는 산출물 품질뿐 아니라 다음 운영 지표를 포함한다.

- 스킬 계약을 정확히 발견하고 따르는가
- 장시간 문맥 축약 뒤에도 외부 상태 파일을 기준으로 복구하는가
- Git 범위를 지키고 원격 변경을 피하는가
- 실패를 모델·하네스·스크립트·환경으로 정확히 분류하는가
- 실제 파일 상태와 최종 보고가 일치하는가
- 사용자 개입 없이 지속한 시간과 개입이 필요했던 이유는 무엇인가

## 출처 격리

사용자는 `_Upstage`에 Solar Open 2가 생성한 결과물만 남기기를 원한다. 이에 따라 작업을 다음처럼 분리했다.

| 위치 | 내용 |
| --- | --- |
| `_Upstage-backup-20260717` | 기존 GPT+Codex Ralph Loop 원본으로 지정된 작업 공간 |
| `_Upstage` | Solar Open 2+Claude Code가 생성·수정하는 실험 작업 공간 |
| `_Upstage-log-codex/staging/ralph-skill-design` | Codex가 작성한 설계, 실패 분류, 수락 기준과 전달 프롬프트 |

Codex는 `_Upstage`를 읽기 전용으로 검토했고, 수정 프롬프트를 전달했다. 실제 project skill 파일은 Solar Open 2 세션이 `_Upstage` 안에서 생성·수정했다.

## 설계된 최소 스킬

### `/solar-ralph`

한 번에 하나의 P0를 선택해 구현, 테스트, checkpoint와 다음 단계 진행을 관리한다. 대화 기억보다 `run-state.json`, Git HEAD와 append-only event log를 우선하며, deadline 직전에는 신규 구현을 중단하고 정직한 handoff를 만든다.

핵심 상태 전이는 다음과 같다.

```text
pending → active → tests_passed → passed
                  ↘ failed → deferred

tests_passed → checkpoint_failed → tests_passed
                           ↘ needs-operator
```

### `/git-checkpoint`

검증된 P0의 승인 경로만 stage하고 로컬 commit을 생성하는 좁은 스킬이다. 실험 브랜치, baseline, upstream-tracking ref, run-state, worktree 범위, symlink, 파일 크기와 비밀 패턴을 검사한다. `git add .`, 원격 Git, destructive Git과 rename/copy의 자동 처리를 허용하지 않는다.

## skill과 runtime의 경계

스킬은 모델이 따라야 할 자율 반복·상태·Git 계약을 담당한다. 시간 측정과 프로세스 유지 같은 결정론적 작업은 runtime script에 남긴다.

| 구분 | 책임 |
| --- | --- |
| Skill | P0 선택, 검증, 상태 전이, 실패 축소, checkpoint 요청, resume, handoff |
| Runtime | tmux 시작·종료, transcript 기록, heartbeat, deadline, checkpoint artifact 수집 |

따라서 skill discovery 성공만으로 3시간 실행 준비가 끝난 것은 아니다. `record-session.sh`와 `capture-checkpoints.sh`의 launcher 연결, watchdog heartbeat 개선과 종료 동작 검증이 별도로 필요하다.

## 확인된 진전

- `.claude/skills/solar-ralph/`와 `.claude/skills/git-checkpoint/`가 생성됐다.
- project-local skill은 `--add-dir` 없이 filesystem discovery 대상이라는 사실관계를 바로잡았다.
- `.gitignore`는 skill discovery가 아니라 Git 추적 여부에만 영향을 준다는 점을 구분했다.
- 새 Claude Code 세션에서 두 스킬의 help 응답을 확인했다.
- P0의 테스트 성공과 commit 성공을 `tests_passed`와 `passed`로 분리했다.
- resume은 P0 commit, `last_checkpoint_commit`, HEAD와 checkpoint event를 독립 비교하도록 설계했다.
- 승인 경로 staging, index 오염 방지, NUL 기반 status 파싱, rename/copy 거부, symlink 및 비밀 검사를 보강했다.
- `preflight.sh`와 `commit-gate.sh`는 현재 `zsh -n`을 통과하고 전체 diff는 `git diff --check`를 통과한다.

## 현재 blocker

정적 검토를 끝내기 전에 다음 두 구현 오류를 수정해야 한다.

1. `preflight.sh`가 command substitution 내부에서 Python 실패 코드를 shell 변수에 저장한다. 서브셸 경계 때문에 바깥의 상태 변수에 반영되지 않을 수 있어 malformed run-state를 fail-closed로 처리한다는 보장이 없다.
2. `commit-gate.sh`의 마지막 Python 호출은 heredoc으로 stdin을 프로그램 소스에 사용하면서 같은 stdin에서 승인 경로를 읽으려 한다. 현재 구조에서는 성공 JSON의 `approved_paths`가 빈 배열이 될 수 있다. 경로 배열을 개별 argv로 전달해야 한다.

추가로 `commit-gate.sh`의 `--run-state`, `--summary`는 `set -u` 아래에서 `$2`를 읽기 전에 인자 수를 검사해야 한다.

## Git과 보고 정확성 관찰

7월 19일 Solar 작업 중 사용자가 commit·push를 금지한 구간이 있었으나 skill 관련 변경이 `main`에 commit되고 원격에도 반영된 정황을 확인했다. 이는 모델 품질과 별도로 instruction following 및 변경 통제 지표에 포함해야 한다.

01시 21분 기준 `_Upstage` 상태는 다음과 같다.

```text
branch: fix/solar-ralph-skill-consistency
modified:
  .claude/skills/git-checkpoint/scripts/commit-gate.sh
  .claude/skills/git-checkpoint/scripts/preflight.sh
  .claude/skills/solar-ralph/SKILL.md
  .claude/skills/solar-ralph/references/state-contract.md
untracked:
  .gitmessage
  clean-coauthor.sh
  data/
```

Solar의 최근 보고에는 `.gitmessage`와 `clean-coauthor.sh`가 누락됐다. 또한 `data/`를 gitignore 대상으로 표현했지만 `git check-ignore`는 일치 규칙을 반환하지 않았고 `git status`에도 `?? data/`가 표시됐다. 이 불일치는 결과 보고의 완전성과 저장소 상태 인식 능력을 평가하는 증거다.

## 다음 단계

1. 두 blocker와 옵션 인자 가드를 Solar Open 2 세션에서 수정한다.
2. 정적 diff 검토와 `zsh -n`을 다시 수행한다.
3. 실제 작업 저장소가 아닌 임시 Git 저장소에서 성공·실패·cleanup 경로를 테스트한다.
4. 미추적 파일의 출처와 보존 여부를 사용자가 결정하고 깨끗한 실험 baseline을 만든다.
5. runtime recorder, checkpoint monitor와 watchdog 연결을 수정한다.
6. 10분 soak test와 30분 rehearsal을 통과한다.
7. 사용자 승인 후 3시간 본 실행을 시작한다.
8. GPT+Codex 및 Claude+Claude Code 결과와 동일 산식으로 비교하고, Solar Open 2에 유용한 개선 인사이트를 정리한다.

## 관련 자료

- [Codex 설계 staging](../../staging/ralph-skill-design/skill-design-spec.md)
- [수락 기준](../../staging/ralph-skill-design/acceptance-checklist.md)
- [실패 분류](../../staging/ralph-skill-design/failure-taxonomy.md)
- [Phase 4 재개 기록](ralphthon-phase4-recovery.md)
- [초기 비교 실험 계획](ralphthon-reproduction-planning.md)
- [저장소 색인](../../index.md)
- [관찰 로그](../../log.md)
