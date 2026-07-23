---
title: "Task 01 Ralphthon 재현 실험 — Solar Open 2 성능 및 준비도 보고서"
type: Experiment Report
description: "Codex로 수행한 Ralphthon Track 2 작업을 Solar Open 2와 Claude Code 조합으로 재현하려 한 과정, 입증된 이식 범위, 운영 장애와 아직 측정하지 못한 모델 성능을 구분한 감사 보고서"
tags:
  - solar-open2
  - claude-code
  - codex
  - ralphthon
  - ralph-loop
  - agent-evaluation
  - readiness
timestamp: 2026-07-23T20:20:00+09:00
status: preflight-incomplete
---

# Task 01 Ralphthon 재현 실험

## 요약 판정

Task 01의 목적은 Ralphthon Track 2에서 Codex가 만든 논문 리뷰 에이전트와 그 자율 실행 과정을 Solar Open 2+Claude Code 조합으로 재현하고, 같은 목표와 검증 계약 아래 장시간 에이전트 성능을 비교하는 것이다.

현재 증거가 지지하는 판정은 세 가지로 나뉜다.

| 판정 대상 | 결과 | 의미 |
| --- | --- | --- |
| Solar Open 2의 본과제 수행 성능 | **N/A — 측정 불가** | 유효한 장시간 본 실행, P0 결과, 리뷰 결과와 원시 실행 증거가 없다. 미실행을 0점 또는 실패로 환산하지 않는다. |
| Codex 결과의 Solar 환경 이식 | **부분 입증** | Solar Open 2 백엔드 기동, 프로젝트 스킬 생성·발견, 첫 Git checkpoint 성공 경로까지 확인됐다. |
| 장시간 운영 준비도 | **미준비** | 후속 checkpoint, 실패 cleanup, 기록기·monitor·watchdog 통합, soak test와 rehearsal이 끝나지 않았고 구조 재편 뒤 실행 경로도 다시 검증해야 한다. |

따라서 이 Task는 “Solar Open 2가 Ralphthon 과제를 어느 수준으로 해결했다”는 모델 성능 보고가 아니라, **그 성능을 공정하게 측정하기 위한 이식·실행 스택이 어디까지 준비됐는지 보여주는 중간 보고**로 읽어야 한다.

## 실험 목표와 시간 기준

원본 과제는 10편의 mock 논문을 처리하는 Track 2 리뷰 에이전트를 만들고 다음을 검증하는 복합 에이전트 작업이다.

- 공식 스킬과 wrapper 스킬 설치
- Root Coordinator와 Review Worker의 권한 분리
- 입력·증거 hash와 canonical review schema 보존
- bounded queue, lease, atomic ledger와 idempotency 구현
- 10편을 3회 처리해 schema 위반과 중복 게시가 없음을 확인
- malformed JSON, worker·claim·post timeout과 process restart 복구
- 품질 평가, 제출 문서, PDF와 handoff 생성

시간 기록에는 서로 다른 두 개념이 섞이면 안 된다.

| 구분 | 시간 | 해석 |
| --- | --- | --- |
| 원본 Ralph Goal의 계획 창 | 최대 3시간 | 12:30~15:30으로 배정된 구현·검증 시간 상한이다. |
| Codex의 실제 실행 | 약 2시간 | 사용자가 제공한 실제 수행 시간이다. 공개 원시 transcript만으로 초 단위 재구성되지는 않으므로 근삿값으로 유지한다. |
| Solar 재현의 계획 창 | 최대 3시간 | 동일한 상한을 적용하려던 실험 설계다. |
| Solar의 유효한 본 실행 | 없음 | 7월 17일 시도는 정상 작업 loop와 기록 스택이 결합되기 전에 중단돼 3시간 실행으로 인정하지 않는다. |

Codex가 “3시간 실행했다”고 표현하면 계획 창과 실제 실행 시간이 혼동된다. 향후 비교에서는 **실제 wall-clock 약 2시간**과 **허용된 최대 3시간**을 별도 필드로 기록해야 한다.

## 평가 대상과 귀속 원칙

이 실험의 직접 비교 단위는 모델 하나가 아니라 `모델 + 에이전트 하네스` 조합이다.

| 조합 | 이 보고서에서의 역할 |
| --- | --- |
| GPT 계열 모델 + Codex | 원본 구현과 프론티어 비교 기준 |
| Claude 계열 모델 + Claude Code | 같은 하네스 계열의 추가 비교 기준 |
| Solar Open 2 + Claude Code | 재현·이식 대상 |

