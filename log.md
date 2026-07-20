---
type: Log
title: Solar Open 2 Field Notes Log
description: 작업폴더 관찰과 지식 문서 갱신의 시간순 기록
tags: [solar-open2, log]
timestamp: 2026-07-17T11:50:35+09:00
---

# Solar Open 2 Field Notes Log

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
