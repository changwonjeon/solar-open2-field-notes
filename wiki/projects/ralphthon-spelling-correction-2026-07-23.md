---
type: Observation
title: "Ralphthon 표기 오류 확산과 canonical 경로 정정"
description: "Solar Open 2 작업에서 Ralphthon 오타가 저장소 경로 전반으로 확산된 경위, Git 근거와 Codex 정정 범위"
tags: [solar-open2, claude-code, ralphthon, naming, repository-migration]
timestamp: 2026-07-23T00:00:00+09:00
---

# Ralphthon 표기 오류 확산과 canonical 경로 정정

## 관찰 결과

정확한 영문 표기는 **Ralphthon**이고 canonical slug는 `ralphthon`이다. `_Upstage`에는 `ralpthon`이라는 잘못된 철자가 문서뿐 아니라 task·Wiki·실행 스크립트·Skill·결과 폴더 이름까지 확산돼 있었다.

사용자가 정답 철자를 명시하고 전수 수정을 요청해 Codex가 작업 저장소에 예외적으로 개입했다. 이 수정은 Solar Open 2의 산출물로 귀속하지 않는다.

## Git과 작업 트리 근거

| 근거 | 관찰 |
|---|---|
| `6d62228` | 2026-07-17 Ralphthon 비교 실험 Wiki와 실행 스크립트가 추가될 때 잘못된 `src/scripts/ralpthon/` 경로가 들어갔다. |
| `7024b1b` | 2026-07-23 구조 개편에서도 오타가 유지됐다. |
| `e54b7a7` | task 체계 통합 과정에서 `tasks/01-ralpthon/`이 만들어져 오타가 canonical 구조로 확대됐다. |
| 정정 시작 시 HEAD | `_Upstage`의 `main`과 `origin/main`은 `41b686f`를 가리켰다. |
| `git ls-files` 전수 검색 | 잘못된 task 상위 경로 아래 tracked 파일 85개가 확인됐다. |
| 파일시스템 전수 검색 | task·Wiki·Task 03 phase spec 외에 ignored 로컬 Skill과 결과 폴더에도 오타가 있었다. |

과거 커밋은 증거 보존을 위해 재작성하지 않았다. 현재 작업 트리의 경로와 참조만 올바른 철자로 정규화했다.

## 정정 범위

- Task 01 상위 폴더: `01-ralpthon` → `01-ralphthon`
- Task 01 Wiki 폴더: `docs/ralpthon` → `docs/ralphthon`
- 실행 스크립트 폴더: `src/scripts/ralpthon` → `src/scripts/ralphthon`
- 로컬 Skill: `run-ralpthon` → `run-ralphthon`
- 로컬 결과 폴더: `results/ralpthon` → `results/ralphthon`
- Task 03 phase spec 파일명과 현재 문서·스크립트의 내부 참조
- 이 Field Notes 저장소의 Task 01 보고서 폴더와 과거 감사 문서의 현재 경로 표기

작업 중지 후 재검색했을 때 잘못된 이름의 폴더는 없었지만 `_Upstage/README.md`에 이전 철자 참조 7개가 다시 나타났다. 이를 재정정했다. 이 재발은 이름 변경 뒤 최종 본문 검색이 별도로 필요하다는 근거다.

## Solar Open 2 평가상의 의미

이 현상은 초기의 작은 고유명사 오류가 에이전트의 대량 구조화 능력을 통해 넓게 복제된 사례다. Solar Open 2는 폴더 구조와 문서를 일관되게 맞추는 작업을 수행했지만, 기준이 된 철자 자체가 틀렸는지를 독립적으로 확인하지 못했다. 후속 수정 과정에서도 README에 이전 표기가 다시 나타나 최종 금지 문자열 gate의 필요성이 확인됐다.

이 한 사례로 Solar Open 2의 일반 언어 능력이나 코딩 능력 전체를 낮게 평가하지는 않는다. 대신 저장소 migration에서 다음 제어가 필요하다는 실사용 특이사항으로 기록한다.

1. 고유명사와 slug를 작업 전에 immutable glossary로 고정한다.
2. rename 전후에 파일명과 본문을 별도로 전수 검색한다.
3. 금지 문자열 검사 결과가 0이 아니면 완료 보고를 차단한다.
4. 역사적 경로를 인용하는 근거 문서는 일반 검사에서 명시적으로 예외 처리한다.

## 검증 해석

정정 후 일반 파일과 경로에는 잘못된 철자가 없어야 한다. 이 문서와 연결된 특이사항 문단은 오류 증거를 설명하기 위해 해당 문자열을 의도적으로 보존한다. 따라서 단순한 전역 검색 수치보다 허용 문서 목록을 제외한 결과가 0인지 확인해야 한다.
