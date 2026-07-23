---
title: _Upstage LLM-Wiki·OKF 구조 개편 전후 및 Solar Open 2 평가
type: Project Review
description: 2026년 7월 22일 구조 개편 커밋의 전후 변화, wiki-organize skill 준수도, 결함과 대안 구조를 기록한 감사 보고서
tags: [solar-open2, llm-wiki, okf, wiki-organize, repository-structure, review]
timestamp: 2026-07-22T23:05:27+09:00
status: reviewed-with-critical-findings
source_workspace: _Upstage
source_branch: main
source_commit_before: 7024b1b^
source_commit_after: 7024b1b
source_commit_current: 6e8c5d1
---

# `_Upstage` LLM-Wiki·OKF 구조 개편 전후 및 Solar Open 2 평가

## 결론

2026년 7월 22일의 `7024b1b`는 `_Upstage`를 **Source / Wiki / Schema의 세 계층으로 나누려는 방향**, `AGENTS.md` 중심의 도구 중립 규칙, `CLAUDE.md`의 단일 import, `_inbox/`, `general-notes/` 명명 개선 등에서 `wiki-organize` skill의 핵심 개념을 잘 포착했다.

그러나 결과물은 **구조 설계 초안으로는 양호하지만, 완료된 재구조화로는 불합격**이다. 커밋 메시지와 실제 변경이 일치하지 않고, 5,657줄 규모의 소스·테스트·fixture가 새 Source 계층으로 이동되지 않은 채 삭제됐다. README와 인덱스도 실제 트리와 맞지 않으며, OKF frontmatter와 링크 lint가 완료되지 않았다.

평가를 한 문장으로 요약하면 다음과 같다.

> Solar Open 2는 skill의 **개념과 겉구조는 잘 적용했지만**, skill이 요구하는 **보존성, 사용자 확인, 로그 연속성, lint와 완료 검증**을 끝까지 수행하지 못했다.

전체 평점은 **48/100**이다. 다만 이는 모델 자체의 일반 성능 점수가 아니라, Git에 남은 이번 구조 개편 산출물을 skill 계약에 대조한 점수다.

## 평가 범위와 증거 수준

### 기준점

