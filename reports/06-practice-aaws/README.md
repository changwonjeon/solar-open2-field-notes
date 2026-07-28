---
type: Project
title: "Task 06 관찰 기록 — AAWS Solar Open 2 API 에이전트 평가"
description: "LangGraph/LangChain 기반 AAWS에서 Solar Open 2 API를 사용하는 웹 수집 멀티에이전트 평가의 진행 상태와 초기 실행 기록"
tags: [solar-open2, aaws, web-scraping, multi-agent, observation]
timestamp: "2026-07-28T00:00:00+09:00"
status: mission-1-completed
model: "Upstage Solar Open 2"
harness: "LangGraph / LangChain / langchain-upstage"
assessment: "Level 1 역할별 대체 가능, 전체 Solar 팀은 prompt tuning 조건부"
---

# Task 06 관찰 기록 — AAWS Solar Open 2 API 에이전트 평가

## 현재 상태

> **Mission 1 실행 마감·후속 Mission 미실행.** Task 06은 Claude Code를
> 사용하는 태스크가 아니다. LangGraph/LangChain 기반 AAWS 에이전트에서
> Upstage API로 `solar-open2`를 호출하고, Gemini 기준선과 역할별로
> 비교하는 실험이다. Level 1 기준선 10회, prompt v1~v3의 세 개선 회차,
> v4 rollback과 Level 2 일반화 실행까지 마쳤다. Mission 2·3은 시작하지
> 않은 상태에서 이번 세션을 마감한다.

작업 폴더는 `/home/redux80/_Upstage/tasks/06-practice-aaws/`이다. 관찰자는
해당 작업 폴더를 수정하지 않고 현재 파일과 Git 상태만 확인했다.

## 관찰된 구조

```text
tasks/06-practice-aaws/
├── .gitignore
├── AGENTS.md
├── CLAUDE.md
├── aaws/       # AAWS Git submodule
├── data/       # 현재 비어 있음
├── docs/       # 현재 비어 있음
└── output/     # 현재 비어 있음
```

`AGENTS.md`는 Task 06을 다음 네 하위 작업으로 구분한다.

1. `06-01`: Jupyter Notebook 실습
2. `06-02`: `Mission.md` 단계별 미션 수행
3. `06-03`: Sequential/Supervisor 시나리오 자동 평가
4. `06-04`: 2일차 자율 개선 프로젝트

이 태스크는 기존 Source 불변성 원칙의 예외다. 로컬 규칙은 실습 중
`aaws/` 원본과 `Mission.md` 수정을 허용한다. `output/`에는 Solar Open 2를
사용한 산출물만 반영하고, `.env`와 API 키는 추적하지 않도록 규정한다.

저장소에 있는 `CLAUDE.md`는 작업 지원 범위를 적은 로컬 지시 파일이며,
Task 06의 모델 실행 하네스가 Claude Code라는 뜻은 아니다. 실제 실습
런타임은 LangGraph/LangChain이다. 태스크 `.gitignore`는 `aaws/`를
제외하도록 설정돼 있다.

업데이트된 `aaws/Mission_upstage.md`는 기존 `Mission.md`를 기본 미션으로
유지한다. Gemini가 원래의 전체 기능과 멀티모달 단계를 담당하고,
Solar Open 2는 텍스트 지시·분석·코드·도구 호출 역할에 추가해 비교한다.
따라서 Mission 2·3에서도 전체 파이프라인을 Solar로 바꾸는 대신 중요한
텍스트 노드만 Gemini(A)와 Solar(B)로 교체한다.

## Solar Open 2 연결 방식

코드에서 확인한 실행 경로는 다음과 같다.

- `langchain_upstage.ChatUpstage`에 `UPSTAGE_API_KEY`와
  `model="solar-open2"`를 전달하는 단독 연결 테스트
- LangChain `init_chat_model()`에 `model_name="solar-open2"`와
  `model_provider="upstage"`를 전달하는 에이전트 생성 경로