동시에 모델 추론 능력과 하네스·스크립트·운영 환경 문제를 분리한다. tmux, wrapper 인자 처리, 경로 계산, 시스템 재부팅과 기록기 오류는 모델이 본과제를 해결하지 못했다는 직접 증거가 아니다.

Solar 세션이 생성·수정한 프로젝트 스킬은 Solar 환경의 산출물로 기록할 수 있다. 그러나 공개 transcript와 artifact별 provenance가 완전하지 않으므로, 개별 수정의 사고 과정이나 모든 후속 보정을 Solar Open 2 단독 성능으로 귀속하지 않는다.

## R0~R8 준비도

R0~R8은 “본 실행에서 몇 개 P0를 완료했는가”가 아니라, **유효한 본 실행을 시작하기 위한 준비도 gate**다. 낮은 단계 통과를 높은 단계의 성능 증거로 대체하지 않는다.

| 단계 | 준비도 기준 | 현재 상태 | 확인된 근거와 남은 조건 |
| --- | --- | --- | --- |
| R0 | 원본·목표·평가 계약 보존 | **부분 통과** | Goal, fixture, 구현과 테스트가 보존됐다. 다만 원본 evidence·submission·staging 일부가 없어 모든 기준 수치를 독립 검증할 수 없다. |
| R1 | 비교 조건과 시간·권한 계약 동결 | **부분 통과** | 14개 평가 항목, 최대 3시간, 원격 push 금지 등은 문서화됐다. Codex 실제 약 2시간과 계획 3시간을 구분한 정식 run manifest는 없다. |
| R2 | Solar Open 2 백엔드 기동 | **통과** | `claude-upstage`가 Solar Open 2 모델로 기동되고 tmux launch/status 흐름이 동작하는 화면을 확인했다. |
| R3 | 프로젝트 스킬 생성·발견 | **통과** | `/solar-ralph`와 `/git-checkpoint`가 생성됐고 새 Claude Code 세션에서 help discovery를 확인했다. |
| R4 | 스킬·스크립트 정적 계약 | **부분 통과** | 대상 스크립트의 `zsh -n`과 `git diff --check`는 통과했다. ShellCheck는 실행하지 않았고 정적 파싱은 경로·수명주기 정확성을 보장하지 않는다. |
| R5 | 첫 checkpoint의 안전한 성공 경로 | **통과** | 격리 Git 저장소에서 preflight와 Gate 0~8을 거쳐 승인 파일만 local commit에 포함되고 `approved_paths`가 보존됐다. |
| R6 | 후속 checkpoint와 실패 복구 | **미통과** | subsequent checkpoint, 재시도, 공백·Unicode 경로, 승인 외 경로, rename/copy, 실패 뒤 index cleanup 검증이 남았다. |
| R7 | runtime 통합과 장시간 rehearsal | **미통과** | recorder·checkpoint monitor·watchdog의 launcher 연결, heartbeat, 종료·resume, 10분 soak와 30분 rehearsal이 미완료다. |
| R8 | 동일 조건 본 실행과 성능 산출 | **미실행** | Solar의 유효한 본 실행, 10편×3회 결과, P0별 증거와 비교 가능한 시간·품질 데이터가 없다. |

현재 최고 준비 단계는 **R5의 제한된 성공 경로**다. R6와 R7을 통과하지 않은 상태에서 R8을 시작하면 하네스 실패와 모델 성능을 다시 혼동하게 된다.

## 관찰된 장애와 해결 과정

### 1. `claude-upstage` 인자 계약 불일치

초기 launcher는 일반 Claude CLI에서 쓰는 권한 생략 옵션을 wrapper에 전달했다. 관찰한 `claude-upstage` 인자 파서는 해당 옵션을 정상 실행 경로로 처리하지 않고 종료했다.

인자 없는 대화형 `claude-upstage`를 실행하고 tmux로 초기 프롬프트와 권한 응답을 전달하는 Question Mode로 바꿨다. 이 변경으로 Solar Open 2 백엔드 기동은 확인했지만, 장시간 무인 권한 처리가 안전하다는 의미는 아니다.

### 2. 시스템 재부팅과 세션 소실

사용자가 자리를 비운 사이 시스템이 재부팅돼 기존 CLI와 tmux 상태가 사라졌다. 재개는 사람이 수행했다. 이는 외부 환경 중단이며 Solar Open 2의 과제 실패로 계산하지 않는다. 다만 unattended run의 실효성을 평가할 때는 사용자 개입 1건과 복구 소요 시간으로 별도 기록해야 한다.

