# requesting-code-review — 코드 리뷰 요청

> 원문: [`skills/requesting-code-review/SKILL.md`](../../../skills/requesting-code-review/SKILL.md)
> 트리거(원문 유지): `Use when completing tasks, implementing major features, or before merging to verify work meets requirements`
> 이 문서는 사람이 읽기 위한 번역본입니다. 에이전트가 따르는 정본은 영어 원문입니다.

---

문제가 연쇄되기 전에 잡기 위해 코드 리뷰어 서브에이전트를 파견한다. 리뷰어는 평가를 위해 **정밀하게 구성된 컨텍스트**를 받는다 — 내 세션 히스토리는 절대 아니다.

**핵심 원칙:** 일찍 리뷰하고, 자주 리뷰한다.

## 언제 요청하나

**필수:**
- 서브에이전트 주도 개발의 각 태스크 후
- 주요 기능 완료 후
- main으로 머지하기 전

**선택이지만 가치 있음:**
- 막혔을 때 (새로운 시각)
- 리팩터링 전 (기준선 점검)
- 복잡한 버그 수정 후

## 요청 방법

**1. git SHA를 얻는다:**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # 또는: git merge-base origin/main HEAD
HEAD_SHA=$(git rev-parse HEAD)
```

**2. 코드 리뷰어 서브에이전트를 파견한다:**

`general-purpose` 서브에이전트를 파견하고 [code-reviewer.md](../../../skills/requesting-code-review/code-reviewer.md) 템플릿을 채운다.

**플레이스홀더:**
- `{DESCRIPTION}` — 무엇을 만들었는지 간단 요약
- `{PLAN_OR_REQUIREMENTS}` — 무엇을 해야 하는지
- `{BASE_SHA}` — 시작 커밋
- `{HEAD_SHA}` — 종료 커밋

**3. 피드백에 따라 행동한다:**
- Critical은 즉시 수정
- Important는 진행 전에 수정
- Minor는 나중을 위해 기록
- 리뷰어가 틀렸다면 근거를 들어 반박

## 예시

```
[Task 2 완료: 검증 함수 추가]

나: 진행 전에 코드 리뷰를 요청하겠습니다.

BASE_SHA=$(git log --oneline | grep "Task 1" | head -1 | awk '{print $1}')
HEAD_SHA=$(git rev-parse HEAD)

[코드 리뷰어 서브에이전트 파견]
  DESCRIPTION: 4가지 이슈 유형을 다루는 verifyIndex()와 repairIndex() 추가
  PLAN_OR_REQUIREMENTS: docs/superpowers/plans/deployment-plan.md의 Task 2
  BASE_SHA: a7981ec
  HEAD_SHA: 3df7661

[서브에이전트 반환]:
  강점: 깔끔한 아키텍처, 실제 테스트
  이슈:
    Important: 진행 표시 누락
    Minor: 보고 주기에 매직 넘버(100)
  평가: 진행해도 좋음

나: [진행 표시 수정]
[Task 3으로]
```

## 흔한 합리화

| 변명 | 실제 |
|---|---|
| "리뷰어를 파견하느니 내가 diff를 보겠다" | 너는 조정자다. diff를 인라인으로 읽으면 작업을 계속 끌고 갈 컨텍스트 창을 태운다. 리뷰어 서브에이전트를 파견하라: diff와 평가는 그의 컨텍스트에 살고, 나에게는 발견만 돌아온다. |
| "리뷰어가 이해하려면 내 세션 히스토리 전체가 필요해" | 정밀하게 구성한 컨텍스트를 주지, 세션 히스토리는 절대 주지 않는다. 그래야 리뷰어가 내 사고 과정이 아니라 **작업 결과물**에 집중한다. |

## 위험 신호

**절대 하지 말 것:**
- "단순하니까" 리뷰를 건너뛰기
- Critical 이슈를 무시하기
- 수정되지 않은 Important를 두고 진행하기
- 타당한 기술적 피드백과 다투기

**리뷰어가 틀렸다면:**
- 기술적 근거로 반박한다
- 동작을 증명하는 코드/테스트를 보여준다
- 명확화를 요청한다

## 함께 있는 파일 (원문)

- `code-reviewer.md` — 코드 리뷰어 프롬프트 템플릿