- `AAWS_MODEL_MODE=upstage`일 때 Navigator 내부 분석 모델도 Solar Open 2로
  선택하는 분기
- Supervisor 시나리오 러너의 `--model-mode upstage` 및
  `--model-mode both` 비교 실행
- LangSmith 프로젝트와 결과 파일에 Upstage 실행을 별도 표기하는 경로

따라서 평가 대상은 Claude Code의 파일 작업 능력이 아니라, Solar Open 2가
LangChain 도구 호출과 LangGraph 멀티에이전트 흐름에서 Navigator, Coder,
Supervisor 역할을 얼마나 안정적으로 수행하는지다.

## AAWS 기준점

관찰된 서브모듈 기준점은 다음과 같다.

| 항목 | 관찰값 |
| --- | --- |
| 원격 저장소 | `https://github.com/changwonjeon/AAWS` |
| 브랜치 | `main` |
| 커밋 | `b7429210c93d9d3ea6616c1d55d305150003c344` |
| 커밋 제목 | `Add Upstage Solar Open2 experiment support` |
| 커밋 시각 | 2026-07-28 05:32:26 UTC |
| 서브모듈 내부 상태 | 관찰 시작 시 clean, 후속 확인 시 `.gitignore` modified |

AAWS에는 다음 주요 자산이 포함돼 있다.

- 실습 노트북 5개
- Navigator, Coder, Supervisor를 포함한 `app/` 코드
- Sequential 및 Supervisor 시나리오 실행기
- 9개 시나리오 명세
- LangChain/LangGraph 참고 문서
- FastAPI 서버, Streamlit UI 및 VNC 설치·실행 스크립트
- Solar Open 2 지원 확인용 `test_upstage.py`
- Mission 1 전용 반복 러너 `tests/run_mission1.py`
- 결정론적 평가기 `tests/mission1_evaluator.py`
- 로컬 Python 선택용 `run_local_python.sh`
- 실험 인덱스 `log.md`와 Mission 1 상세 로그

## 업데이트된 미션 설계

### Mission 1

두 Level 1 시나리오에 5개 역할 조건을 한 번씩 적용해 총 10회의 탐색
기준선을 만든다.

| 조건 | Supervisor | Navigator | Coder |
| --- | --- | --- | --- |
| `GGG` | Gemini | Gemini | Gemini |
| `SSS` | Solar | Solar | Solar |
| `SGG` | Solar | Gemini | Gemini |
| `GSG` | Gemini | Solar | Gemini |
| `GGS` | Gemini | Gemini | Solar |

기준선 뒤에는 `SSS`, `GGS`, `GSG`를 대표 조건으로 선정해
`quotes_02_tag_filter`에서 prompt v1~v3을 회차별로 실행·분석한다.
마지막 v4를 동결한 뒤 Level 2 하나에서 일반화를 확인한다.

### Mission 2·3

- Mission 2는 Gemini가 Analyst 전체 파이프라인과 멀티모달 기능을
  구현하고, 분석 코드 또는 근거 기반 보고서 같은 텍스트 노드만 Solar와
  A/B 비교한다.
- Mission 3도 Gemini로 기능을 먼저 완성한 뒤 Memory, Fallback, Skill,
  사용자 정의 시나리오의 중요 텍스트 판단 노드만 Solar로 교체한다.
- 이미지 생성·편집, Vision, OCR은 Gemini 전용으로 기록하며 Solar
  성과로 귀속하지 않는다.
- fallback 뒤 성공한 실행은 Solar 단독 성공으로 집계하지 않는다.

## Mission 1 평가 기반

2026년 7월 28일 관찰 시점에 다음 기반이 추가돼 있었다.