### 3. 절대 시각 watchdog

초기 watchdog은 과거 행사 종료 시각을 절대값으로 사용해 재현 당일에는 남은 시간이 음수가 되고 즉시 종료될 수 있었다. 실행 순간의 epoch를 한 번 저장하고 10,800초를 재는 상대 시간 방식으로 수정했다.

이 수정은 deadline 계산 방식의 개선이다. 실제로 3시간 동안 정확히 유지·종료됐다는 증거는 아직 없다.

### 4. tmux 수명주기와 프롬프트 주입

다음 문제가 여러 차례 나타났다.

- 존재하지 않는 세션을 종료하는 명령이 `&&` 체인을 중단해 시작 명령까지 도달하지 않음
- `tmux load-buffer`의 stdin 대상 `-` 누락
- 지원되지 않는 `load-buffer -a` 사용
- 프롬프트를 주입하기 전에 launcher가 성공을 기록
- tmux 세션은 있으나 실제 Solar 작업 loop는 없는 상태

stdin 처리와 buffer 조합 방식을 수정하고 세션 제어를 보강했다. 그러나 “세션 생성 성공”과 “자식 모델이 목표를 받아 실제 작업 중”을 구분하는 readiness probe는 끝까지 입증되지 않았다.

### 5. 저장소 루트와 로그 경로 계산

스크립트 위치를 기준으로 상위 디렉터리 수를 잘못 계산해 Goal을 찾지 못하거나 로그가 다른 디렉터리에 분산됐다. 이후 root 계산을 여러 차례 보정했다.

Task 03 구조 재편 뒤 canonical 경로가 바뀌었으므로, 과거 수정이 현재 구조에서도 유효하다고 가정할 수 없다. 최신 감사에서는 일부 launcher·recorder·checkpoint 스크립트에 이전 경로 또는 잘못된 상위 경로 계산이 남아 있는 것으로 확인됐다.

### 6. nohup과 문자열 처리

`/dev/tty`로 stderr를 보내는 방식은 nohup 환경에서 실패했다. 해당 의존성을 제거하고 파일 기반 디버깅 로그로 바꿨다. 이후 실행 스크립트를 ASCII 중심으로 다시 작성해 문자열·파싱 불확실성을 줄였다.

이 조치는 호환성 완화책이다. Unicode 자체를 모델 성능 저하의 원인으로 일반화할 증거는 없다.

### 7. Git checkpoint 계약 오류

스킬 구현 뒤 정적 검토와 기능 시험에서 다음 blocker가 발견됐다.

- command substitution 경계에서 Python 실패 코드가 소실될 수 있음
- run-state가 저장소 밖·절대 경로·디렉터리·symlink를 가리킬 수 있음
- `null` sentinel 복원 실패로 첫 checkpoint를 후속 checkpoint로 오인
- 옵션 값이 없는데 `$2`를 먼저 읽는 `set -u` 오류
- heredoc stdin 충돌로 성공 JSON의 `approved_paths`가 빈 배열이 됨
- zsh에서 Bash 전용 `read -ra` 사용
- preflight와 commit gate의 경로 containment 규칙 불일치

종료 코드 보존, realpath containment, argv 전달, 옵션 guard와 zsh 호환 처리를 추가했다. 그 결과 첫 checkpoint 성공 경로는 격리 저장소에서 통과했다. 후속 checkpoint와 실패 뒤 cleanup은 아직 검증되지 않았다.

## 실제로 입증된 결과

| 관찰 | 증거 수준 | 모델 성능 해석 |
| --- | --- | --- |
| Solar Open 2 모델로 CLI 기동 | 직접 관찰 | 백엔드 연결 가능성은 확인했지만 과제 해결 능력은 측정하지 않음 |
| tmux launch/status/screenshot/quit | 제한적 스모크 | 하네스 제어의 일부만 확인 |
| 두 프로젝트 스킬 생성·발견 | artifact와 help 기록 | Claude Code 스킬 형식으로 이식 가능함을 부분 입증 |
| Git checkpoint blocker 수정 | diff·기능 기록 | 도구 계약 구현 능력의 사례지만 사람의 검토·재지시 비용을 포함 |
| 첫 checkpoint Gate 통과 | 격리 저장소 시험 | 승인 경로만 commit하는 좁은 성공 경로 확인 |
| 3시간 자율 실행 | 증거 없음 | 평가 불가 |
| 14개 본과제 완료율 | 증거 없음 | 평가 불가 |
| 10편×3회 schema·중복 결과 | Solar 증거 없음 | 평가 불가 |
| TP·FP·FN·F1 | Solar 결과 없음 | 평가 불가 |
| failure injection 복구율 | Solar 본과제 결과 없음 | 평가 불가 |

