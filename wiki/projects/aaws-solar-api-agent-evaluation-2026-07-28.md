---
type: Experiment
title: "AAWS에서 Solar Open 2 API 에이전트 역할 평가"
description: "LangGraph/LangChain 웹 수집 에이전트에서 Solar Open 2의 역할별 Gemini 대체 가능성과 prompt tuning 결과"
tags: [solar-open2, aaws, langgraph, langchain, agent, evaluation]
timestamp: "2026-07-28T17:00:00+09:00"
status: mission-1-completed
---

# AAWS에서 Solar Open 2 API 에이전트 역할 평가

## 목적

AAWS의 Supervisor, Navigator, Coder에 Upstage API의 `solar-open2`를
연결하고 Gemini 기반 역할을 실제로 대체할 수 있는지 확인했다. Claude
Code 평가는 아니며 LangGraph/LangChain 런타임의 API 에이전트 실험이다.

## 완료 범위

- Solar Open 2 단독 API 연결
- Level 1 두 시나리오 × 5개 역할 조건 기준선 10회
- prompt v1~v3 세 개선 회차와 v4 rollback
- Level 2 AJAX 시나리오의 GGG·GGS·GSG 일반화 확인

Mission 2 Analyst와 Mission 3 Memory/Fallback/Skills는 수행하지 않았다.

## Level 1 결과

기준선 10회 모두 요구 레코드를 만들었고 8회 정상 종료했다. GGG와 SSS의
partial 각 1회도 결과는 완성했으나 성공 뒤 workflow를 종료하지 못했다.

| 조건 | 정상/partial | 평균 시간 | Solar 역할 |
| --- | ---: | ---: | --- |
| GGG | 1/1 | 89.218초 | 없음 |
| SSS | 1/1 | 228.553초 | 전체 |
| SGG | 2/0 | 86.360초 | Supervisor |
| GSG | 2/0 | 85.262초 | Navigator |
| GGS | 2/0 | 104.398초 | Coder |

Solar를 하나의 역할에만 적용한 여섯 실행은 모두 정상 종료했다. 결과
레코드도 Gemini 기준선과 같았고, GSG의 tag-filter 결과는 바깥 인용부호
표현만 달랐다.

## Prompt tuning

성공 뒤 반복을 줄이는 Supervisor·Navigator·Coder 종료 규칙을 추가했다.
v2에서 GGG 71.555초, GGS 94.221초, GSG 65.641초로 모두 정상 종료했고
정확성을 유지했다. 추가 Navigator 지침을 넣은 v3는 token과 호출 효율을
일관되게 개선하지 못해 rollback했다. 최종 v4는 검증된 v2 동작을 유지한다.

## Level 2 결과

AJAX로 로딩되는 연도별 Best Picture 3개를 수집했다.

| 조건 | 결과 |
| --- | --- |
| GGG | 3건을 만들었으나 `year` 문자열로 content fail |
| GGS | 성공을 보고했지만 지정 결과 파일이 없어 persistence fail |
| GSG | 정확한 3건, value accuracy 1.0으로 완전 통과 |

Solar Navigator 조건은 세 조건 중 유일하게 gold 평가를 통과했다. Solar
Coder는 Level 1에서는 정상 작동했지만 동적 Playwright 과제의 실행·저장
안정성을 더 보강해야 한다.

## 평가

Solar Open 2는 Level 1의 Supervisor, Navigator, Coder 개별 역할에서
Gemini를 충분히 대체할 가능성을 보여줬다. 특히 Navigator는 Level 1에서
가장 짧은 평균을 기록했고 Level 2에서도 유일한 완전 통과 조건이었다.

전체 Solar 팀도 요구 결과를 생성할 수 있었다. 현재 지연과 완료 후 반복을
감안하면 prompt tuning을 전제로 조건부 대체 가능으로 평가한다. Solar
Coder는 정적 수집에는 대체 가능하지만 동적 브라우저 과제에는 결과 저장
guardrail이 필요하다.

## 해석 범위

조건별 표본은 한 번이고 live 사이트를 순차 실행했다. 초기 recursion
limit과 evaluator version이 일부 달랐고 provider별 token 계측도 대칭적이지
않다. 따라서 통계적 우열이나 비용 우위를 주장하지 않는다. 이번 결과는
Solar Open 2의 실제 역할별 적용 가능성과 다음 개선 지점을 확인한 현장
관찰로 해석한다.

## 관련 문서

- [Task 06 상세 보고서](../../reports/06-practice-aaws/README.md)
- [실사용 성능 보고서 색인](../../reports/index.md)