- 역할별 모델 조합과 고유 run ID
- `scenario/condition/run_id` 결과 분리
- 조건 공통 900초 timeout과 `model_api` 오류용 1회 retry
- 실행 metadata, 원본 로그, 결과 JSON과 결정론적 평가 분리
- LangSmith의 Gemini/Solar 프로젝트 분리
- Schema, 레코드 완전성, 누락률, 중복률, 페이지 커버리지 측정
- 모델 실패와 외부·평가기 실패의 분류
- prompt v0 snapshot과 v1 변경 이력
- prompt tuning 실행의 version·round metadata와 SHA-256

## Mission 1 첫 기준선 10회

### 실행 범위

```text
Level 1 시나리오 2개
× GGG·SSS·SGG·GSG·GGS 5조건
× 조건당 1회
= 10개 개별 실행
```

두 시나리오는 live `quotes.toscrape.com`의 5페이지 pagination과
`inspirational` tag filter다. 모든 실행은 결과 JSON, metadata,
execution log와 evaluation을 별도 run ID 아래 보존했다.

### 전체 결과

| 조건 | 정상 종료 | Partial | 평균 시간 | 관찰 |
| --- | ---: | ---: | ---: | --- |
| `GGG` | 1 | 1 | 89.218초 | pagination 결과 생성 뒤 recursion limit 25 |
| `SSS` | 1 | 1 | 228.553초 | tag filter 결과 생성 뒤 recursion limit 60 |
| `SGG` | 2 | 0 | 86.360초 | Solar Supervisor 단독 교체 |
| `GSG` | 2 | 0 | 85.262초 | Solar Navigator 단독 교체, 가장 짧은 평균 |
| `GGS` | 2 | 0 | 104.398초 | Solar Coder 단독 교체 |

정상 종료는 8회, 결과를 만들었지만 workflow가 종료되지 않은 partial은
2회, 결과 생성 자체에 실패한 실행은 0회다. 두 partial도 요구 레코드와
필터 조건을 충족한 JSON을 만들었다.

### 시나리오별 근거

| 시나리오 | 조건 | 시간 | 상태 | 결과 |
| --- | --- | ---: | --- | --- |
| pagination | GGG | 84.751초 | partial | 50건, recursion 25 |
| pagination | SSS | 118.593초 | success | 50건 |
| pagination | SGG | 91.486초 | success | 50건 |
| pagination | GSG | 85.023초 | success | 50건 |
| pagination | GGS | 106.446초 | success | 50건 |
| tag filter | GGG | 93.685초 | success | 13건, filter 1.0 |
| tag filter | SSS | 338.513초 | partial | 13건, recursion 60 |
| tag filter | SGG | 81.234초 | success | 13건, filter 1.0 |
| tag filter | GSG | 85.501초 | success | 13건, filter 1.0 |
| tag filter | GGS | 102.349초 | success | 13건, filter 1.0 |

모든 실행에서 retry는 0회였다. 외부 사이트 또는 model API 오류로
분류된 실행도 없었다.

## Gemini 대비 Solar 관찰

### 결과 생성 능력

- pagination 결과는 다섯 조건 모두 byte-identical한 50건이었다.
- tag-filter 결과는 GGG·SSS·SGG·GGS가 byte-identical한 13건이었다.
- GSG도 동일한 13개 quote·author·tag를 수집했지만 quote 본문의 바깥
  typographic quotation mark를 제거해 byte hash가 달랐다. 바깥 인용부호를
  정규화하면 GGG 결과와 같은 데이터다.
- 따라서 이 두 Level 1 시나리오에서는 Solar를 어느 역할에 넣어도 요구한
  레코드 집합을 생성할 수 있었다.

다만 gold fixture가 없어 `value_accuracy`는 모든 평가에서 `null`이다.
레코드 수, Schema, 누락·중복과 필터 조건은 확인됐지만 각 문자열이
authoritative source와 정확히 일치한다는 독립 oracle 검증은 아니다.

### 역할별 관찰