## 원본 Codex 기준의 한계

사용자 제공 정보에 따르면 Codex의 실제 실행은 약 2시간이었다. 목표 문서의 3시간은 실행 상한이다. 공개 보존 자료에는 구현 코드, fixture와 테스트가 상당 부분 남아 있지만 모든 원시 evidence와 최종 submission이 포함된 것은 아니다.

2026년 7월 23일 현재 보존된 source를 다시 검사한 결과는 다음과 같다.

- Python unit/integration test 65개 중 62개 통과
- 1개 실패와 2개 오류
- 실패 원인은 누락된 `evidence/`와 `staging/` 자산을 참조하는 검증 경로
- 보존본에는 최종 submission, outbox와 handoff 일부가 없음

이는 보존된 코드 전체가 무효라는 뜻이 아니다. mock runtime, schema, ledger와 여러 failure recovery 단위 시험은 통과한다. 다만 비교 템플릿에 적힌 `14/14`, `29/29`, `30/30`, `F1=1.0`을 이 공개 보존본만으로 모두 독립 재현했다고 말할 수 없다.

추가로 비교 템플릿은 산출물 생성률을 `29/29`로 표기하면서 파일 목록에는 40개 항목을 열거한다. 기준 분모를 다시 동결하기 전에는 해당 생성률을 모델 비교에 사용하지 않는다.

## 현재 측정 도구를 그대로 쓰면 안 되는 이유

초기 checkpoint 수집기와 비교 스크립트는 관찰 보조 도구이지 확정 성능 평가기로 보기 어렵다.

- checkpoint JSON의 상태가 없거나 로그 문자열 휴리스틱·수동 trigger로 생성될 수 있음
- 가장 큰 checkpoint 번호를 실제 완료 개수로 간주할 수 있음
- 산출물 개수를 실제 filesystem 검사가 아닌 수식으로 추정
- checkpoint가 하나만 있어도 schema 준수율을 100%로 둘 수 있음
- 실패가 기록되지 않으면 복구율을 100%로 표현할 수 있음
- 사용자 개입 수가 로그 파싱 없이 0으로 고정될 수 있음

향후 평가기는 각 수치가 가리키는 원시 artifact, hash, validator 출력과 timestamp를 요구하고, 근거가 없으면 `0`이 아니라 `N/A`를 출력해야 한다.

## Task 03 구조 재편과의 관계

Task 03은 Wiki와 task 디렉터리 구조를 canonical 형태로 정리하고 frontmatter·링크·index를 맞추는 별도 정비 작업이다. Task 01 자료도 이 과정에서 `tasks/01-ralpthon` 계층으로 이동·분류됐다.

두 Task의 관계는 다음처럼 제한한다.

- Task 03은 Task 01의 자료 탐색성과 source/docs/data/output 경계를 개선한다.
- 구조 재편 뒤 stale launcher 경로를 찾아내고 다시 검증해야 하는 계기를 제공한다.
- Task 03의 frontmatter, 링크 수정, 파일 이동과 검증 통과를 Solar Open 2의 Ralphthon 본과제 성과로 합산하지 않는다.
- Task 01에서 만든 스킬·런처·실험 결과를 Task 03의 산출물로 재귀속하지 않는다.
- Task 03 완료 여부는 Task 01의 R6~R8 통과를 대신하지 않는다.

즉, Task 03은 **증거 보존과 구조 정합화**, Task 01은 **에이전트 실행 및 성능 평가**를 담당한다. 같은 파일을 만졌더라도 성과는 목적과 검증 계약에 따라 한 번만 귀속한다.

## 본 실행 측정 기준

### 결과 품질

| 지표 | 산식 또는 증거 |
| --- | --- |
| P0 완료율 | validator와 artifact hash가 있는 완료 P0 수 / 동결된 전체 P0 수 |
| 산출물 생성률 | 동결 manifest에 정의한 필수 파일 중 내용·schema·hash 검증을 통과한 수 |
| schema 준수율 | 실제 10편×3회 output 중 canonical validator를 통과한 수 / 30 |
| 중복 발생 | paper ID와 idempotency key별 중복 post event 수 |
| 리뷰 품질 | 동결 gold와 location-match 계약에 따른 TP·FP·FN·precision·recall·F1 |
| 제출물 완성도 | PDF build, 페이지 제한, 익명성, placeholder와 수치 일치 검사 |

