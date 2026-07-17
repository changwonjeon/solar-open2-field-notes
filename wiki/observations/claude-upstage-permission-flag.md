---
type: Observation
title: claude-upstage의 권한 생략 옵션 미지원
description: claude-upstage 래퍼가 --dangerously-skip-permissions를 거부하는 동작과 Ralphthon 재현 계획에 미치는 영향
tags: [solar-open2, claude-code, claude-upstage, permissions, ralphthon, limitation]
timestamp: 2026-07-17T11:58:22+09:00
status: confirmed
source_workspace: _Upstage
evidence: ../../assets/screenshots/2026-07-17-claude-upstage-unsupported-permission-flag.jpg
---

# `claude-upstage`의 권한 생략 옵션 미지원

## 결론

현재 설치된 `claude-upstage` 래퍼에서는 다음 호출을 사용할 수 없다.

```bash
claude-upstage --dangerously-skip-permissions
```

래퍼는 이 값을 Claude Code 옵션으로 전달하지 않고 자체 최상위 명령으로 해석하며, `unknown command` 오류와 사용법을 출력한 뒤 종료한다.

이 결론은 **현재 설치된 `claude-upstage` 래퍼에 한정**된다. Claude Code CLI를 직접 실행했을 때 동일 옵션을 지원하는지는 이번 관찰에서 시험하지 않았다.

## 화면 증거

![claude-upstage가 dangerously-skip-permissions를 거부한 화면](../../assets/screenshots/2026-07-17-claude-upstage-unsupported-permission-flag.jpg)

캡처에서 확인되는 순서는 다음과 같다.

1. 작업폴더의 `main` 브랜치에서 `claude-upstage --dangerously-skip-permissions`를 입력했다.
2. `claude-upstage: unknown command '--dangerously-skip-permissions'`가 출력됐다.
3. 이어서 지원되는 명령과 옵션 목록이 표시됐다.

캡처에는 API 키, 이메일 또는 인증 정보가 노출되지 않았다.

## 래퍼 구현 확인

관찰 당시 `claude-upstage`는 사용자 로컬 실행 경로에 설치된 Bash 스크립트였다. 파일을 수정하거나 실행하지 않고 인자 처리 부분만 읽어 확인했다.

래퍼의 `parse_flags`가 명시적으로 허용하는 항목은 다음과 같다.

- `--model`
- `-c`, `--continue`
- `-r`, `--resume`
- `-h`, `--help`

그 밖의 옵션은 `unknown option`으로 처리된다. 최상위 명령 분기에서도 `--dangerously-skip-permissions`는 실행 명령으로 인정되지 않아 캡처처럼 `unknown command`가 된다.

래퍼는 Claude Code 실행 직전에 인자 목록을 초기화하고 세션 관련 인자만 다시 구성한다. 따라서 현재 구현에는 알 수 없는 옵션을 Claude Code로 그대로 전달하는 passthrough 동작도 없다.

## 작업폴더 계획과의 차이

작업폴더의 Ralphthon 비교 계획 문서는 `--dangerously-skip-permissions`를 세 곳에서 실행 전제로 사용한다.

- Codex 실행 스크립트를 Claude Code용으로 바꾸는 Phase 2 설명
- Solar 실행을 시작하는 Phase 4 설명
- Day 1의 다음 액션

그러나 현재 래퍼에서는 이 옵션을 전달할 수 없으므로, 문서에 적힌 실행 절차와 실제 도구 동작이 일치하지 않는다.

## 영향

- 현재 계획에 적힌 명령 그대로는 Ralph Loop 실행 단계에 진입할 수 없다.
- 권한 확인을 어떻게 처리할지 결정하기 전까지 완전 자율 실행 조건은 충족됐다고 볼 수 없다.
- 향후 대안을 사용한다면 Codex 기준의 `사용자 개입 0회`와 동등한 조건인지 별도로 평가해야 한다.

## 미확인 사항

- Claude Code CLI를 직접 호출할 때 해당 옵션이 실제로 지원되는지
- `claude-upstage`가 안전하게 임의 Claude Code 옵션을 전달하도록 확장될 예정인지
- 권한 확인을 유지한 상태에서 Ralph Loop를 실행할 수 있는지
- 다른 설정이나 환경변수로 같은 목적을 달성할 수 있는지

## 관련 문서

- [랄프톤 재현 비교 실험 — 계획 수립](../projects/ralphthon-reproduction-planning.md)
- [저장소 색인](../../index.md)
- [관찰 로그](../../log.md)