- **Supervisor (`SGG`)**: 두 시나리오 모두 정상 종료했고 평균
  86.360초였다. 이번 범위에서는 Solar가 위임·조정 역할을 수행할 수
  있다는 긍정적 신호다.
- **Navigator (`GSG`)**: 두 실행 모두 정상 종료했고 평균 85.262초로
  다섯 조건 중 가장 짧았다. DOM 기반 Level 1 탐색 역할에서 가장 안정적인
  Solar 적용 후보로 관찰됐다.
- **Coder (`GGS`)**: 두 실행 모두 정상 종료했지만 평균 104.398초로 GGG
  평균보다 약 17% 길었다. 기능은 수행했으나 속도 이점은 확인되지 않았다.
- **전체 Solar (`SSS`)**: pagination은 성공했지만 tag filter에서 결과
  생성 후에도 반복해 recursion 60에 도달했다. 평균 228.553초로 GGG
  평균의 약 2.56배였다.

전체 Solar 팀의 반복 종료 문제는 개별 Solar 역할 조건에서는 재현되지
않았다. 이는 각 역할의 기본 능력 부족보다는 Solar 에이전트끼리 연결됐을
때 완료 신호와 재호출이 누적되는 orchestration 문제일 가능성을 시사한다.
그러나 GGG도 pagination에서 같은 계열의 recursion 실패가 있었으므로
Solar에만 고유한 결함이라고 결론내릴 수는 없다.

### 운영 지표

정상 완료된 mixed 조건의 model/tool call은 대체로 GGG와 같거나 적었다.
예를 들어 tag filter의 GGS는 11/8회, GSG는 16/13회, SGG는 15/11회였고
GGG는 18/15회였다.

token 비교는 유효하지 않다. SSS pagination은 model call 19회가
기록됐지만 token이 0이고, partial 실행은 운영 지표 자체가 비어 있다.
mixed 조건의 token도 Solar provider usage가 누락됐을 가능성이 있어
Gemini와 Solar의 비용·token 효율을 비교하는 근거로 사용하지 않는다.

## 실험 타당성 검토

### 의미 있게 수행된 부분

- 역할별 모델을 명시적으로 분리해 전체 팀 결과만으로 실패 원인을
  추정하는 문제를 줄였다.
- 10개 실행 모두 고유 run ID와 원본 결과·로그·metadata를 보존했다.
- 동일한 두 시나리오에서 다섯 조건의 결과 레코드를 비교할 수 있다.
- 결과 생성 성공과 workflow 정상 종료를 분리해 partial을 숨기지 않았다.
- model API·사이트 오류와 orchestration 오류를 구분했다.
- 기준선의 한계를 반영해 업데이트된 미션이 10회를 통계적 본 실험이 아닌
  prompt tuning용 탐색 기준선으로 명시했다.

### 비교를 제한하는 문제

1. **표본이 조건·시나리오별 1회다.** 성공률, 분산과 재현성을 추정할 수
   없다.
2. **recursion 조건이 같지 않았다.** GGG pagination은 limit 25,
   SSS tag filter와 현재 runner는 60이다. 두 partial을 동등한 종료 실패로
   직접 비교할 수 없다.
3. **평가기 계약이 실행 중 바뀌었다.** 빈 `tags` 배열의 missing 처리와
   tag-filter 예상 레코드가 10에서 13으로 수정됐다. 원본 evaluation은
   보존됐지만 10개 파일이 완전히 같은 evaluator version으로 재채점되지는
   않았다.
4. **baseline run 디렉터리에 prompt snapshot이 없다.** v0 snapshot은
   별도 provenance 폴더에 보존됐지만 각 최초 run과 직접 묶인 hash가 없다.
5. **live 사이트를 순차 실행했다.** 사이트·네트워크 시간 변화와 고정된
   실행 순서가 latency에 섞일 수 있다.
6. **token 계측이 provider 간 대칭적이지 않다.** 현재 값으로 비용 비교를
   하면 안 된다.
