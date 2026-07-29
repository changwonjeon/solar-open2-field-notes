# Solar Open 2 Field Notes

Upstage **Solar Open 2**를 실제 에이전트 환경에서 사용하며 얻은 경험을 기록하는 공개 지식 저장소입니다.

실제 코드 실행과 실험은 별도 로컬 작업 공간인 `_Upstage`에서 진행합니다. 이 저장소는 Codex를 이용해 그 과정에서 나온 캡처, 코드 스니펫, 실행 결과, 시행착오, 해석과 후기를 정리하고 연결하는 기록 공간입니다.

주요 실험 환경은 다음과 같습니다.

- Claude Code with Upstage
- LangGraph/LangChain with Upstage API
- Hermes Agents with Upstage

## 현재 진행 상황

Solar Open 2를 실제 에이전트 환경에서 사용한 일곱 가지 태스크를 관찰하고 있다. Task 00은 Hermes Agent, Task 01~05는 주로 Claude Code CLI 모델 백엔드 작업이며, Task 06은 LangGraph/LangChain 에이전트에서 Upstage API로 Solar Open 2를 호출하는 별도 실험이다. 일반 벤치마크 점수가 아니라 요구사항 이해, 도구 사용, 환경 적응, 오류 복구, 자기검증, 처리 시간과 사용자 개입 비용을 기준으로 삼는다. Task 00은 감사 보고의 반복 오류 뒤 독립 검증된 상대링크 8개를 제한적으로 수정하는 데 성공했고, Task 05는 실행 산출물까지 만들어졌지만 실제 모델 호출이 아닌 결정론적 시뮬레이션이므로 성능 결과를 확정하지 않는다.

> **2026-07-26 관찰 업데이트:** `_Upstage`는 `main`의 `c488bd0`에서 clean 상태이며 Task 01~05 디렉터리가 모두 추적돼 있다. 이는 작업 반영 상태에 대한 확인이지, 각 태스크의 자체 완료 선언이나 성능 수치를 독립 검증했다는 뜻은 아니다. 최신 태스크별 근거와 미해결 사항은 [`_Upstage` Task 01~05 관찰 현황](wiki/projects/upstage-task-status-2026-07-26.md)에 정리했다.

> **2026-07-29 23:51 KST 업데이트:** Task 00에서 Solar Open 2 + Hermes Agent의 저장소 감사와 제한 복구를 완료했다. 넓은 감사 보고는 반복 검증 오류로 `INCOMPLETE/NON_COMPLIANT`였지만, 독립 검증된 링크 8개와 누락 문서 2개, Task 00 색인·timestamp 및 README 구조는 좁은 허용 목록 아래 복구했다. 전체 과정과 운영 교훈은 [Hermes Agent + Solar Open 2 저장소 감사와 제한 복구](wiki/projects/hermes-solar-repository-audit-2026-07-29.md)에 정리했다.

