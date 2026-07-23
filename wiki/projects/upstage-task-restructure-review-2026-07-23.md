---
title: _Upstage tasks 상위 구조 재편 후속 감사
type: Project Review
description: tasks/ 중심 재편 완료 상태를 blob 보존, OKF, 링크, 문서 정합성과 Git 반영 안전성 관점에서 재검토한 보고서
tags: [solar-open2, llm-wiki, okf, tasks, migration, follow-up-review]
timestamp: 2026-07-23T00:00:00+09:00
status: commit-blocked
source_workspace: _Upstage
source_branch: main
source_head: 6e8c5d10a4eca26b52c33462ca90ee94baea60ca
comparison_commit: 7024b1b
---

# `_Upstage` `tasks/` 상위 구조 재편 후속 감사

## 결론

`tasks/01-ralphthon`과 `tasks/02-meeting-minutes`로 실험을 분리한 **상위 구조의 방향은 이전보다 명확하다.** 특히 회의록 실험은 `source/original`, `docs/meeting-minutes`, `output`으로 입력·위키·산출물을 나눈 점이 좋다.

하지만 2026년 7월 23일 재검토 시점의 worktree는 **커밋하면 안 되는 상태**다. 이전 감사에서 발견한 Ralphthon 원본 42개가 여전히 복구되지 않았고, 이번 재편에서는 현재 HEAD가 추적하던 Ralph 프로젝트 자산 36개가 새 위치에서 blob hash로 발견되지 않는다. 새 `tasks/` 파일은 모두 미추적이며 README·AGENTS·index·log도 실제 구조와 맞지 않는다.

판정은 다음과 같다.

> `tasks/` 도메인 설계는 개선됐지만 migration 보존성과 Wiki 탐색성이 충족되지 않았다. **방향 승인, Git 반영 거부, 원본 복구 후 재검증 필요**다.

현재 점수는 **42/100**이다. 이전 48점보다 낮아진 이유는 `tasks/` 설계 개선보다 이번 worktree에서 새로 발생한 tracked source 36개 누락과 `docs/index.md` 손상이 더 크기 때문이다.

## 기준 상태

- branch: `main`
- HEAD: `6e8c5d1`
- worktree: 변경 중이 아니라 사용자가 완료했다고 알린 미커밋 상태
- 주요 상태: tracked 삭제 47개, 수정 3개 이상, `docs/experiment-log.md`와 `tasks/` 전체 미추적
- `_Upstage`에는 어떤 수정도 가하지 않고 읽기 전용으로 감사했다.

## 개선된 점

### 실험 단위가 명확해졌다

```text
tasks/
├── 01-ralphthon/
│   ├── source/
│   ├── data/
│   ├── docs/ralphthon/
│   └── output/
└── 02-meeting-minutes/
    ├── source/original/
    ├── data/
    ├── docs/meeting-minutes/
    └── output/
```

실험마다 입력, 지식 문서, 데이터와 결과를 함께 찾을 수 있다는 점은 기존의 전역 `projects/`, `docs/experiments/`, `data/` 분산보다 사용 맥락에 따라 더 이해하기 쉽다.

### 회의록 실험은 실제 자료와 산출물을 갖췄다

- 원본 9개가 `tasks/02-meeting-minutes/source/original/`에 있다.
- 두 회의록 문서는 OKF frontmatter와 `type`을 갖는다.
- 별도 LinkedIn 산출물을 `output/`에 둔 것은 Wiki 문서와 배포용 결과의 역할 구분에 부합한다.

### 일부 이동은 blob 단위로 보존됐다

현재 HEAD에서 삭제로 보이는 47개 중 11개는 현재 파일 트리의 동일 blob으로 찾을 수 있다.

- `docs/experiments/experiment-log.md` → `docs/experiment-log.md`
- Ralph 문서 4개 → `tasks/01-ralphthon/docs/ralphthon/`
- shell script 3개 → `src/scripts/ralphthon/original/` 및 task 문서 경로
- 이전 `projects/.../ralphthon_src/...`의 shell script 3개 → 현재 동일 blob 존재

## P0 — 커밋 차단 결함

### 1. 이전 구조 개편에서 누락된 Ralphthon 원본 42개가 전혀 복구되지 않았다

`7024b1b^`의 다음 세 경로에는 42개의 추적 blob이 있었다.

| 분류 | 개수 |
| --- | ---: |
| fixtures | 18 |
| Python package | 10 |
| tests/evaluators | 14 |
| **합계** | **42** |

현재 filesystem 전체를 blob hash로 대조한 결과 보존된 항목은 **0/42**다. 이전 감사에서 집계한 41개보다 하나 늘어난 이유는 이번에는 basename이 아니라 전체 blob을 엄격히 비교해 `__init__.py`와 중복 prompt까지 포함했기 때문이다.

이는 이번 `tasks/` 재편이 새로 삭제한 자료는 아니지만, “정리 완료”를 선언하려면 반드시 해결해야 하는 상속 결함이다.