7. **gold data가 없다.** 내용 정확성을 정답과 대조하지 못했다.
8. **모델 구성이 동일 등급 대조가 아니다.** Gemini는 Supervisor·Navigator에
   2.5 Pro, Coder에 Flash를 쓰고 Solar는 단일 모델을 쓴다. 이는 실제
   스택 비교에는 의미가 있지만 순수 모델 능력 비교와는 다르다.

### 종합 판단

첫 10회는 **역할별 적용 가능성과 종료 문제를 찾는 탐색 실험으로는
제대로 기능했다.** Solar Open 2는 세 역할을 각각 단독 교체했을 때 모두
정상적으로 결과를 만들고 workflow를 종료했다. 특히 Navigator 역할은
후속 평가 가치가 높고, 전체 Solar 팀에서는 완료 후 반복을 줄이는 prompt
개선이 필요하다는 구체적인 가설도 얻었다.

Solar Open 2 관점의 평가는 **Level 1 텍스트 기반 웹 수집 에이전트의
개별 역할에서 Gemini를 충분히 대체 가능**하다는 것이다. Supervisor,
Navigator, Coder에 하나씩 투입한 여섯 실행이 모두 정상 종료했고 결과
내용도 Gemini 기준선과 같거나 표현상 차이만 있었다. 특히 Navigator는
이번 기준선에서 Gemini 전체 팀보다 느리지 않았으며 가장 짧은 조건 평균을
보였다.

전체 Solar 팀도 두 실행 모두 요구 결과를 만들었기 때문에 작업 수행 능력은
확인됐다. 한 번의 종료 반복과 현재 API 지연 때문에 즉시 완전 대체로
평가하기보다 **prompt tuning을 전제로 한 조건부 대체 가능**으로 본다.
이는 모델이 결과를 만들지 못한 문제가 아니라 성공 뒤 멈추는 규칙과 호출
효율의 문제이며, 이미 v1에서 직접 개선 중인 영역이다.

따라서 현재 실용적 판단은 다음과 같다.

| 적용 범위 | 판단 |
| --- | --- |
| Solar Navigator | 대체 가능성이 높음 |
| Solar Supervisor | Level 1 범위에서 대체 가능 |
| Solar Coder | 대체 가능, 현재는 다소 긴 지연 감수 |
| 전체 Solar 팀 | 결과 생성은 대체 가능, 종료·지연 tuning 권장 |

표본이 작다는 한계는 남지만, 이번 단계의 목적이 실제 적용 가능성을
확인하는 것이라면 Solar Open 2는 충분히 긍정적인 결과를 보였다.

## Prompt tuning 결과

기준선 분석은 완료 후 반복을 줄이는 것을 1차 개선 목표로 삼았다.

- v1: Supervisor 호출 예산, Navigator DoD, Coder 성공 후 즉시 종료
- v2: Coder의 고유 script, `create → run → validate` 순서와 호출 상한
- v3: 정적 filter URL에서 browse를 줄이는 Navigator 지침
- v4: 효과가 일관되지 않은 v3만 rollback하고 v2 동작을 최종 채택

### Round 1 — v1

| 조건 | 상태 | 시간 | 기준선 대비 |
| --- | --- | ---: | --- |
| SSS | partial | 306.841초 | recursion 반복 지속 |
| GGS | success | 87.384초 | 102.349초에서 14.965초 단축 |
| GSG | success | 76.069초 | 85.501초에서 9.432초 단축 |

v1은 mixed Solar 조건의 지연을 줄였지만 SSS 전체 팀의 종료 반복은
해결하지 못했다. 이후 SSS 반복은 진단 목적을 달성한 것으로 보고 중단했다.

### Round 2 — v2

| 조건 | 상태 | 시간 | model/tool | 관찰 |
| --- | --- | ---: | ---: | --- |
| GGG | success | 71.555초 | 10/7 | v0 대비 22.130초 단축 |
| GGS | success | 94.221초 | 12/8 | v0 대비 8.128초 단축 |
| GSG | success | 65.641초 | 11/8 | v0 대비 19.860초 단축 |