### 자율성과 운영

| 지표 | 기록 방식 |
| --- | --- |
| 실제 실행 시간 | launcher 시작부터 정상 종료까지 monotonic wall-clock |
| active work 시간 | 모델 출력·도구 호출 heartbeat가 있던 구간 |
| 첫 유효 P0 시간 | 첫 validator 통과와 checkpoint commit의 두 timestamp |
| 사용자 개입 | 재부팅 복구, 권한 응답, 프롬프트 재전송, 수동 수정과 재시작을 원인별 집계 |
| 실패 복구율 | 동결한 failure injection 각각의 발생·감지·복구·최종 검증 event |
| resume 정확성 | 중단 전 ledger·HEAD·run-state와 재개 뒤 중복·누락 비교 |
| Git 안전성 | 승인 외 staging, 원격 변경, destructive Git, secret·symlink·대용량 파일 위반 수 |
| 보고 정확성 | 최종 주장과 실제 filesystem·Git·validator 결과의 불일치 수 |

### 비교 공정성

- 같은 Goal, fixture, gold, 시간 상한과 도구 권한을 사용한다.
- 모델별로 최소 3회 반복하고 성공률과 시간의 평균뿐 아니라 범위를 공개한다.
- 프롬프트 변경, 사람의 수정, 하네스 수정과 재시작은 run별로 기록한다.
- 계획 시간과 실제 시간을 분리한다.
- 모델·하네스·스크립트·환경 장애를 별도 원인 코드로 분류한다.
- 실행 증거가 누락된 지표는 추정하지 않고 `N/A`로 둔다.

## 다음 실행의 통과 조건

Solar 본 실행 전에 다음 순서로 준비도를 닫아야 한다.

1. 구조 재편 이후 모든 launcher·Goal·data 경로를 canonical 위치에 맞추고 실제 preflight를 통과한다.
2. 후속 checkpoint, 재시도, 승인 외 경로와 실패 cleanup으로 R6을 검증한다.
3. recorder·monitor·watchdog을 launcher에 연결하고 artifact manifest를 자동 생성한다.
4. 10분 soak에서 heartbeat, transcript, checkpoint와 종료 처리를 확인한다.
5. 30분 rehearsal에서 중단·resume과 최소 한 개 failure injection을 검증한다.
6. 깨끗한 baseline과 동결 manifest를 만든 뒤 사용자 승인 하에 최대 3시간 본 실행을 시작한다.
7. 본 실행이 끝난 뒤 원시 증거에서만 지표를 계산하고 Codex의 실제 약 2시간 결과와 조건 차이를 함께 표시한다.

## 종합 평가

Solar Open 2는 Claude Code의 대체 백엔드로 기동되고 프로젝트 스킬 형식과 좁은 Git checkpoint 계약을 구현하는 수준까지는 도달했다. 이 과정은 **에이전트 하네스 이식 가능성**을 보여준다.

반면 첫 구현이 바로 안정적으로 동작한 것은 아니다. wrapper 옵션, tmux, root 경로, 로그 수명주기와 Git gate에서 반복적인 수정이 필요했고 사람 또는 별도 검토자의 진단·재지시 비용이 컸다. 이는 현재 조합의 운영 성숙도가 낮다는 증거지만, 본과제 작업 loop에 진입하기 전의 문제이므로 Solar Open 2의 논문 리뷰 품질이나 3시간 자율 코딩 능력 점수로 환산할 수 없다.

가장 객관적인 현재 결론은 다음과 같다.

> **Solar Open 2의 Ralphthon 본과제 성능은 아직 N/A다. Codex 결과를 Claude Code 기반 Solar 환경으로 이식할 가능성은 부분 입증됐지만, 재현 가능한 장시간 운영 준비는 완료되지 않았다.**

## 관련 공개 기록

- [Ralphthon Codex 원본 공개 저장소](https://github.com/changwonjeon/20260712-ralphthon-submit)
- [Ralph Loop 스킬 설계와 실행 전 검증](../../wiki/projects/ralph-skill-stack-validation.md)
- [Ralphthon Phase 4 재개와 Question Mode 전환](../../wiki/projects/ralphthon-phase4-recovery.md)
- [Ralphthon 재현 비교 실험 계획](../../wiki/projects/ralphthon-reproduction-planning.md)
- [7월 20일까지의 작업 타임라인](../../wiki/projects/upstage-work-summary-through-2026-07-20.md)
- [`claude-upstage` 권한 옵션 관찰](../../wiki/observations/claude-upstage-permission-flag.md)