- 변경 전: `7024b1b^` (`dc64e34`)
- 구조 개편: `7024b1b` — `docs: restructure workspace for LLM-Wiki/OKF conformance and Solar Open2 agent experimentation`
- 감사 시점 HEAD: `6e8c5d1`
- 감사 시점 worktree: `_inbox/`, `docs/experiments/meeting-minutes/`가 미추적 상태
- skill 기준: [`changwonjeon/skill-okf-llmwiki`](https://github.com/changwonjeon/skill-okf-llmwiki)의 `.claude/skills/wiki-organize/SKILL.md`

### 귀속의 한계

Git commit에는 작성 모델의 식별 정보나 skill invocation log가 없다. 따라서 **Solar Open 2가 실제로 해당 skill을 호출했다는 사실은 Git만으로 독립 검증할 수 없다.** 이 보고서는 사용자의 설명을 작업 출처로 받아들이되, 점수는 모델의 자기보고가 아니라 파일 트리, diff, 문서 내용과 링크 상태만으로 계산한다.

## 구조 변경 전

변경 전에는 문서 위키인 `docs/experiments/ralphthon/` 안에 다음 성격의 자료가 함께 들어 있었다.

- 위키 문서: 실험 개요, 비교 문서, context notes
- 실행 소스: Python package와 shell script
- 검증 코드: unit/integration test와 evaluator
- fixture: frozen manifest, evaluation contract, mock paper와 JSON evidence
- 에이전트 자산: `.codex/agents`, `.codex/skills`
- 실행 계획과 context: `.omx/plans`, `.omx/context`

또한 `docs/notes/notes/`처럼 의미가 중복되는 경로가 있었고, 에이전트 규칙은 루트 `CLAUDE.md`에만 집중돼 있었다. 즉 `docs/`가 지식 문서와 실행 가능한 원본을 동시에 담는 구조라서 LLM-Wiki의 불변 Source 계층과 가변 Wiki 계층이 구분되지 않았다.

```text
_Upstage/
├── .claude/skills/                 # Solar 실행용 프로젝트 skill
├── docs/
│   ├── experiments/
│   │   └── ralphthon/
│   │       ├── 문서
│   │       ├── fixtures/
│   │       ├── src/
│   │       ├── tests/
│   │       ├── .codex/
│   │       └── .omx/
│   ├── notes/notes/
│   └── guide, reference, templates
├── src/scripts/ralphthon/           # Solar 적응 실행 코드
├── CLAUDE.md                       # 규칙 본문
└── README.md
```

## 구조 변경 후

### 의도된 변화

| 영역 | 변경 전 | 변경 후 의도 | 판단 |
| --- | --- | --- | --- |
| Schema | `CLAUDE.md`에 규칙 본문 | 루트·`docs/`의 `AGENTS.md`, 한 줄짜리 `CLAUDE.md` | 좋음 |
| Wiki | 코드·fixture와 문서 혼재 | `docs/`를 OKF Markdown 지식 번들로 한정 | 방향 좋음 |
| Source | 명시적 원본 계층 없음 | `projects/`, `src/`, `tests/`, `data/fixtures/` | 방향 좋음 |
| 일반 노트 | `docs/notes/notes/` | `docs/notes/general-notes/` | 좋음 |
| 전달 경로 | 없음 | `_inbox/` | 좋음 |
| Ralph 자료 | `docs/experiments/ralphthon/` 아래 혼재 | 원본은 `projects/ralph-loop/`, 위키는 `docs/experiments/ralphthon/` | 부분 성공 |

### Git에서 실제 확인된 변화

- `AGENTS.md`, `docs/AGENTS.md`, `docs/CLAUDE.md`가 생겼다.
- 루트 `CLAUDE.md`는 정확히 `@AGENTS.md` 한 줄로 바뀌었다.
- `docs/notes/notes/`의 3개 파일은 `docs/notes/general-notes/`로 100% rename됐다.
- `.codex`와 `.omx` 자료는 `projects/ralph-loop/`로 100% rename됐다.
- `checklist.md`, `execution-log.md`도 `projects/ralph-loop/`로 이동했다.
- 원본 shell script 3개가 `src/scripts/ralphthon/original/`과 `projects/ralph-loop/ralphthon_src/.../original/` 두 곳에 추가됐다.
- 추적 파일 수는 133개에서 101개로 줄었다.
- 구조 개편 직후 93개 파일에 변화가 있었고, diff는 375줄 추가·5,657줄 삭제였다.

## 잘한 점

### 1. 세 계층의 개념을 프로젝트 성격에 맞게 번역했다

개발과 실험을 함께 하는 저장소이므로 단순한 `sources/`와 `wiki/`만 두지 않고 `projects/`, `src/`, `tests/`, `data/fixtures/`를 Source 계층으로 설명한 판단은 합리적이다. 코드 프로젝트에서는 원본·실행 자산을 하나의 폴더로 억지로 몰아넣는 것보다 이 구분이 실용적이다.

### 2. 규칙의 단일 출처를 만들었다

skill이 요구한 대로 실제 규칙을 `AGENTS.md`에 두고 `CLAUDE.md`는 `@AGENTS.md`만 참조한다. `docs/`에 로컬 규칙 쌍을 둔 것도 도구 중립성과 범위별 지침이라는 목적에 맞는다.

### 3. 의미가 중복된 경로를 개선했다

`docs/notes/notes/`를 `docs/notes/general-notes/`로 바꾼 것은 사람이 보거나 에이전트가 탐색할 때 모두 명확하다. 관련 README 링크 한 곳도 함께 수정했다.

### 4. 전달용 inbox와 사람/에이전트 문서의 역할을 인식했다

`_inbox/`를 구조와 규칙에 명시했고 README와 AGENTS의 독자 차이를 구분했다. 현재 실제 미추적 자료가 `_inbox/`에 들어온 점을 보면 전달 경로라는 개념도 작동하고 있다.

## 핵심 결함

### P0 — 보존해야 할 원본 41개 이상이 이동되지 않고 삭제됐다

커밋 설명은 코드, 테스트와 fixture를 `projects/ralph-loop/`, `src/`, `tests/`, `data/fixtures/`로 옮겼다고 주장한다. 그러나 실제로는 다음 자료가 새 위치에서 발견되지 않는다.

- fixture 18개: freeze·manifest·calibration·quality·throughput 자료
- Python 구현 7개: `calibration.py`, `contract.py`, `identity.py`, `io_utils.py`, `ledger.py`, `manifest.py`, `runtime.py`
- test/evaluator 16개: evaluator, support, mock evidence runner와 unit/integration test

이 자료는 Git history에서 복구할 수 있으므로 물리적으로 영구 소실된 것은 아니다. 하지만 **불변 원본을 보존한다는 LLM-Wiki 핵심 계약**, skill의 “원본 자료는 절대 수정·이동하지 않는다”는 원칙, commit message의 “move” 설명과 모두 충돌한다.

### P0 — 대량 재구조화의 승인·매핑·검증 증거가 없다

skill은 재구조화 시 가벼운 인터뷰, 새 구조안 제시, 대량 이동 전 이동 매핑과 사용자 확인을 요구한다. 저장소에는 승인 기록, 확정 이동표, dry-run 결과 또는 사후 manifest가 없다. 대화 로그가 별도로 존재할 가능성은 있지만, Git 산출물만으로는 이 필수 절차를 입증할 수 없다.

### P1 — “OKF frontmatter 10개 추가”라는 commit 설명이 실제 diff와 다르다

구조 개편 diff에서 frontmatter가 새로 추가된 index는 `docs/experiments/index.md` 1개만 확인된다. 감사 시점의 `docs/` Markdown 중 다음 14개는 첫 줄이 `---`가 아니다.

- `docs/index.md`, `docs/log.md`
- `docs/guide/index.md`, `docs/reference/index.md`
- `docs/notes/index.md`와 6개 notes 하위 index
- `docs/experiments/ralphthon/index.md`
- `docs/AGENTS.md`, `docs/CLAUDE.md`

여기에는 schema import 파일도 포함돼 OKF 적용 범위를 어떻게 정의할지 논의할 여지는 있다. 그러나 현재 `AGENTS.md`가 “`docs/`의 모든 `.md`”에 frontmatter를 요구하므로 자체 규칙 기준으로는 14건 위반이다. 특히 OKF bundle의 핵심인 `docs/index.md`와 `docs/log.md` 누락은 명백하다.

### P1 — 실제 구조와 문서화가 서로 다르다

- README 트리는 여전히 `docs/notes/notes/`를 표시한다.
- README에는 `projects/`, `_inbox/`, `AGENTS.md`, `docs/AGENTS.md`가 반영되지 않았다.
- README에는 `docs/experiments/ralphthon/`과 현재 script 구성이 충분히 반영되지 않았다.
- `AGENTS.md`는 `docs/experiments/ralph-loop/`를 적었지만 실제 경로는 `ralphthon/`이다.
- `AGENTS.md`가 명시한 `docs/templates/index.md`는 존재하지 않는다.
- `AGENTS.md`가 Source 계층으로 열거한 `tests/`, `data/fixtures/`에는 감사 시점에 파일이 없다.

### P1 — 이동 결과에 중복 nesting과 중복 script가 생겼다

원래 `ralphthon/.codex/...`를 `projects/ralph-loop/.codex/`로 옮기는 과정에서 실제 경로가 `projects/ralph-loop/.codex/.codex/...`가 됐다. `.omx`도 `.omx/.omx/...`가 됐다. 이는 이동 destination 계산에서 basename을 중복 삽입한 흔적으로 보인다.

동일한 원본 shell script 3개는 `src/scripts/ralphthon/original/`과 `projects/ralph-loop/ralphthon_src/scripts/ralphthon/original/` 양쪽에 추가됐다. 어느 쪽이 canonical source인지 규칙이 없어 이후 drift가 발생할 수 있다.

### P1 — lint를 완료하지 않았다

단순 Markdown 상대 링크 검사에서 47개가 존재하지 않는 대상으로 나왔다. 예시 placeholder와 Source 계층 안의 과거 링크를 포함한 수치라 모두 같은 심각도는 아니지만, 다음은 실제 구조 변경과 직접 관련된 결함이다.

- `docs/index.md` → 존재하지 않는 `templates/index.md`
- `docs/experiments/index.md` → 존재하지 않는 `ralphthon/index.md` 표기 오류(실제 하위 index와의 경로 상태도 재검증 필요)
- `docs/notes/index.md` → 옛 `notes/index.md`
- Ralph 비교 문서 → 옛 `notes/notes/` 및 잘못된 `../src/` 상대 경로
- 여러 notes 문서 → 실제 위치보다 한 단계 얕은 `../guide`, `../reference`, `../templates` 링크

### P1 — 구조 개편이 `docs/log.md`에 기록되지 않았다

skill은 기존 `log.md`를 이어 쓰고 구조 변경 자체를 기록하라고 한다. `docs/index.md`에는 짧은 변경 설명이 있지만 `docs/log.md`에는 7월 22일 구조 개편 항목이 없다. 변경 이력의 연속성이 끊겼다.

### P2 — Source 불변성 규칙이 서로 모순된다

skill의 재구조화 절차는 사용자 승인 후 기존 파일을 새 위치로 옮기라고 하지만, 마지막 원칙은 원본을 절대 이동하지 말라고 한다. 결과물의 `AGENTS.md`도 `src/scripts/ralphthon/original/`을 불변 원본이라 부르면서 이번 commit에서 원본을 새로 복제했다. 구조 전환기에만 허용되는 migration 규칙과 정상 운영기의 immutability 규칙을 구분했어야 한다.

## skill 준수도 평가

| 평가 항목 | 배점 | 점수 | 근거 |
| --- | ---: | ---: | --- |
| Source / Wiki / Schema 계층 설계 | 20 | 16 | 개념과 역할은 잘 분리했으나 실제 migration 누락 |
| AGENTS·CLAUDE schema 패턴 | 15 | 13 | 단일 출처와 로컬 규칙 적용, 경로명 불일치 존재 |
| README·inbox·사용자 안내 | 10 | 5 | inbox는 도입, README는 거의 갱신되지 않음 |
| 원본 보존과 migration 안전성 | 20 | 2 | 41개 이상이 목적지 없이 삭제, 중복 경로 생성 |
| OKF conformance | 15 | 5 | 일부 문서는 준수하지만 핵심 index/log 포함 14건 위반 |
| Ingest / Query / Lint 운영성 | 10 | 3 | 규칙 일부만 문서화, lint 결과 미해결 |
| 변경 이력·정확한 보고 | 10 | 4 | commit message 과장, `docs/log.md` 미기록 |
| **합계** | **100** | **48** | **부분 준수, 완료 판정 불가** |

## 내가 했다면 적용할 구조

핵심 차이는 **문서 분류보다 먼저 보존 manifest와 검증 gate를 만든다**는 점이다. 실제 `_Upstage`에는 적용하지 않았으며 아래는 문서상 제안이다.

```text
_Upstage/
├── _inbox/                         # 아직 분류하지 않은 전달 자료
├── sources/                        # 외부·이식 원본, 불변
│   └── ralphthon-codex/
│       ├── fixtures/
│       ├── package/                # Python 구현
│       ├── tests/
│       ├── agent-config/           # .codex, .omx 원형 보존
│       └── manifest.md             # 출처 commit, hash, 이동표
├── projects/
│   └── ralph-loop/                 # 현재 실험의 project-local 설정과 실행 계획
│       ├── README.md
│       └── config/
├── src/
│   └── ralphthon/                  # 현재 유지·개발하는 Solar 적응 코드만
├── tests/
│   └── ralphthon/                  # 현재 코드용 test
├── data/
│   ├── fixtures/ralphthon/         # 실행 입력; sources manifest에서 유래 표시
│   └── results/ralphthon/           # 생성 결과, ignore 정책 명시
├── docs/                            # OKF Wiki bundle
│   ├── index.md
│   ├── log.md
│   ├── AGENTS.md
│   ├── CLAUDE.md
│   ├── guides/
│   ├── reference/
│   ├── experiments/
│   │   ├── index.md
│   │   ├── ralph-loop/
│   │   └── meeting-minutes/
│   ├── entities/                   # people, models, papers, projects
│   └── notes/                      # general notes와 writing
├── assets/
├── AGENTS.md
├── CLAUDE.md
├── README.md
└── pyproject.toml
```

### 이 구조를 택하는 이유

1. **원본과 현재 제품 코드를 분리한다.** `sources/ralphthon-codex/`는 그대로 보존하고 `src/ralphthon/`에는 실제로 고치는 Solar 적응 코드만 둔다.
2. **canonical copy를 하나만 둔다.** 원본 shell script를 두 위치에 복제하지 않는다. 실행에 필요하면 symlink보다 명시적 import/copy script와 hash 검증을 사용한다.
3. **Wiki 안에는 Markdown 지식만 둔다.** 실행 소스, fixture, hidden tool state를 `docs/`에 넣지 않는다.
4. **실험 명칭을 하나로 통일한다.** 폴더는 `ralph-loop`로 정하고 “Ralphthon”은 이벤트·프로젝트 별칭으로 metadata에 남긴다.
5. **문서 카테고리를 콘텐츠 양에 맞게 줄인다.** 비어 있는 People/Papers/Writing 하위 트리를 미리 강제하기보다 실제 페이지가 생길 때 index와 함께 만든다.

## 내가 적용했을 migration 절차

### 1. 변경 전 inventory 동결

- `git ls-tree -r --name-only <before>`로 전체 파일 목록 저장
- 각 파일의 blob hash, 크기, 분류 후보를 migration manifest에 기록
- Source 후보는 삭제·내용 변경 금지로 표시

### 2. 사용자 확인용 이동표 작성

| 기존 경로 | 제안 경로 | 방식 |
| --- | --- | --- |
| `docs/experiments/ralphthon/fixtures/` | `sources/ralphthon-codex/fixtures/` | `git mv` |
| `docs/experiments/ralphthon/src/...` | `sources/ralphthon-codex/package/` | `git mv` |
| `docs/experiments/ralphthon/tests/` | `sources/ralphthon-codex/tests/` | `git mv` |
| `docs/experiments/ralphthon/.codex/` | `sources/ralphthon-codex/agent-config/.codex/` | `git mv` |
| `docs/experiments/ralphthon/.omx/` | `sources/ralphthon-codex/agent-config/.omx/` | `git mv` |
| 위키 문서 | `docs/experiments/ralph-loop/` | 내용별 `git mv` |
| `docs/notes/notes/` | `docs/notes/` 또는 `docs/notes/general/` | `git mv` |

이 표와 예상 삭제 목록이 비어 있음을 사용자에게 보여준 뒤에만 실행한다.

### 3. 한 commit에 한 종류의 변화

1. 순수 rename/move commit
2. 링크와 README·AGENTS 경로 수정 commit
3. OKF frontmatter·index·log 보강 commit
4. lint와 manifest 검증 commit

이렇게 나누면 rename이 삭제로 오인되는 문제와 내용 변경을 쉽게 감사할 수 있다.

### 4. 완료 gate

- 변경 전 Source blob 수 = 변경 후 Source blob 수
- 의도하지 않은 `D` 상태 0개
- 모든 Wiki `.md`에 parse 가능한 frontmatter와 `type`
- 모든 실제 디렉터리와 README·AGENTS tree 일치
- 내부 상대 링크 0개(템플릿 placeholder는 code fence 제외)
- 각 Wiki 디렉터리 index가 실제 파일 목록과 일치
- 구조 변경 항목이 `docs/log.md`에 존재
- 중복 hash는 승인된 경우만 존재
- clean worktree에서 test와 lint 실행 결과를 report에 첨부

## 권고 우선순위

실제 폴더를 고치기로 결정한다면 순서는 다음이 안전하다.

1. **P0:** `7024b1b^`에서 삭제된 fixture·Python source·test 41개 이상을 목적이 명확한 Source 경로로 복구한다.
2. **P0:** `.codex/.codex`, `.omx/.omx` 중복 nesting과 shell script 이중 canonical copy를 정리한다.
3. **P1:** README와 두 AGENTS의 트리를 실제 경로에 맞춘다. `ralph-loop`와 `ralphthon` 중 하나를 선택한다.
4. **P1:** `docs/index.md`, `docs/log.md`와 각 index의 OKF frontmatter를 보강하고 `docs/templates/index.md`를 만들거나 링크를 제거한다.
5. **P1:** 구조 변경으로 깨진 상대 링크를 수정한 후 lint를 다시 실행한다.
6. **P1:** `docs/log.md`에 구조 개편과 복구 내역, before/after commit을 기록한다.
7. **P2:** migration 시의 예외와 정상 운영 시 Source 불변성을 AGENTS에 각각 명시한다.

## 최종 판단

Solar Open 2 결과물은 `wiki-organize` skill을 피상적으로 복사한 수준은 아니다. 프로젝트가 개발+실험형이라는 점을 반영해 계층을 변형했고, schema 파일 패턴과 inbox, 일반 노트 명명까지 실제 구조에 적용했다. 이 부분에서는 **skill 이해력과 구조 설계력은 확인된다.**

반면 지식 관리 구조에서 가장 중요한 것은 예쁜 트리가 아니라 **원본 보존, 탐색 가능성, 링크 무결성, 변경 이력과 검증 가능한 보고**다. 이번 결과는 이 네 가지에서 큰 결함이 있으므로 “컨셉에 맞게 잘 정리하고 구조를 바꿨다”고 최종 승인하기 어렵다. 적절한 판정은 **방향은 적합, migration은 실패, 복구 후 재검증 필요**다.

## 관련 문서

- [`_Upstage` 작업 내역 정리 — 2026년 7월 20일까지](upstage-work-summary-through-2026-07-20.md)
- [Ralph Loop 스킬 설계와 실행 전 검증](ralph-skill-stack-validation.md)
- [저장소 색인](../../index.md)
- [관찰 로그](../../log.md)