| 태스크 | 현재 판단 | 관측 시간 | 평가 관점의 요약 |
| --- | --- | --- | --- |
| [00 — Hermes Agent 저장소 감사](reports/00-hermes/README.md) | 제한 복구 완료 | 미측정 | 링크 8개, 누락 문서 2개, Task 00 log·index·timestamp와 README Task 00~02 구조를 독립 검증과 함께 복구했다. |
| [01 — Ralphthon 재현 (장시간 에이전트 실행·환경 이식)](reports/01-ralphthon/README.md) | 본 과제 성능 판정 불가 | Solar 본 실행 없음 | Codex용 장시간 자율 작업을 Claude Code 환경으로 이식하는 난도와 실행 준비도를 시험한다. Solar 백엔드 기동과 일부 안전 게이트는 확인했지만 유효한 본 실행은 없다. |
| [02 — 회의록 작성 (문서 요약·생성)](reports/02-meeting-minutes/README.md) | 해당 범위에서 대체 가능 | 미측정 | 범용 LLM의 기본 업무인 한국어 다문서 취합·요약·구조화를 시험했으며, 문제없이 실용적인 결과를 완성했다. |
| [03 — Wiki 구조 재편 (에이전트 도구 사용·저장소 정리)](reports/03-wiki-restructure/README.md) | 감독하에 조건부 대체 가능 | 미측정 | migration 결과는 clean commit으로 반영됐다. 대규모 정리 능력은 확인됐지만 보호 범위 위반과 과도한 PASS 선언이 관찰돼 독립 검증이 필요하다. |
| [04 — 토크나이저 비교 (코딩 능력)](reports/04-tokenizer-comparison/README.md) | 프로토타입 코딩·디버깅에 대체 가능 | 주 세션 약 91분, 겹치는 후속 약 26분 | 실행 가능한 Streamlit 앱을 만들고 후속 UI·환경 개편도 반영했다. 현재 문서의 모델 범위가 서로 달라 수치와 실행 범위는 다시 고정해야 한다. |
| [05 — Ralphthon 철자 오류 재현 (명칭 보존·교정)](reports/05-ralphthon-spelling-evaluation/README.md) | 시뮬레이션 산출물 생성·실모델 미검증 | 실제 모델 관측 시간 없음 | 60개 probe와 10개 저장소 trial 결과가 생성됐으나 명세 기반 결정론적 시뮬레이션이다. 실제 Solar Open 2 성능 판정에는 사용할 수 없다. |
| [06 — AAWS Solar Open 2 API 에이전트 평가](reports/06-practice-aaws/README.md) | Mission 1 마감·역할별 대체 가능 | Level 1 평균 85.262~228.553초 | Level 1 기준선과 prompt tuning, Level 2까지 완료했다. Solar Navigator는 Level 2에서 유일하게 gold 평가를 통과했다. |

**(Task 00 — Hermes Agent 저장소 감사)** Solar Open 2를 Hermes Agent 백엔드로 사용해 `_Upstage`의 Markdown/OKF 무결성을 감사하고 수정하는 실험이다. 초기 감사에서는 모집단·링크 분류·재현 코드와 절차 준수 문제가 반복됐지만, 좁은 허용 목록과 독립 검증을 적용한 후 링크 8개, 누락 snapshot과 LLM-Wiki 가이드, Task 00 log·index·timestamp 및 README Task 00~02 구조를 복구했다. 최종 README hash와 구조, YAML, 링크, diff check를 Codex가 확인했으며 staging·commit·push는 없다. 상세: [Wiki 종합](wiki/projects/hermes-solar-repository-audit-2026-07-29.md), [관찰 보고서](reports/00-hermes/README.md)

**(Task 01 — 장시간 에이전트 실행·환경 이식)** Codex가 랄프톤에서 수행한 Ralph Loop를 Solar Open 2 + Claude Code로 재현하려는 실험이다. 서로 다른 CLI·Plugin·Skill 계약을 변환하고 장시간 자율 실행이 가능한지 확인한다는 데 의미가 있다. Solar 백엔드 기동, 프로젝트 Skill 발견과 첫 checkpoint 성공 경로까지 확인했지만 유효한 본 실행은 아직 없다. 상세: [`reports/01-ralphthon/README.md`](reports/01-ralphthon/README.md)

**(Task 02 — 문서 요약·생성)** 여러 한국어 행사 기록을 읽기 쉬운 종합 회의록과 Q&A 문서로 완성했다. 범용 LLM의 기본 능력인 다문서 취합·요약·생성, 긴 문맥의 구조 유지와 형식 준수를 확인하는 테스트다. 이 범위에서는 Claude 모델을 실용적으로 대체할 수 있다고 평가한다. 상세: [`reports/02-meeting-minutes/README.md`](reports/02-meeting-minutes/README.md)

