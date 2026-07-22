---
type: Log
title: Solar Open 2 Field Notes Log
description: 작업폴더 관찰과 지식 문서 갱신의 시간순 기록
tags: [solar-open2, log]
timestamp: 2026-07-17T11:50:35+09:00
---

# Solar Open 2 Field Notes Log

## [2026-07-23 01:09 KST] handoff | Solar Open 2 단계별 구조 복구 작업 시작

- 사용자가 후속 감사 결과를 바탕으로 Solar Open 2에 단계별 복구 프롬프트를 전달하고 실제 정리 작업을 시작했다.
- 복구 절차를 ① read-only 상태 동결·migration manifest 작성, ② Ralphthon 원본 복구, ③ `tasks/` 구조 정규화, ④ README·AGENTS·OKF·링크 정합화, ⑤ read-only 최종 검증의 다섯 단계로 분리했다.
- 첫 번째 안전 기준은 `7024b1b^`의 Ralphthon Source blob 42개와 현재 HEAD에서 삭제 예정인 미보존 자산 36개를 직접 재계산하고, 경로 수와 고유 blob 수를 구분해 최종 목적지를 제시하는 것이다.
- `tasks/02-meeting-minutes/source/original/`의 원본 9개, 회의록 문서, output, 기존 미추적 파일과 `_private/`를 보호 대상으로 지정했다.
- 원본 blob 보존율 100%와 보호 자료 hash 일치를 통과하기 전에는 다음 단계로 진행하지 않고, 모든 검증 gate가 통과하기 전에는 `git add`, commit 또는 push를 수행하지 않도록 했다.
- 현재 상태는 **Solar Open 2 복구 작업 진행 중**이며, migration 결과와 최종 gate는 아직 검증되지 않았다. 작업 완료나 구조 품질 개선으로 기록하지 않는다.
- 복구 계획 전체를 별도 `Work Plan` 문서로 기록하고 상태를 `in-progress`로 표시했다.
- 실행 계획: [`_Upstage` 구조 복구 실행 계획](wiki/projects/upstage-structure-recovery-plan-2026-07-23.md)
- 관련 문서: [`_Upstage` `tasks/` 상위 구조 재편 후속 감사](wiki/projects/upstage-task-restructure-review-2026-07-23.md)

## [2026-07-23] review | `tasks/` 상위 구조 재편 후속 감사

- 완료됐다고 전달받은 `_Upstage`의 `tasks/01-ralpthon`, `tasks/02-meeting-minutes` 구조를 현재 HEAD `6e8c5d1`과 worktree 기준으로 읽기 전용 재검토했다.
- task별 `source/data/docs/output` 분리는 이전보다 명확하고, 회의록 원본 9개와 OKF 회의록 2개 및 별도 output이 존재함을 확인했다.
- `7024b1b^`의 Ralphthon fixtures·Python package·tests 42개는 현재 filesystem에서 동일 blob이 0개로, 이전 누락이 복구되지 않았다.
- 이번 worktree의 tracked 삭제 47개 중 11개만 동일 blob으로 보존됐고 `.codex`, `.omx`, project 문서 등 36개는 새 위치에서 찾을 수 없었다.
- 새 task 파일 전체가 미추적이고 shell script가 `docs/`와 `src/`에 중복돼 현재 상태의 commit을 차단 판정했다.
- Wiki 후보 40개 중 frontmatter 문제 13개, 잠재 broken link 44개와 README·AGENTS·index·log의 새 구조 불일치를 확인했다.
- `zsh -n`과 `git diff --check`는 통과했으나 원본 보존·OKF·링크·문서 정합성 gate 실패로 종합 42/100을 부여했다.
- 관련 문서: [`_Upstage` `tasks/` 상위 구조 재편 후속 감사](wiki/projects/upstage-task-restructure-review-2026-07-23.md)

## [2026-07-22] review | `_Upstage` LLM-Wiki·OKF 구조 개편 감사