세 조건 모두 13건, Schema, missing/duplicate 0과 filter compliance 1.0을
유지했다. v2는 정확성을 유지하면서 불필요한 실행을 줄인 가장 균형 잡힌
prompt version으로 관찰됐다.

### Round 3 — v3와 v4 rollback

v3도 세 조건 모두 정확한 결과를 만들었지만 의도와 달리 `browse_web`을
제거하지 못했다. v2 대비 세 조건 모두 token이 증가했고 GGG·GSG 시간도
늘었다. 이에 v3의 Navigator 문구를 제거하고 v2의 일반 종료·도구 예산
규칙으로 돌아간 v4를 최종 prompt로 동결했다.

프롬프트가 길어지는 것이 항상 개선으로 이어지지 않았고, 실제 trace에서
효과가 없는 지침을 되돌린 점은 이번 실험의 의미 있는 운영 성과다.

## Level 2 일반화 결과

최종 v4를 `ajax_01_playwright_wait` live 시나리오에 적용했다. 2015,
2014, 2013년의 Best Picture 3개를 Playwright 대기로 수집하는 과제다.

| 조건 | 시간 | 결과 | 판정 |
| --- | ---: | --- | --- |
| GGG | 124.438초 | 3건 생성, `year`를 문자열로 저장 | content fail, value accuracy 0.0 |
| GGS | 180.226초 | 최종 보고는 성공 주장, 지정 결과 파일 없음 | persistence fail |
| GSG | 180.471초 | 정확한 3건과 정수 `year` 저장 | deterministic pass, value accuracy 1.0 |

이번 Level 2 단일 실행에서는 **Solar Navigator 조건만 완전 통과**했다.
Gemini 전체 팀은 데이터 자체는 맞았지만 Schema가 기대한 정수 대신 문자열을
사용했고 gold 비교에서 실패했다. Solar Coder는 코드를 반복 실행하고
성공을 보고했지만 결과 파일을 남기지 못했다.

이 결과는 역할별 결론을 더 선명하게 한다.

- **Solar Navigator:** 정적 Level 1뿐 아니라 AJAX Level 2에서도 Gemini를
  대체할 강한 후보다. 세 Level 2 조건 중 유일하게 정확한 결과를 완성했다.
- **Solar Coder:** Level 1에서는 충분히 대체 가능했지만 동적 Playwright
  과제에서는 실행·저장 안정성 보강이 필요하다.
- **전체 Solar 팀:** 결과 생성 역량은 확인됐으나 종료 반복과 지연을 줄이는
  후속 tuning이 필요하다.

Level 2도 조건별 1회이므로 일반적인 승률은 아니지만, Solar Navigator가
더 어려운 시나리오에서도 실제로 Gemini 기준선보다 정확한 결과를 냈다는
구체적인 긍정 증거다.

## 이번 마감 범위

- Solar API 사전 검사 완료
- Level 1 역할별 기준선 10회 완료
- 세 차례 prompt 개선 실험 완료
- 효과 없는 v3 rollback과 최종 v4 동결
- Level 2 세 조건 일반화 실행 완료
- 유효 실행 22개와 수동 중단 invalid 실행 1개 보존
- Mission 2 Analyst와 Mission 3 고도화는 미실행

관찰 종료 시점에 실행 중인 Mission 프로세스는 없었다.

## 사전 연결 검사

작업 로그에는 세 번의 Solar API 사전 검사가 기록돼 있다.

| 시도 | 결과 | 해석 |
| --- | --- | --- |
| 기본 `uv run` | 실패 | 상위 `.venv`가 선택돼 `langchain_upstage` import 실패; 모델 실패 아님 |
| `uv run --active` | 수동 중단 | 약 30초 대기 후 종료 코드 130; 모델 실패로 집계하지 않음 |
| `./.venv/bin/python` 직접 실행 | 성공 | 45초 제한 안에 종료 코드 0, 총 2,504 token |