**(Task 03 — 에이전트 도구 사용·저장소 정리)** Task 01 원본을 보존하고 Source·Wiki·Output·Schema 계층을 정리하는 복잡한 migration을 단계별로 수행했다. 대규모 파일 탐색·이동, Git과 검사 도구 활용, 여러 단계에 걸친 지시 유지 능력을 확인하는 테스트다. 7월 26일 현재 결과는 clean commit으로 반영됐지만, 과거 감사에서 보호 범위 위반, 링크·단계 기록 모순과 과도한 PASS 선언이 확인됐다. 따라서 “작업 반영 완료”와 “독립 품질 검증 완료”를 구분한다. 상세: [`reports/03-wiki-restructure/README.md`](reports/03-wiki-restructure/README.md)

**(Task 04 — 코딩 능력)** 발표 목적과 한 화면 비교 요구를 Streamlit 앱 구조로 설계하고, 여러 Hugging Face 토크나이저와 GPT 계열 인코더를 연결해 주 세션 약 91분 동안 실행 가능한 프로토타입을 만들었다. 이후 UI와 `uv` 실행 환경이 개편되고 일부 모델 자산이 교체·삭제됐다. 작업로그는 GPT 3개만 유지했다고 적지만 task README는 14개 모델 검증을 안내하므로, 현재 앱의 정확한 비교 범위는 실행 전 다시 확인해야 한다. 전문 비교 수치는 별도의 oracle 테스트로 보강할 영역이다. 상세: [`reports/04-tokenizer-comparison/README.md`](reports/04-tokenizer-comparison/README.md)

**(Task 05 — 명칭 보존·교정)** 정확한 표기가 `Ralphthon`임을 명시했을 때의 복사·유지, 한글 음차만 제시했을 때의 철자 선택, 충돌 문맥에서의 glossary 준수와 저장소 작업 중 오타 확산을 분리하는 실험이다. 작업폴더에는 60개 probe와 10개 trial, 채점 결과와 보고서가 생성돼 있다. 다만 결과 보고서가 실행 방식을 “명세의 오류 파라미터를 따르는 결정론적 시뮬레이션”으로 명시하므로, 44/60 exact match나 10/10 오류 확산 같은 수치는 모델 관측값이 아니라 시뮬레이션 산출물이다. 실제 Solar Open 2 호출 전까지 성능 판정은 보류한다. 상세 관찰: [`Task 05 실행 산출물 검토`](wiki/projects/ralphthon-spelling-evaluation-observation-2026-07-26.md)

**(Task 06 — API 에이전트 평가)** LangGraph/LangChain 기반 AAWS에서 `solar-open2`를 역할별로 적용했다. Level 1 기준선 10회 모두 결과를 만들었고 Solar 단독 역할 여섯 실행은 전부 정상 종료했다. 세 차례 prompt 개선 뒤 v3의 효과 없는 지침을 rollback해 v4를 동결했다. Level 2 AJAX에서는 Solar Navigator 조건만 정확한 3건과 value accuracy 1.0으로 완전 통과했다. Solar Open 2는 Navigator를 중심으로 Gemini 역할을 충분히 대체할 가능성을 보였고, Coder는 동적 브라우저 과제의 저장 안정성을 더 보강할 필요가 있다. 상세: [`reports/06-practice-aaws/README.md`](reports/06-practice-aaws/README.md), [Wiki 요약](wiki/projects/aaws-solar-api-agent-evaluation-2026-07-28.md)

**(종합 의견)** Solar Open 2는 범위가 명확한 문서 통합, 대량 파일 처리와 프로토타입 초안에서 실제 생산성을 보여줬다. 장시간 실행과 고위험 저장소 변경에는 테스트·diff·사람 검수를 붙이는 편이 안전하지만, 이는 활용 가치가 낮다는 뜻이 아니라 현재 가장 효과적인 운영 방식에 가깝다. 직접 Claude 대조군이 없는 태스크에서는 동등성이나 우위 대신 확인된 성과와 필요한 감독 수준을 함께 제시한다.

평가 방법과 판정 범례: [실사용 성능 보고서 색인](reports/index.md)