- `_Upstage`의 구조 개편 전 `7024b1b^`, 개편 commit `7024b1b`, 현재 `6e8c5d1`의 파일 트리와 diff를 비교했다.
- 사용자가 제공한 `wiki-organize` skill 원문을 읽고 Source/Wiki/Schema, AGENTS/CLAUDE, README/inbox, OKF, Ingest/Query/Lint, migration 승인·보존 계약을 평가 기준으로 삼았다.
- 세 계층 분리, `AGENTS.md` 단일 규칙, `CLAUDE.md` import, `general-notes/`, `_inbox/` 도입은 적절한 적용으로 평가했다.
- 반면 fixture 18개, Python 구현 7개, test/evaluator 16개 이상이 목적지 없이 삭제됐고, `.codex/.codex`·`.omx/.omx` 중복 nesting과 원본 script 중복이 생긴 사실을 확인했다.
- commit 설명의 “frontmatter 10개 추가”와 달리 실제 diff에서 확인되는 추가는 1개이며, 현재 `docs/` Markdown 14개가 자체 frontmatter 규칙을 충족하지 못한다.
- README·AGENTS·실제 트리의 경로 불일치, 구조 변경 로그 누락과 47개 잠재적 broken link를 기록했다. 예제 placeholder가 섞인 링크 수치는 실제 결함과 구분했다.
- Solar Open 2의 skill 적용 산출물을 48/100으로 평가했다. 모델 실행 귀속은 사용자 설명을 따르되 Git만으로 skill invocation을 독립 검증할 수 없다는 한계를 명시했다.
- `_Upstage`는 수정하지 않고 보존 중심의 대안 트리, 이동 매핑과 완료 gate를 문서로만 제안했다.
- 관련 문서: [`_Upstage` LLM-Wiki·OKF 구조 개편 전후 및 Solar Open 2 평가](wiki/projects/upstage-okf-restructure-review-2026-07-22.md)

## [2026-07-21] synthesize | 7월 20일까지의 `_Upstage` 작업 내역 종합

- 실제 `_Upstage`의 README, 변경 로그, 실험 로그, Ralphthon 실행 로그와 현재 Git 이력을 대조했다.
- 정리 기준점을 2026년 7월 20일 03시 58분(KST), `fix/solar-ralph-skill-consistency` 브랜치의 `8204119`로 확정했다.
- 작업 공간 초기화, Ralphthon 자료 이식, Question Mode 전환, 실행 스크립트 안정화, 프로젝트 스킬 구현, 9개 정합성 보정과 Git checkpoint blocker 7건 수정·검증을 날짜별로 종합했다.
- 첫 checkpoint 기능 경로는 통과했지만 runtime 통합, 후속·실패 경로, soak/rehearsal과 3시간 본 실행은 남았음을 구분했다.
- 히스토리 재작성 전 hash, 중복 로그와 문서 날짜·Git timestamp 불일치를 기록상 주의사항으로 남겼다.
- 관련 문서: [`_Upstage` 작업 내역 정리 — 2026년 7월 20일까지](wiki/projects/upstage-work-summary-through-2026-07-20.md)

## [2026-07-20 01:21 KST] review | Ralph Loop 스킬 정합성 검토와 남은 blocker

- 비교 기준을 Codex 단독이 아니라 GPT+Codex, Claude+Claude Code 등 프론티어 에이전트 조합과 Solar Open 2+Claude Code의 비교로 명확히 했다.
- 원본 Codex Ralph Loop 작업은 `_Upstage-backup-20260717`, Solar Open 2 작업은 `_Upstage`, Codex 설계·검토 산출물은 이 저장소의 `staging/ralph-skill-design/`으로 분리했다.
- Solar Open 2가 `_Upstage/.claude/skills/`에 `/solar-ralph`와 `/git-checkpoint`를 생성했고, 새 Claude Code 세션에서 두 스킬의 discovery와 help 응답을 확인했다.
- 상태 머신을 `pending → active → tests_passed → passed`로 분리하고 `checkpoint_failed`, `needs-operator`, resume의 state/HEAD/event 일치 계약과 local-only Git checkpoint 게이트를 설계했다.
- 반복 정적 검토에서 shell 문법, NUL porcelain 처리, rename/copy, symlink, 승인 경로, secret 검사, upstream 기준선과 atomic state write 문제를 수정했다.
- 최신 diff에는 아직 두 blocker가 남았다. `preflight.sh`의 Python 실패 코드가 command substitution 밖으로 보존되지 않을 수 있고, `commit-gate.sh`의 heredoc이 stdin을 소비해 성공 JSON의 `approved_paths`가 빈 배열이 될 수 있다.
- `zsh -n`과 `git diff --check`는 통과했지만 실제 preflight와 commit gate의 기능 검증은 수행하지 않았다. 3시간 본 실행도 아직 시작하지 않았다.
- `_Upstage`의 실제 상태는 `fix/solar-ralph-skill-consistency` 브랜치, 수정 파일 4개, 미추적 `.gitmessage`, `clean-coauthor.sh`, `data/`이다. 이 중 앞의 두 파일은 Solar 보고에서 누락됐으며 `data/`도 ignore 대상이 아니다.
- 관련 문서: [Ralph Loop 스킬 설계와 실행 전 검증](wiki/projects/ralph-skill-stack-validation.md)