### 2. 이번 재편에서 추가로 tracked blob 36개가 목적지 없이 사라진다

현재 HEAD 대비 삭제 예정 47개 중 11개만 동일 blob으로 보존됐다. 나머지 36개는 현재 파일 트리 어디에서도 찾을 수 없다.

| 분류 | 개수 | 예시 |
| --- | ---: | --- |
| `.codex` agents/skills/assets/scripts | 29 | `auto-research`, review-agent, schema와 runner |
| `.omx` context/plans | 3 | Track 2 context와 설계 계획 |
| 프로젝트 관리 문서 | 3 | `README.md`, `checklist.md`, `execution-log.md` |
| 실험 index | 1 | 기존 `docs/experiments/index.md` |
| **합계** | **36** | |

중복 nesting을 없애기 위해 `.codex/.codex`, `.omx/.omx`를 삭제하는 것 자체는 맞지만, 내부 자산을 `tasks/01-ralphthon/source/` 또는 다른 canonical 경로로 옮기지 않고 삭제하면 안 된다.

### 3. 새 구조 전체가 미추적이라 Git은 rename으로 보호하지 못한다

`tasks/01-ralphthon`과 `tasks/02-meeting-minutes`의 파일이 모두 `??` 상태다. 지금 일부만 stage하거나 commit하면 대량 삭제만 들어가거나 원본과 산출물이 불완전하게 기록될 위험이 있다. migration manifest와 path별 staging 검증 전에는 `git add -A`를 사용하면 안 된다.

## P1 — 구조·문서 정합성 결함

### `README.md`와 두 `AGENTS.md`가 새 `tasks/` 구조를 설명하지 않는다

- README tree는 여전히 `docs/experiments/`, 전역 `data/`, `assets/` 중심의 이전 구조다.
- README의 회의록 output 링크도 삭제된 `docs/experiments/meeting-minutes/`를 가리킨다.
- 루트 AGENTS는 `projects/ralph-loop/`, `docs/experiments/...`, `tests/`, `data/fixtures/`를 현재 구조라고 설명한다.
- `docs/AGENTS.md`도 삭제된 `docs/experiments/ralph-loop/`와 `meeting-minutes/`를 규칙 대상으로 둔다.
- task별 로컬 `AGENTS.md`/`CLAUDE.md` 또는 최소 README/schema가 없어 `source`, `docs`, `output`의 변경 가능성 계약이 없다.

### `docs/index.md`가 루트 Docs index가 아니라 과거 Experiments index 내용으로 덮였다

frontmatter title과 본문이 `Experiments Index`, `Experiments Directory`이고 다음 문제가 있다.

- `guide/`, `reference/`, `notes/`, `templates/`의 정상 색인이 사라졌다.
- 존재하지 않는 model-tests, benchmarks, usage-studies, integration-tests 링크가 남았다.
- meeting-minutes를 “아직 시작되지 않음”으로 표시하지만 실제 결과가 존재한다.
- `tasks/...` 링크는 `docs/` 기준 `../tasks/...`가 아니라 `tasks/...`여서 깨진다.
- reference 링크도 `docs/reference/...`가 아닌 `reference/...`여야 하는데 일부 맥락이 섞였다.

### `docs/log.md`와 문서 metadata가 옛 경로를 기록한다

- 회의록 output을 `docs/experiments/meeting-minutes/...`라고 기록하지만 실제 위치는 `tasks/02-meeting-minutes/docs/meeting-minutes/...`다.
- 입력을 `_inbox/`라고만 기록하고 현재 canonical source 경로를 남기지 않았다.
- Ralph experiment log를 `tasks/01-ralphthon/docs/ralphthon/experiment-log.md`라고 바꿨지만 실제 파일은 `docs/experiment-log.md`다.
- 구조 재편 자체에 대한 migration 기록, blob 보존 수치와 이동표가 없다.
- 7월 19일 로그 중복도 그대로다.

### 회의록 문서의 `input_sources`가 존재하지 않는 `projects/meeting-minutes/...`를 가리킨다

실제 source는 `tasks/02-meeting-minutes/source/original/`이다. 두 회의록의 provenance metadata가 현재 canonical source와 연결되지 않아 OKF 지식 문서의 추적성이 깨진다.

### task 디렉터리에 탐색 진입점과 규칙이 없다

두 task root 모두 `README.md`, `index.md`, `log.md`, `AGENTS.md`가 없다. 빈 `source/`, `data/`, `output/` 디렉터리의 의미도 문서화되지 않았다. 최소한 task root index 또는 README가 있어야 상위 index에서 단계적으로 탐색할 수 있다.

### 실행 script가 `docs/` 안에 남아 있다

`tasks/01-ralphthon/docs/ralphthon/`에는 shell script 3개가 있다. 동시에 같은 blob이 `src/scripts/ralphthon/original/`에도 있어 canonical source가 중복된다. task의 `docs/`는 Wiki Markdown으로 한정하고 실행 파일은 `source/` 또는 `src/` 중 하나에만 두는 편이 맞다.

