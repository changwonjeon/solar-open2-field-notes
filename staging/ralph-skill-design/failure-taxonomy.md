---
title: Solar Ralph Loop 실패 분류
type: analysis
description: 3시간 본 실행 이전의 실패를 모델과 실행 스택 계층으로 분리한 분석
tags: [solar-open2, ralph-loop, troubleshooting]
timestamp: 2026-07-19
producer: Codex
---

# Solar Ralph Loop 실패 분류

## 결론

현재 증거는 Solar Open 2가 Ralph 과제를 수행하지 못했다고 판정하기에 부족하다. 모델이 P0 작업을 지속적으로 수행할 수 있는 상태에 도달하기 전에 skill 의미론, wrapper, 권한, tmux, 기록과 Git baseline 문제가 발생했다. 이번 단계에서 확인 가능한 결과는 `모델 성능 실패`가 아니라 `평가 실행 스택 미성립`이다.

## 실패 계층

| 계층 | 관찰 | 판정 | 다음 검증 |
| --- | --- | --- | --- |
| Skill 발견 | Solar 세션 목록에 `ralph`가 없고 project skill은 Git에서 무시됨 | 확인됨 | 새 세션에서 `/solar-ralph` 직접 호출 |
| 실행 의미론 | `$ralph` 문자열만 주입하고 Codex/OMX 반복 계약은 이식하지 않음 | 강한 추정 | 단일 P0 이후 무입력 다음 작업 전환 |
| Goal 이식 | Goal이 Codex skill·agent 경로와 새 Codex 세션을 요구 | 확인됨 | 공통 task spec과 Solar runtime 계약 분리 |
| Wrapper 호환성 | 일반 Claude CLI flag가 래퍼 parser에서 거부됨 | 확인됨 | 지원 명령 표와 doctor 결과 고정 |
| 권한 처리 | 화면 keyword에 따라 `y`를 보내는 휴리스틱 | 확인됨 | allow/deny fixture로 오승인 검사 |
| 세션 지속 | tmux와 watchdog은 생성됐으나 유효 loop는 확인되지 않음 | 확인됨 | heartbeat 기반 10분 soak test |
| 활동 감지 | pane 문자열 길이만 비교해 동일 길이 변경을 놓칠 수 있음 | 확인됨 | content hash와 tool activity 사용 |
| 기록 | recorder와 checkpoint monitor가 launcher에 연결되지 않음 | 확인됨 | 시작부터 종료까지 artifact manifest 생성 |
| 경로 | 여러 root 계산 오류가 반복됐고 checkpoint script에도 잔존 | 확인됨 | 모든 script를 임시 경로에서 smoke test |
| Git baseline | dirty state, untracked data, upstream 부재 | 확인됨 | preflight가 타이머 전에 hard fail |
| 모델 역량 | P0 자율 사이클의 관찰 결과 없음 | 미검증 | 10분·30분 시험 통과 후 평가 |

## 실패 귀속 원칙

1. 모델이 Goal을 받고 tool을 사용할 수 있었는지 먼저 입증한다.
2. skill 미발견, permission wait, wrapper exit와 Git preflight 실패는 모델 실패로 계산하지 않는다.
3. 정상 tool access 상태에서 같은 P0를 두 번 잘못 수행하거나 다음 P0를 선택하지 못한 경우에만 agent behavior 실패 후보로 기록한다.
4. 실패 후보도 prompt, tool result, 상태 파일과 commit diff가 모두 있을 때 확정한다.
5. GPT+Codex와 Claude+Claude Code는 프론티어 agent system의 참고점이다. 동일 task·시간·권한으로 직접 실행하지 않은 조합의 수치를 만들지 않는다.

## Upstage에 유용한 관찰 축

- 모델 교체만으로 기존 agent workflow가 유지되는가
- wrapper가 upstream Claude Code 기능을 얼마나 보존하는가
- 사용자가 별도 tmux/watchdog/permission automation을 작성해야 하는가
- 중단 원인을 모델, provider API, wrapper와 harness 중 어디로 식별할 수 있는가
- 장시간 실행 전 smoke test와 readiness 진단이 제공되는가
- local checkpoint와 resume가 제품 수준에서 지원되는가

이 축은 모델 benchmark와 별도로 `agent integration readiness`로 평가한다.