## 목적

- Solar Open 2의 실제 사용 과정과 결과를 재현 가능한 형태로 남깁니다.
- 성공 사례뿐 아니라 오류, 한계, 우회 방법과 판단 근거도 함께 기록합니다.
- 흩어진 원본 자료를 주제별 문서로 종합하고 서로 연결합니다.
- 실행 가능한 공개 코드와 장기적으로 참고할 수 있는 사용 후기를 만듭니다.

이 저장소는 [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)의 누적형 지식 관리 방식을 따릅니다. 문서는 [Open Knowledge Format (OKF)](https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf)를 바탕으로 YAML frontmatter와 Markdown 본문을 사용합니다.

## 명칭 표기

- 문서 제목과 본문에서는 Upstage가 안내한 공식 명칭인 **Solar Open 2**를 사용합니다.
- `solar-open2`는 API 요청의 `model` 파라미터, 코드, 설정값 또는 태그처럼 정확한 모델 식별자가 필요한 경우에만 사용합니다.
- 임의의 축약형이나 붙여 쓴 표기는 사용하지 않습니다.

## 작업 공간 구분

| 위치 | 역할 | 공개 여부 |
| --- | --- | --- |
| 로컬 `_Upstage` 작업 공간 | 에이전트 실행, 코드 작성, 모델 호출과 실험 | 별도 관리 |
| 이 저장소 | 실험 관찰, 선별된 자료, 분석과 지식 문서 | 공개 |
| `_private/` | 개인정보, API 관련 정보, 공개 전 원본과 내부 메모 | Git 제외 |

실행 작업 공간의 파일을 이 저장소에 그대로 복제하지 않습니다. 공개할 가치가 있는 코드와 결과만 선별해 출처, 실행 조건, 관련 문서와 함께 기록합니다.

## 디렉터리 구성

```text
.
├── README.md          # 저장소 소개와 운영 원칙
├── index.md           # 공개 지식 문서의 주제별 색인
├── log.md             # 수집, 실험, 질의와 정비 작업의 시간순 기록
├── sources/           # 공개 가능한 원본 또는 비식별·정제된 자료
├── wiki/              # 주제별로 종합된 OKF Markdown 문서
├── assets/            # 공개 가능한 캡처, 그림과 문서 첨부물
├── snippets/          # 재사용 가능한 최소 코드와 설정 예시
├── reports/           # 태스크별 Solar Open 2 실사용 성능 평가
├── staging/           # Codex가 작성한 격리된 설계·검토 자료
└── _private/          # 공개 금지 자료를 보관하는 로컬 전용 공간
```

각 디렉터리는 자료가 생길 때 생성하며, 빈 디렉터리를 유지하기 위한 파일은 두지 않습니다.

### `sources/`

공식 문서, 공개 안내문, 비식별 처리된 실험 원본처럼 출처 역할을 하는 자료를 보관합니다. 원본은 가능한 한 변경하지 않으며, 수정하거나 정제한 경우 그 사실과 원 출처를 명시합니다.

### `wiki/`

모델 특성, 도구 연동, 프롬프트 패턴, 오류와 해결책, 비교 실험 등 축적된 지식을 주제별로 정리합니다. 새 자료가 기존 지식과 상충하면 이전 내용을 지우기보다 조건과 근거를 함께 기록합니다.

### `assets/`

문서에서 참조하는 공개 가능한 이미지와 캡처를 둡니다. 파일명만으로 용도를 파악할 수 있게 작성하고, 관련 Markdown 문서에서 맥락과 출처를 설명합니다.

### `snippets/`

핵심 동작을 보여주는 짧고 재사용 가능한 코드를 보관합니다. 전체 애플리케이션은 실제 작업 저장소에서 관리하며, 여기에는 이해와 재현에 필요한 최소 예제만 둡니다.

### `reports/`

