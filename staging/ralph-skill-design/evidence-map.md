---
title: Ralph Loop 스킬 설계 근거 지도
type: reference
description: Codex 원본과 Solar Open 2 재현 시도의 출처, 확인 범위와 provenance
tags: [solar-open2, ralph-loop, skill-design, provenance]
timestamp: 2026-07-19
producer: Codex
---

# Ralph Loop 스킬 설계 근거 지도

## 조사 경계

- Codex 조사·설계 산출물은 이 staging 디렉터리에만 둔다.
- sibling 작업공간 `_Upstage`와 `_Upstage-backup-20260717`은 읽기 전용 근거다.
- 실제 Claude Code 스킬과 Solar 실험 산출물은 `claude-upstage`에서 Solar Open 2가 작성해야 한다.
- 비밀값, 개인 계정 정보와 세션 원문은 공개 문서로 복사하지 않는다.

## 저장소 역할

| 위치 | 역할 | 확인 상태 |
| --- | --- | --- |
| `_Upstage-backup-20260717` | 사용자가 지정한 Codex 랄프루프 원본 | 현재 reachable `main`에는 Ralph 관련 파일이 없음 |
| `_Upstage` | Solar Open 2 + Claude Code 재현 작업공간 | Ralph 자료 복사본, 실행 스크립트와 후속 수정 존재 |
| `_Upstage-log-codex` | Codex의 공개 관찰·설계 기록 | 실제 Solar 스킬을 두지 않음 |

백업 저장소의 현재 `main`은 `1070953`이며 `origin/main`을 추적한다. 현재 트리와 reachable history에서는 Ralph 실행 파일을 확인하지 못했다. 따라서 “원본”이라는 사용자 지정은 존중하되, 세부 실행 계약은 `_Upstage/docs/experiments/ralphthon/`에 보존된 복사본과 기록을 함께 대조해야 한다. 원본과 복사본의 동일성을 입증할 hash나 원본 commit이 없으면 해당 내용은 `보존된 복사본`으로 표기한다.

## Codex 실행 계약의 보존된 흔적

`_Upstage/docs/experiments/ralphthon/`에서 다음을 확인했다.

- `run-ralph-direct.sh`는 `$ralph`와 `RALPH_GOAL.md`를 결합해 `codex -a never -s workspace-write`로 전달한다.
- `OMX_RALPH_APPEND_INSTRUCTIONS_FILE`로 별도 세션 지침을 연결한다.
- `RALPH_GOAL.md`는 P0, checkpoint, 실패 축소, deadline과 handoff를 정의한다.
- 실행 중 remote push는 금지하고 검증 checkpoint를 위한 local commit만 허용한다.
- 산출물은 Codex용 `.codex/skills`, `.codex/agents`와 native multi-agent runtime을 전제로 한다.

이는 `$ralph`가 단순 프롬프트 접두사가 아니라 Codex/OMX 실행 계약과 결합됐음을 보여준다.

## Solar 재현 시도에서 확인된 사실

Solar 작업공간은 원격 `origin/main`의 `6d62228` 이후 로컬에 다음 6개 후속 커밋이 있다.

| Commit | 확인된 수정 목적 |
| --- | --- |
| `963d81a` | Question Mode 전환과 실행 스크립트 수정 |
| `918dc92` | tmux buffer 입력과 watchdog root 경로 수정 |
| `ed24e63` | root 계산과 prompt injection 보강 |
| `748b9c4` | nohup 환경의 tty redirect 제거 |
| `628876e` | UTF-8 파싱 문제를 피하도록 스크립트 재작성 |
| `3c2387d` | 지원되지 않는 `tmux load-buffer -a` 제거 |

추가로 확인한 상태는 다음과 같다.

- `.claude/skills/run-ralphthon/SKILL.md`는 존재하지만 `.gitignore`의 `/.claude/` 규칙 때문에 추적되지 않는다.
- 이 스킬은 tmux 실행과 watchdog을 설명하는 launcher이며 Ralph 작업 반복 계약은 정의하지 않는다.
- Solar 세션의 발견된 skill 목록에 `ralph` 또는 `run-ralphthon`이 없었던 기록이 있다.
- 초기 입력은 문자 그대로 `$ralph`와 Goal 전체를 tmux buffer로 붙여 넣는다.
- `claude-upstage` 래퍼는 모델, continue와 resume만 전달하며 일반 Claude CLI 옵션을 통과시키지 않는다.
- `record-session.sh`와 `capture-checkpoints.sh`는 launcher에서 시작되지 않는다.
- `capture-checkpoints.sh`의 root 계산은 현재 파일 위치 기준으로 프로젝트 root가 아니라 `src/scripts`를 가리킨다.
- 작업공간은 `record-session.sh` 실행 권한 변경과 untracked `data/` 때문에 clean baseline이 아니다.
- local `main`은 `origin/main`보다 6 commits 앞서며 upstream branch 설정이 없다.

## 로그가 입증하는 범위

- watchdog 시작과 무활동 후 prompt 재전송
- 짧은 test watchdog에서 반복 입력
- 한 시점의 `RALPH_GOAL.md` 경로 오류
- 존재하지 않는 tmux pane 접근 오류
- `claude-upstage`가 Solar Open 2로 기동된 화면

다음은 입증되지 않았다.

- Solar용 `$ralph` skill 발견과 호출
- P0 하나의 자율 완료
- local checkpoint commit 생성
- 중단 후 상태 기반 재개
- 유효한 3시간 본 실행
- GPT+Codex 또는 Claude+Claude Code와의 모델 성능 직접 비교

## 공식 동작과 설계에 미치는 영향

Claude Code project skill은 `.claude/skills/<name>/SKILL.md`에서 발견되고 `/<name>`으로 직접 호출한다. 추가 디렉터리 안의 `.claude/skills`도 `--add-dir`로 읽을 수 있지만, 현재 `claude-upstage` 래퍼는 이 옵션을 전달하지 않는다. 따라서 staging 설계는 사용자가 Solar 세션에 수작업으로 전달하고 Solar가 `_Upstage/.claude/skills`를 생성하는 방식을 기준으로 한다. 공식 동작의 근거는 [Claude Code Skills 문서](https://code.claude.com/docs/en/slash-commands)다.

## 판정 표기

- **확인됨**: 파일, Git 상태, commit 또는 로그로 직접 확인
- **강한 추정**: 두 개 이상의 독립 근거가 일치하지만 실행 재현 전
- **미검증**: 설계상 필요하지만 실제 동작 증거 없음