세 번째 시도로 Upstage 인증, 네트워크와 `solar-open2` 호출 경로가
작동함을 확인했다. 이후 Solar 단독 검증에는 최소 120초를 허용하는
운영 결정을 남겼다.


## Git 상태

상위 `_Upstage` 저장소에서 다음 변경이 관찰됐다.

| 경로 | 상태 | 의미 |
| --- | --- | --- |
| `.gitmodules` | staged add | 서브모듈 설정 신규 추가 |
| `tasks/06-practice-aaws/aaws` | staged add + worktree modified | AAWS gitlink 신규 추가, Mission·runner·평가기·로그 등 변경 |
| `tasks/06-practice-aaws/.gitignore` | untracked | 태스크 로컬 ignore 규칙 |
| `tasks/06-practice-aaws/AGENTS.md` | untracked | 태스크 로컬 규칙 |
| `tasks/06-practice-aaws/CLAUDE.md` | untracked | 태스크 로컬 지시 |
| `tasks/06-practice-aaws/README.md` | untracked | 태스크 개요와 진행 상태 |
| 루트 `uv.lock` | untracked | 상위 실행 환경 잠금 파일 |
| `data/`, `docs/`, `output/` | 빈 디렉터리 | Git에는 아직 기록되지 않음 |

따라서 Task 06 준비 상태는 아직 상위 저장소 커밋으로 확정되지 않았다.

## 관찰 중 상태 변화

첫 확인에서는 서브모듈 내부가 clean이고 `AGENTS.md`만 untracked 상태였다.
후속 검증 시점에는 태스크 루트에 `.gitignore`와 `CLAUDE.md`가 추가됐고,
서브모듈 내부 `.gitignore`도 16줄 추가로 변경돼 상위 gitlink가 `dirty`로
표시됐다. 이는 Task 06 준비 작업이 관찰과 동시에 진행되고 있음을 뜻한다.

따라서 이 문서는 Mission 1 실행을 마감한 2026년 7월 28일의 관찰
스냅샷이다. 작업 트리는 아직 커밋되지 않았으며 Mission 2와 Mission 3은
실행되지 않았다.

## 재현성 주의점

현재 `.gitmodules`의 submodule 이름과 `path`는 모두 로컬 절대경로
`/home/redux80/_Upstage/tasks/06-practice-aaws/aaws`로 기록돼 있다.
상위 저장소에서 `git submodule status`를 실행하면 해당 gitlink에 대응하는
mapping을 찾지 못했다는 오류가 발생했다.

이는 다른 환경에서 clone 또는 `git submodule update --init`을 수행할 때
재현성을 해칠 수 있는 준비 단계 이슈다. 일반적으로 저장소 루트 기준 상대
경로인 `tasks/06-practice-aaws/aaws`로 설정한 뒤, 상위 저장소에서 submodule
명령이 정상 동작하는지 검증할 필요가 있다.

이 기록은 문제를 관찰한 것이며 작업 폴더의 설정을 수정한 것은 아니다.

## 남은 과제

- Mission 2 Analyst 실험
- Mission 3 강화 실험
- 동적 브라우저 작업에서 Solar Coder의 실행·결과 저장 보장
- 전체 Solar 구성의 종료 판단과 지연 시간 보정
- 공급자 간 대칭적인 token·비용 계측
- 고정 fixture와 반복 실행을 통한 선택적 재검증

현재까지 Solar-open2는 Gemini를 대체할 가능성을 충분히 보여주었다.
특히 Navigator 역할은 Level 2에서도 완전한 정답을 만든 유일한 구성으로,
우선 적용을 검토할 만하다. 전체 Solar 구성은 종료 조건과 지연 시간을
보완하면 대체 범위를 더 넓힐 수 있다.