`_Upstage`의 실제 태스크를 Solar Open 2의 실행 환경별 활용 가능성 관점에서 평가한 상세 보고서를 보관합니다. Claude Code 기반 작업과 LangGraph/LangChain API 에이전트 실험을 구분하며, 모델 행동과 하네스·Plugin·환경 문제, 처리 시간과 사용자 개입을 확인 가능한 범위에서 기록합니다.

### `staging/`

Solar Open 2의 결과물과 Codex의 설계 기여를 섞지 않기 위한 격리 공간입니다. 현재 `staging/ralph-skill-design/`에는 실패 분류, 스킬 격차, 실행 계약, 수락 기준과 Solar 전달용 프롬프트가 들어 있다. 이 자료는 `_Upstage`의 Solar 결과물이 아니라 비교 실험을 준비하기 위한 Codex 측 설계 기록이다.

### `_private/`

다음과 같은 공개 금지 자료를 로컬에서만 보관합니다.

- API 키, 토큰, 인증 파일과 환경 변수
- 개인 이메일 주소, 계정 정보와 기타 개인정보
- 수신 메일 원본 및 비공개 안내 자료
- 공개 전 캡처, 내부 메모와 비식별 처리 전 데이터

`_private/` 전체는 `.gitignore`에 포함됩니다. 다만 민감정보의 안전한 보관을 보장하는 장치는 아니므로, 비밀값은 가능한 한 전용 비밀 관리 도구나 저장소 밖에서 관리합니다.

## 문서 형식

지식 문서는 다음과 같은 최소 OKF frontmatter를 사용합니다.

```yaml
---
title: Solar Open 2 API 기본 호출
type: concept
description: Solar Open 2 API를 호출하는 최소 예제와 관찰 내용
tags:
  - solar-open2
  - api
timestamp: 2026-07-17
---
```

문서 본문에는 필요한 범위에서 다음 내용을 포함합니다.

- 목적과 배경
- 실행 환경과 모델·도구 버전
- 사용한 코드 또는 프롬프트
- 결과와 증거 자료
- 관찰, 한계와 재현 조건
- 관련 문서 및 원본 링크

문서 간 연결에는 일반 Markdown 상대 링크를 사용합니다.

## 기록 원칙

1. 비밀값과 개인정보를 커밋하지 않습니다.
2. 관찰 사실과 작성자의 해석을 구분합니다.
3. 모델명, 파라미터, 도구 버전과 실행 날짜 등 재현 조건을 남깁니다.
4. 실패한 실험도 원인과 배운 점이 있다면 보존합니다.
5. 새 자료를 추가할 때 관련 문서, `index.md`, `log.md`도 함께 갱신합니다.
6. 공개 전 캡처와 로그에 개인정보, API 키, 로컬 경로가 노출되지 않았는지 확인합니다.

## 보안 주의

Solar Open 2 Private Beta API의 입·출력 데이터에는 타인의 개인정보나 민감정보를 포함하지 않습니다. API 키는 승인된 용도에서만 사용하고 제3자에게 공유하지 않습니다. 실험 로그와 화면 캡처 역시 공개 전에 반드시 비식별 처리합니다.

## 특이사항: Ralphthon 명칭 오류

정확한 표기는 **Ralphthon**이고 경로 slug는 `ralphthon`이다. Solar Open 2 작업에서는 잘못된 `ralpthon` 표기가 문서·task·Wiki·실행 스크립트·Skill·결과 폴더로 반복 확산됐다. 사용자가 올바른 철자를 지정한 뒤 Codex가 작업 저장소에 예외적으로 개입해 현재 이름과 참조를 전수 정정했다.

이는 대량 수정 능력과 별개로 canonical 명칭을 먼저 고정하고 금지 문자열 검사를 완료 gate로 둘 필요가 있음을 보여준다. 최초 도입 커밋, 구조 재편 이력, 정정 범위와 재검색 결과는 [Ralphthon 표기 오류 확산과 canonical 경로 정정](wiki/projects/ralphthon-spelling-correction-2026-07-23.md)에 기록했다.