## [2026-07-17] troubleshoot | Ralph Loop 재개와 Question Mode 전환

- 낮 12시 14분경 작업 지시 후 불명 시점의 시스템 재부팅으로 CLI 세션이 종료됐고, 오후 3시 40분경 `claude --resume`으로 준비 작업을 재개했다.
- 재부팅 전 생성된 Phase 1~3 자료는 보존됐으며, 이후 Phase 4 실행 스크립트와 비교 계획을 계속 수정했다.
- `claude-upstage`에 권한 생략 옵션을 전달하는 대신 인자 없는 대화형 실행과 tmux 기반 프롬프트·권한 응답 자동화로 전환했다.
- `claude-upstage`가 Solar Open 2로 기동되는 화면과 launch/status/screenshot/quit 검증은 확인했다.
- 실제 loop가 없는 성공 로그, 존재하지 않는 tmux 세션과 `&&` 체인, `tmux load-buffer` 표준입력 누락, watchdog 루트 경로 오류를 차례로 확인했다.
- 작업폴더 커밋 `963d81a`, `918dc92`, `ed24e63`에서 Question Mode 전환과 후속 수정을 기록했다.
- 17시 16분 기준 tmux 세션과 `claude-upstage` 프로세스가 없어 유효한 3시간 본 실행은 아직 확인되지 않았다.
- 관련 문서: [랄프톤 Phase 4 재개와 Question Mode 전환](wiki/projects/ralphthon-phase4-recovery.md)

## [2026-07-17] observe | `claude-upstage` 권한 생략 옵션 거부

- `claude-upstage --dangerously-skip-permissions` 실행이 `unknown command`로 종료된 캡처를 확인했다.
- 설치된 `claude-upstage` 래퍼의 인자 파서를 읽기 전용으로 확인해 해당 옵션과 임의 인자 전달을 지원하지 않는 사실을 확인했다.
- 작업폴더의 Ralphthon 계획 문서가 이 옵션을 세 곳에서 실행 전제로 사용하고 있어 현재 계획대로는 Phase 4 진입이 불가능하다고 기록했다.
- 이 관찰은 `claude-upstage` 래퍼에 한정되며 Claude Code CLI 자체의 지원 여부는 이번에 시험하지 않았다.
- 관련 문서: [`claude-upstage`의 권한 생략 옵션 미지원](wiki/observations/claude-upstage-permission-flag.md)

## [2026-07-17] observe | 랄프톤 재현 비교 실험 계획

- 작업폴더의 Git 상태, 최근 커밋과 Ralphthon 관련 추적 파일을 읽기 전용으로 확인했다.
- 커밋 `6d62228`에서 비교 실험 계획 문서, 공개 README와 작업 로그가 함께 갱신된 사실을 확인했다.
- 계획된 실험 디렉터리와 실행 산출물은 아직 존재하지 않아 현재 상태를 `planning`으로 기록했다.
- 작업폴더 문서에 제시된 Codex 기준 수치는 독립 검증값이 아니라 원문 주장으로 구분했다.
- 관련 문서: [랄프톤 재현 비교 실험 — 계획 수립](wiki/projects/ralphthon-reproduction-planning.md)