## OKF·링크 lint

### OKF

Wiki 후보 Markdown 40개 중 13개가 자체 규칙상 frontmatter 문제를 가진다.

- Schema 예외 검토 대상 2개: `docs/AGENTS.md`, `docs/CLAUDE.md`
- 실제 콘텐츠 위반 11개: `docs/log.md`, guide/reference/notes index 9개, task 01 index 1개

Schema 파일에 OKF를 적용하지 않겠다면 AGENTS에서 “모든 `docs/*.md`”가 아니라 명시적 예외를 정의해야 한다. 콘텐츠 index와 log는 OKF 예약 문서이므로 frontmatter를 갖추는 것이 타당하다.

### 링크

code fence와 명백한 placeholder를 제외한 검사에서 잠재 broken link 44개가 나왔다. 이 중 새 구조와 직접 관련된 항목은 다음과 같다.

- `docs/index.md`의 task 링크 2개
- `tasks/02.../index.md`의 experiment log와 root log 링크 2개
- `tasks/01.../index.md`의 사라진 `checklist.md`
- Ralph 비교 문서의 옛 experiments, notes, src 상대 경로
- notes에서 옮겨진 Ralph 문서와 전역 guide/reference/template로 가는 잘못된 상대 경로

기존 placeholder와 작성 예정 문서도 섞여 있으므로 44개를 모두 같은 오류로 자동 수정해서는 안 된다. 실제 링크, 계획 placeholder, archive 역사 링크로 분류한 뒤 처리해야 한다.

## 검증 결과

| Gate | 결과 | 증거 |
| --- | --- | --- |
| 이전 Source blob 보존 100% | FAIL | `0/42` |
| 이번 tracked 삭제 보존 | FAIL | `11/47` 보존, `36/47` 누락 |
| canonical source 중복 0 | FAIL | shell script 3개가 docs와 src에 중복 |
| Wiki OKF 위반 0 | FAIL | 13개, schema 예외 제외 시 11개 |
| broken internal link 0 | FAIL | 잠재 44개 |
| 문서-트리 일치 | FAIL | README, AGENTS, index, log 모두 불일치 |
| shell syntax | PASS | 현재 대상 `zsh -n` 통과 |
| `git diff --check` | PASS | whitespace 오류 없음 |
| clean migration 상태 | FAIL | 대량 D + `tasks/` 전체 미추적 |

## 수정 우선순위

1. `7024b1b^`에서 42개 Source blob을 `tasks/01-ralphthon/source/` 아래의 명확한 원형 구조로 복구한다.
2. HEAD에서 삭제 예정인 `.codex`, `.omx`, 프로젝트 관리 문서 35개를 task 01의 source·project-doc 위치로 blob 보존 이동한다. 과거 experiments index는 새 root index에 의미를 흡수했는지 별도로 판단한다.
3. task 01의 shell script canonical 위치를 하나로 정한다. `docs/` 안의 script는 제거하되 canonical copy 보존을 먼저 검증한다.
4. task root에 README 또는 OKF `index.md`를 만들고 Source/Wiki/Output 변경 계약을 기록한다.
5. README, root AGENTS, docs AGENTS를 실제 `tasks/` 구조에 맞춰 함께 갱신한다.
6. `docs/index.md`를 Docs bundle index로 다시 작성하고 task 링크는 `../tasks/...`로 고친다.
7. 회의록 `input_sources`, docs log와 각 task index의 상대 경로를 실제 위치로 수정한다.
8. 콘텐츠 index/log의 frontmatter를 보강하고 schema 예외 정책을 명시한다.
9. 링크를 실제/placeholder/archive로 분류해 실제 broken link만 고친다.
10. migration manifest로 `7024b1b^`, HEAD, 최종 worktree의 blob 보존을 검증한 뒤 pathspec 단위로 stage한다.

## Solar Open 2에 줄 다음 지시의 핵심

이 상태에서는 이전의 일반 복구 프롬프트보다 다음 세 수치를 명시해야 한다.

- 상속 누락: `7024b1b^` Source blob **42개, 현재 보존 0개**
- 이번 삭제: HEAD tracked 삭제 **47개 중 36개 현재 미보존**
- 보호 대상: 현재 `tasks/02-meeting-minutes/source/original/` 9개와 생성 결과 전부

Solar에는 먼저 read-only 복구 매핑을 다시 만들게 하고, 각 old blob이 최종 어느 경로에 놓일지 78개 항목(42+36)을 전부 제시하기 전에는 실행을 승인하지 않는 것이 안전하다. 동일 blob이 겹치는 항목은 manifest에서 alias로 표시하되 파일 보존율 계산을 임의로 낮추면 안 된다.

## 관련 문서

- [1차 구조 개편 감사](upstage-okf-restructure-review-2026-07-22.md)
- [`_Upstage` 작업 내역 정리 — 2026년 7월 20일까지](upstage-work-summary-through-2026-07-20.md)
- [저장소 색인](../../index.md)
- [관찰 로그](../../log.md)
