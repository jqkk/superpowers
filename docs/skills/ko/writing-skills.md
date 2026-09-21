# writing-skills — 스킬 작성

> 원문: [`skills/writing-skills/SKILL.md`](../../../skills/writing-skills/SKILL.md)
> 트리거(원문 유지): `Use when creating new skills, editing existing skills, or verifying skills work before deployment`
> 이 문서는 사람이 읽기 위한 번역본입니다. 에이전트가 따르는 정본은 영어 원문입니다.

---

## 개요

**스킬을 쓰는 것은 프로세스 문서에 적용한 테스트 주도 개발이다.**

**개인 스킬은 런타임의 skills 디렉터리에 산다**(Claude Code에서는 `~/.claude/skills/`). 다른 런타임의 경로는 `../using-superpowers/references/`의 codex-tools.md, gemini-tools.md를 참고한다. Codex, Copilot CLI, Gemini CLI는 모두 `~/.agents/skills/`를 교차 런타임 별칭으로도 인식한다.

테스트 케이스를 쓰고(서브에이전트를 쓰는 압박 시나리오), 실패를 지켜보고(기준선 동작), 스킬을 쓰고(문서), 테스트가 통과하는 것을 보고(에이전트가 준수함), 리팩터링한다(빠져나갈 구멍 막기).

**핵심 원칙:** 스킬 없이 에이전트가 실패하는 것을 보지 않았다면, 그 스킬이 올바른 것을 가르치는지 알 수 없다.

**필수 배경:** 이 스킬을 쓰기 전에 superpowers:test-driven-development를 이해해야 한다. 그 스킬이 근본적인 RED-GREEN-REFACTOR 사이클을 정의한다. 이 스킬은 TDD를 문서에 적응시킨 것이다.

**공식 가이드:** Anthropic의 공식 스킬 작성 베스트 프랙티스는 원문 디렉터리의 `anthropic-best-practices.md`를 참고한다. 이 문서는 그것을 보완하는 추가 패턴과 지침을 제공한다.

## 스킬이란?

**스킬**은 검증된 기법, 패턴, 도구에 대한 참조 가이드다. 스킬은 미래의 에이전트가 효과적인 접근을 찾아 적용하도록 돕는다.

**스킬인 것:** 재사용 가능한 기법, 패턴, 도구, 참조 가이드
**스킬이 아닌 것:** 어떤 문제를 한 번 어떻게 풀었는지에 대한 서사

## 스킬 작성의 TDD 대응표

| TDD 개념 | 스킬 작성 |
|---|---|
| **테스트 케이스** | 서브에이전트를 쓴 압박 시나리오 |
| **프로덕션 코드** | 스킬 문서 (SKILL.md) |
| **테스트 실패 (RED)** | 스킬 없이 에이전트가 규칙을 위반 (기준선) |
| **테스트 통과 (GREEN)** | 스킬이 있을 때 에이전트가 준수 |
| **리팩터** | 준수를 유지하면서 빠져나갈 구멍 막기 |
| **테스트 먼저 작성** | 스킬을 쓰기 **전에** 기준선 시나리오 실행 |
| **실패 지켜보기** | 에이전트가 쓰는 정확한 합리화를 기록 |
| **최소 코드** | 그 구체적 위반들을 겨냥한 스킬 작성 |
| **통과 지켜보기** | 이제 에이전트가 준수하는지 확인 |
| **리팩터 사이클** | 새 합리화 발견 → 막기 → 재검증 |

스킬 작성 전 과정이 RED-GREEN-REFACTOR를 따른다.

## 언제 스킬을 만드나

**만들 때:**
- 그 기법이 직관적으로 명백하지 않았을 때
- 프로젝트를 넘나들며 다시 참조할 것 같을 때
- 패턴이 폭넓게 적용될 때 (프로젝트 특정이 아님)
- 다른 사람에게도 도움이 될 때

**만들지 말 것:**
- 일회성 해법
- 다른 곳에 잘 문서화된 표준 관행
- 프로젝트 특정 관례 (지침 파일에 넣는다)
- 기계적 제약 (정규식/검증으로 강제할 수 있다면 자동화하고, 문서는 판단이 필요한 것에 아껴 쓴다)

## 스킬 유형

### 기법 (Technique)
따를 단계가 있는 구체적 방법 (condition-based-waiting, root-cause-tracing)

### 패턴 (Pattern)
문제를 바라보는 방식 (flatten-with-flags, test-invariants)

### 참조 (Reference)
API 문서, 문법 가이드, 도구 문서 (office docs)

## 디렉터리 구조

```
skills/
  skill-name/
    SKILL.md              # 본 참조 (필수)
    supporting-file.*     # 필요할 때만
```

**평평한 네임스페이스** — 모든 스킬이 하나의 검색 가능한 네임스페이스에 있다

**별도 파일로 뺄 것:**
1. **무거운 참조** (100줄 이상) — API 문서, 포괄적 문법
2. **재사용 도구** — 스크립트, 유틸리티, 템플릿

**인라인으로 유지할 것:**
- 원칙과 개념
- 코드 패턴 (50줄 미만)
- 그 외 전부

## SKILL.md 구조

**프론트매터 (YAML):**
- 필수 필드 둘: `name`과 `description` (지원되는 전체 필드는 [agentskills.io/specification](https://agentskills.io/specification) 참고)
- 총 1024자 이내
- `name`: 영문자, 숫자, 하이픈만 (괄호나 특수문자 금지)
- `description`: 3인칭, **언제 쓰는지만** 서술 (무엇을 하는지가 아니라)
  - 트리거 조건에 집중하도록 "Use when..."으로 시작
  - 구체적 증상, 상황, 맥락을 포함
  - **스킬의 프로세스나 워크플로를 절대 요약하지 않는다** (이유는 아래 SDO 절)
  - 가능하면 500자 이내

```markdown
---
name: Skill-Name-With-Hyphens
description: Use when [구체적 트리거 조건과 증상]
---

# Skill Name

## Overview
이게 무엇인가? 핵심 원칙 1~2문장.

## When to Use
[판단이 명백하지 않을 때만 작은 인라인 플로차트]

증상과 사용 사례가 담긴 불릿 목록
쓰지 말아야 할 때

## Core Pattern (기법/패턴용)
before/after 코드 비교

## Quick Reference
훑어볼 수 있는 표나 불릿

## Implementation
단순 패턴은 인라인 코드
무거운 참조나 재사용 도구는 파일로 링크

## Common Mistakes
무엇이 잘못되는가 + 수정

## Real-World Impact (선택)
구체적 결과
```

## 스킬 발견 최적화 (SDO)

**발견에 결정적:** 미래의 에이전트가 이 스킬을 **찾을** 수 있어야 한다.

### 1. 풍부한 description 필드

**목적:** 에이전트는 description을 읽고 주어진 작업에 어떤 스킬을 로드할지 정한다. "지금 이 스킬을 읽어야 하는가?"에 답하게 만든다.

**형식:** 트리거 조건에 집중하도록 "Use when..."으로 시작한다.

**결정적: description = 언제 쓰는가이지, 스킬이 무엇을 하는가가 아니다**

description은 **트리거 조건만** 서술해야 한다. 스킬의 프로세스나 워크플로를 description에 요약하지 **않는다**.

**왜 중요한가:** 테스트 결과, description이 스킬의 워크플로를 요약하면 에이전트가 **스킬 내용을 읽는 대신 description을 따를 수 있다**. "태스크 사이의 코드 리뷰"라고 쓴 description은 에이전트가 리뷰를 **한 번만** 하게 만들었다 — 스킬의 플로차트가 두 번의 리뷰를 분명히 보여주고 있었는데도.

description을 "Use when executing implementation plans with independent tasks"(워크플로 요약 없음)로 바꾸자 에이전트는 플로차트를 제대로 읽고 2단계 리뷰 프로세스를 따랐다.

**함정:** 워크플로를 요약하는 description은 에이전트가 택할 **지름길**을 만든다. 스킬 본문은 에이전트가 건너뛰는 문서가 된다.

```yaml
# ❌ 나쁨: 워크플로를 요약 — 에이전트가 스킬을 읽는 대신 이것을 따를 수 있다
description: Use when executing plans - dispatches subagent per task with code review between tasks

# ❌ 나쁨: 프로세스 세부가 너무 많음
description: Use for TDD - write test first, watch it fail, write minimal code, refactor

# ✅ 좋음: 트리거 조건만, 워크플로 요약 없음
description: Use when executing implementation plans with independent tasks in the current session

# ✅ 좋음: 트리거 조건만
description: Use when implementing any feature or bugfix, before writing implementation code
```

**내용:**
- 이 스킬이 해당됨을 알리는 구체적 트리거, 증상, 상황을 쓴다
- *언어 특정 증상*(setTimeout, sleep)이 아니라 *문제*(경쟁 상태, 일관되지 않은 동작)를 서술한다
- 스킬 자체가 기술 특정적이지 않은 한 트리거를 기술 중립적으로 유지한다
- 스킬이 기술 특정적이면 그 점을 트리거에 명시한다
- 3인칭으로 쓴다 (시스템 프롬프트에 주입된다)
- **스킬의 프로세스나 워크플로를 절대 요약하지 않는다**

```yaml
# ❌ 나쁨: 너무 추상적, 모호, 언제 쓰는지 없음
description: For async testing

# ❌ 나쁨: 1인칭
description: I can help you with async tests when they're flaky

# ❌ 나쁨: 기술을 언급하지만 스킬이 그 기술 특정적이지 않음
description: Use when tests use setTimeout/sleep and are flaky

# ✅ 좋음: "Use when"으로 시작, 문제를 서술, 워크플로 없음
description: Use when tests have race conditions, timing dependencies, or pass/fail inconsistently

# ✅ 좋음: 트리거를 명시한 기술 특정 스킬
description: Use when using React Router and handling authentication redirects
```

### 2. 키워드 커버리지

에이전트가 검색할 법한 단어를 쓴다:
- 에러 메시지: "Hook timed out", "ENOTEMPTY", "race condition"
- 증상: "flaky", "hanging", "zombie", "pollution"
- 동의어: "timeout/hang/freeze", "cleanup/teardown/afterEach"
- 도구: 실제 명령, 라이브러리 이름, 파일 유형

### 3. 서술적 이름 짓기

**능동태, 동사 우선:**
- ✅ `creating-skills` (❌ `skill-creation`)
- ✅ `condition-based-waiting` (❌ `async-test-helpers`)

### 4. 토큰 효율 (결정적)

**문제:** getting-started와 자주 참조되는 스킬은 **모든 대화**에 로드된다. 토큰 하나하나가 중요하다.

**목표 단어 수:**
- getting-started 워크플로: 각 150단어 미만
- 자주 로드되는 스킬: 총 200단어 미만
- 그 외 스킬: 500단어 미만 (그래도 간결하게)

**기법:**

**세부는 도구의 help로 옮긴다:**
```bash
# ❌ 나쁨: 모든 플래그를 SKILL.md에 문서화
search-conversations supports --text, --both, --after DATE, --before DATE, --limit N

# ✅ 좋음: --help를 참조
search-conversations supports multiple modes and filters. Run --help for details.
```

**상호 참조를 쓴다:**
```markdown
# ❌ 나쁨: 워크플로 세부를 반복
검색할 때는 이 템플릿으로 서브에이전트를 파견한다...
[반복되는 지침 20줄]

# ✅ 좋음: 다른 스킬을 참조
항상 서브에이전트를 쓴다 (컨텍스트 50~100배 절약). REQUIRED: Use [other-skill-name] for workflow.
```

**예시를 압축한다:**
```markdown
# ❌ 나쁨: 장황한 예시 (42단어)
사람 파트너: "React Router에서 인증 에러를 전에 어떻게 처리했었지?"
나: React Router 인증 패턴에 대해 과거 대화를 검색하겠습니다.
[검색 쿼리로 서브에이전트 파견: "React Router authentication error handling 401"]

# ✅ 좋음: 최소 예시 (20단어)
파트너: "React Router에서 auth 에러를 어떻게 처리했지?"
나: 검색 중...
[서브에이전트 파견 → 종합]
```

**중복을 제거한다:**
- 상호 참조된 스킬에 있는 내용을 반복하지 않는다
- 명령에서 자명한 것을 설명하지 않는다
- 같은 패턴의 예시를 여러 개 넣지 않는다

**검증:**
```bash
wc -w skills/path/SKILL.md
# getting-started 워크플로: 각 150 미만 목표
# 그 외 자주 로드되는 것: 총 200 미만 목표
```

**하는 일이나 핵심 통찰로 이름 짓는다:**
- ✅ `condition-based-waiting` > `async-test-helpers`
- ✅ `using-skills` (❌ `skill-usage`)
- ✅ `flatten-with-flags` > `data-structure-refactoring`
- ✅ `root-cause-tracing` > `debugging-techniques`

**동명사(-ing)가 프로세스에 잘 맞는다:**
- `creating-skills`, `testing-skills`, `debugging-with-logs`
- 능동적이고, 취하는 행동을 서술한다

### 5. 다른 스킬 상호 참조하기

**다른 스킬을 참조하는 문서를 쓸 때:**

스킬 이름만 쓰고, 명시적인 요구 표시를 붙인다:
- ✅ 좋음: `**REQUIRED SUB-SKILL:** Use superpowers:test-driven-development`
- ✅ 좋음: `**REQUIRED BACKGROUND:** You MUST understand superpowers:systematic-debugging`
- ❌ 나쁨: `See skills/testing/test-driven-development` (필수인지 불분명)
- ❌ 나쁨: `@skills/testing/test-driven-development/SKILL.md` (강제 로드, 컨텍스트 소모)

**왜 @ 링크를 쓰지 않나:** `@` 문법은 파일을 즉시 강제 로드해서, 필요해지기도 전에 200k 이상의 컨텍스트를 소모한다.

## 플로차트 사용

```
정보를 보여줘야 하나?
 └─ 예 → 내가 틀릴 수 있는 판단 지점인가?
      ├─ 예 → 작은 인라인 플로차트
      └─ 아니오 → 마크다운을 쓴다
```

**플로차트는 다음에만 쓴다:**
- 명백하지 않은 판단 지점
- 너무 일찍 멈출 수 있는 프로세스 루프
- "언제 A vs B" 판단

**플로차트를 절대 쓰지 말 것:**
- 참조 자료 → 표, 목록
- 코드 예시 → 마크다운 블록
- 선형 지시 → 번호 목록
- 의미 없는 라벨 (step1, helper2)

graphviz 스타일 규칙은 원문 디렉터리의 `graphviz-conventions.dot`을 참고한다.

**사람 파트너를 위한 시각화:** 원문 디렉터리의 `render-graphs.js`로 스킬의 플로차트를 SVG로 렌더링한다:
```bash
node ./render-graphs.js ../some-skill           # 다이어그램을 각각
node ./render-graphs.js ../some-skill --combine # 모든 다이어그램을 SVG 하나에
```

## 코드 예시

**탁월한 예시 하나가 평범한 예시 여럿보다 낫다**

가장 적절한 언어를 고른다:
- 테스트 기법 → TypeScript/JavaScript
- 시스템 디버깅 → Shell/Python
- 데이터 처리 → Python

**좋은 예시:**
- 완전하고 실행 가능
- 왜인지를 설명하는 주석
- 실제 시나리오에서 나옴
- 패턴을 명확히 보여줌
- 바로 적용 가능 (일반 템플릿이 아님)

**하지 말 것:**
- 5개 이상 언어로 구현
- 빈칸 채우기 템플릿 생성
- 억지스러운 예시 작성

포팅은 잘한다. 훌륭한 예시 하나면 충분하다.

## 파일 조직

### 자족적 스킬
```
defense-in-depth/
  SKILL.md    # 전부 인라인
```
언제: 모든 내용이 들어가고, 무거운 참조가 필요 없을 때

### 재사용 도구가 있는 스킬
```
condition-based-waiting/
  SKILL.md    # 개요 + 패턴
  example.ts  # 적용할 동작하는 헬퍼
```
언제: 도구가 서사가 아니라 재사용 가능한 코드일 때

### 무거운 참조가 있는 스킬
```
pptx/
  SKILL.md       # 개요 + 워크플로
  pptxgenjs.md   # 600줄 API 참조
  ooxml.md       # 500줄 XML 구조
  scripts/       # 실행 도구
```
언제: 참조 자료가 인라인으로 담기엔 너무 클 때

번들된 스크립트는 산문에서 **인터프리터를 통해** 호출한다(`bash scripts/tool.sh`, `node scripts/tool.js`). 맨 경로로 부르지 않는다 — 일부 하네스 플러그인 패키저가 실행 비트를 벗겨내서 맨 `scripts/tool.sh`가 `Permission denied`로 실패한다.

## 철칙 (TDD와 동일)

```
실패하는 테스트 없이 스킬 없음
```

**새 스킬에도, 기존 스킬의 수정에도** 적용된다.

테스트 전에 스킬을 썼는가? 삭제하고 처음부터. 테스트 없이 스킬을 수정했는가? 같은 위반이다.

**예외 없음:**
- "간단한 추가"도 아니고
- "섹션 하나 추가"도 아니고
- "문서 업데이트"도 아니다
- 테스트하지 않은 변경을 "참고용"으로 남기지 않는다
- 테스트를 돌리면서 "각색"하지 않는다
- 삭제는 삭제를 뜻한다

**필수 배경:** superpowers:test-driven-development 스킬이 왜 이것이 중요한지 설명한다. 같은 원칙이 문서에도 적용된다.

## 모든 스킬 유형 테스트하기

스킬 유형마다 다른 테스트 접근이 필요하다.

### 규율 강제 스킬 (규칙/요구사항)

**예:** TDD, verification-before-completion, designing-before-coding

**테스트 방법:**
- 학술적 질문: 규칙을 이해하는가?
- 압박 시나리오: 스트레스 속에서 준수하는가?
- 복합 압박: 시간 + 매몰비용 + 피로
- 합리화를 식별하고 명시적 반박을 추가

**성공 기준:** 최대 압박 속에서도 에이전트가 규칙을 따른다

### 기법 스킬 (방법 가이드)

**예:** condition-based-waiting, root-cause-tracing, defensive-programming

**테스트 방법:**
- 적용 시나리오: 기법을 올바르게 적용할 수 있는가?
- 변형 시나리오: 엣지 케이스를 처리하는가?
- 정보 누락 테스트: 지침에 빈틈이 있는가?

**성공 기준:** 에이전트가 새 시나리오에 기법을 성공적으로 적용한다

### 패턴 스킬 (사고 모델)

**예:** reducing-complexity, information-hiding 개념

**테스트 방법:**
- 인식 시나리오: 패턴이 해당되는 때를 알아보는가?
- 적용 시나리오: 사고 모델을 쓸 수 있는가?
- 반례: 적용하지 **말아야** 할 때를 아는가?

**성공 기준:** 에이전트가 언제/어떻게 패턴을 적용할지 올바로 식별한다

### 참조 스킬 (문서/API)

**예:** API 문서, 명령 참조, 라이브러리 가이드

**테스트 방법:**
- 검색 시나리오: 올바른 정보를 찾을 수 있는가?
- 적용 시나리오: 찾은 것을 올바르게 쓸 수 있는가?
- 빈틈 테스트: 흔한 사용 사례가 커버되는가?

**성공 기준:** 에이전트가 참조 정보를 찾아 올바르게 적용한다

## 테스트를 건너뛰는 흔한 합리화

| 변명 | 실제 |
|---|---|
| "스킬이 명백히 명확해" | 나에게 명확 ≠ 다른 에이전트에게 명확. 테스트하라. |
| "그냥 참조 문서인데" | 참조에도 빈틈과 불명확한 부분이 있을 수 있다. 검색을 테스트하라. |
| "테스트는 과해" | 테스트하지 않은 스킬에는 **항상** 문제가 있다. 15분 테스트가 몇 시간을 아낀다. |
| "문제가 생기면 그때 테스트할게" | 문제 = 에이전트가 스킬을 못 쓴다. 배포 **전에** 테스트하라. |
| "테스트가 너무 번거로워" | 프로덕션에서 나쁜 스킬을 디버깅하는 것보다 덜 번거롭다. |
| "좋다고 확신해" | 과신은 문제를 보장한다. 그래도 테스트하라. |
| "학술적 검토면 충분해" | 읽기 ≠ 쓰기. 적용 시나리오를 테스트하라. |
| "테스트할 시간이 없어" | 테스트 안 한 스킬을 배포하면 나중에 고치는 데 더 많은 시간을 쓴다. |

**전부 같은 뜻이다: 배포 전에 테스트하라. 예외 없음.**

## 실패 유형에 형식을 맞춰라 (Match the Form to the Failure)

지침을 쓰기 전에 **기준선 실패를 분류한다.** 한 실패 유형을 방탄으로 만드는 형식이 다른 유형에서는 측정 가능하게 **역효과**를 낸다.

| 기준선 실패 | 맞는 형식 | 틀린 형식 |
|---|---|---|
| 압박 속에 규칙을 건너뛰거나 위반 (알면서도 어김) | 금지 + 합리화 표 + 위험 신호 (아래 방탄화 참고) | 부드러운 안내 ("~을 선호", "~을 고려") |
| 준수는 하지만 출력의 **모양**이 틀림 (프롬프트가 비대함, 평결이 묻힘, 스펙을 되풀이함) | **긍정적 레시피 또는 계약**: 출력이 **무엇인지** — 그 부분들을, 순서대로 — 진술 | 금지 목록 ("되풀이하지 마라", "서술하지 마라") |
| 이미 만들고 있는 것에서 필수 요소를 **누락** | **구조적**: 채우는 템플릿 안의 REQUIRED 필드나 슬롯 | 템플릿 근처의 산문 알림 |
| 동작이 조건에 의존해야 함 | **관측 가능한 술어**에 연결된 조건문 ("브리프가 있으면 그것을 참조하라") | 무조건 규칙 + 예외 조항 |

**왜 금지가 모양 문제에서 역효과를 내나:** 경쟁하는 유인("프롬프트를 자족적으로 만들어라")이 있으면 에이전트는 "X 하지 마라"와 **협상한다**. 파견 프롬프트 지침에 대한 맞대결 워딩 테스트에서, 금지 쪽이 원치 않는 내용을 **분명히 더 많이** 만들어냈고(분포가 완전히 분리됨), 심지어 **무지침 대조군보다도 나쁜** 경향을 보였다. 가정하지 말고 자기 경우를 마이크로 테스트하되, 절대 기본값으로 금지에 손을 뻗지 마라. 레시피는 협상할 것을 남기지 않는다: 출력이 진술된 모양에 맞거나 맞지 않거나다.

**어떤 형식을 고르든 지켜야 할 규칙:**
- **뉘앙스 조항 금지.** "중요한 경우가 아니면 X 하지 마라"는 협상을 다시 연다 — 이기고 있던 레시피에 뉘앙스 조항 하나를 덧붙이자 같은 워딩 테스트에서 일관성이 노이즈로 떨어졌다. 진짜 예외는 **관측 가능한 술어에 대한 자기만의 조건문**으로 표현하라
- **예외 조항은 범위를 한정하지 못한다.** "이 제한은 코드 블록에는 적용되지 않는다"고 해도 여전히 코드 블록을 억제한다. 출력의 일부가 면제되어야 한다면, 규칙이 거기 닿을 수 없도록 구조를 다시 짜라

## 합리화에 대한 스킬 방탄화

규율을 강제하는 스킬(TDD 같은)은 합리화에 저항해야 한다. 에이전트는 똑똑하고, 압박을 받으면 빠져나갈 구멍을 찾아낸다.

**범위:** 이 도구 모음은 **규율 실패** — 규칙을 알면서 압박 속에 건너뛰는 에이전트 — 를 위한 것이다. 모양이 틀린 출력이나 누락된 요소에는 금지 기반 방탄화가 역효과를 낸다. 위 "실패 유형에 형식을 맞춰라"의 형식을 쓰라.

**심리학 노트:** 설득 기법이 **왜** 통하는지 이해하면 체계적으로 적용할 수 있다. 권위, 일관성, 희소성, 사회적 증거, 유대 원리에 대한 연구 기반(Cialdini, 2021; Meincke et al., 2025)은 원문 디렉터리의 `persuasion-principles.md`를 참고한다.

### 모든 구멍을 명시적으로 막아라

규칙만 진술하지 말고 **구체적인 우회로를 금지**한다:

나쁨:
```markdown
Write code before test? Delete it.
```

좋음:
```markdown
Write code before test? Delete it. Start over.

**No exceptions:**
- Don't keep it as "reference"
- Don't "adapt" it while writing tests
- Don't look at it
- Delete means delete
```

### "정신 대 문자" 논변을 다뤄라

기초 원칙을 앞쪽에 넣는다:

```markdown
**Violating the letter of the rules is violating the spirit of the rules.**
```

이것이 "나는 정신을 따르고 있다"는 합리화 부류 전체를 차단한다.

### 합리화 표를 만들어라

기준선 테스트에서 나온 합리화를 포착한다(아래 테스트 절 참고). 에이전트가 하는 모든 변명이 표에 들어간다:

```markdown
| Excuse | Reality |
|--------|---------|
| "Too simple to test" | Simple code breaks. Test takes 30 seconds. |
| "I'll test after" | Tests passing immediately prove nothing. |
| "Tests after achieve same goals" | Tests-after = "what does this do?" Tests-first = "what should this do?" |
```

### 위험 신호 목록을 만들어라

에이전트가 합리화 중임을 스스로 점검하기 쉽게 만든다:

```markdown
## Red Flags - STOP and Start Over

- Code before test
- "I already manually tested it"
- "Tests after achieve the same purpose"
- "It's about spirit not ritual"
- "This is different because..."

**All of these mean: Delete code. Start over with TDD.**
```

### 위반 증상으로 SDO를 갱신하라

규칙을 **위반하기 직전**의 증상을 description에 추가한다:

```yaml
description: use when implementing any feature or bugfix, before writing implementation code
```

## 스킬을 위한 RED-GREEN-REFACTOR

TDD 사이클을 따른다.

### RED: 실패하는 테스트 작성 (기준선)

스킬 **없이** 서브에이전트로 압박 시나리오를 돌린다. 정확한 동작을 기록한다:
- 어떤 선택을 했는가?
- 어떤 합리화를 썼는가 (**그대로**)?
- 어떤 압박이 위반을 유발했는가?

이것이 "테스트가 실패하는 것을 지켜보기"다 — 스킬을 쓰기 전에 에이전트가 자연스럽게 무엇을 하는지 봐야 한다.

### GREEN: 최소한의 스킬 작성

그 구체적인 합리화들을 겨냥한 스킬을 쓴다. 가상의 경우를 위한 추가 내용을 넣지 않는다.

같은 시나리오를 스킬과 함께 돌린다. 이제 에이전트가 준수해야 한다.

### REFACTOR: 구멍 막기

에이전트가 새 합리화를 찾았는가? 명시적 반박을 추가한다. 방탄이 될 때까지 재테스트한다.

### 전체 시나리오 전에 워딩을 마이크로 테스트하라

전체 압박 시나리오 실행이 최종 관문이지만, 반복당 느리고 비싸다. 먼저 **워딩 자체**를 마이크로 테스트로 검증한다:

1. **호출당 새 컨텍스트 샘플 하나** — 원시 API 호출, 또는 API 접근이 없으면 단발성 서브에이전트. 시스템 프롬프트 = 이 지침이 살게 될 **현실적인 컨텍스트**(지침만 떼어낸 것이 아니라 전체 스킬이나 프롬프트 템플릿), 사용자 메시지 = 그 실패를 유혹하는 작업
2. **항상 무지침 대조군을 포함한다.** 대조군이 그 실패를 보이지 않으면 고칠 것이 없다 — 멈추고 지침을 쓰지 마라
3. **변형당 5회 이상.** 단일 샘플은 거짓말한다
4. **표시된 모든 매치를 직접 읽는다.** 프로그램으로 점수를 매겨도 좋지만, 템플릿 반향과 인용된 반례가 히트인 척한다. 자동 집계만으로는 실패와 성공을 둘 다 과장한다
5. **분산도 지표다.** 지침이 먹히면 반복들이 같은 모양으로 수렴한다. 5회에서 다섯 가지 해석이 나오면 워딩이 구속력이 없는 것이다 — 단어를 늘리기 전에 **형식**을 조인다

마이크로 테스트는 워딩을 검증할 뿐, 규율 스킬의 압박 시나리오를 대체하지 않는다.

**테스트 방법론:** 완전한 테스트 방법론은 원문 디렉터리의 `testing-skills-with-subagents.md`를 참고한다:
- 압박 시나리오 작성법
- 압박 유형 (시간, 매몰비용, 권위, 피로)
- 체계적으로 구멍 막기
- 메타 테스트 기법

## 안티패턴

### ❌ 서사적 예시
"2025-10-03 세션에서 빈 projectDir이 ...를 유발한다는 것을 발견했다"
**왜 나쁜가:** 너무 구체적이고 재사용 불가

### ❌ 다중 언어 희석
example-js.js, example-py.py, example-go.go
**왜 나쁜가:** 품질이 평범해지고 유지보수 부담

### ❌ 플로차트 안의 코드
```dot
step1 [label="import fs"];
step2 [label="read file"];
```
**왜 나쁜가:** 복사·붙여넣기 불가, 읽기 어려움

### ❌ 일반적인 라벨
helper1, helper2, step3, pattern4
**왜 나쁜가:** 라벨에는 의미가 있어야 한다

## 멈춤: 다음 스킬로 넘어가기 전에

**스킬을 쓴 뒤에는 반드시 멈추고 배포 프로세스를 완료해야 한다.**

**하지 말 것:**
- 각각을 테스트하지 않고 여러 스킬을 한꺼번에 만들기
- 현재 스킬이 검증되기 전에 다음 스킬로 넘어가기
- "묶어서 하는 게 효율적"이라며 테스트 건너뛰기

**아래 배포 체크리스트는 스킬마다 필수다.**

테스트되지 않은 스킬을 배포하는 것 = 테스트되지 않은 코드를 배포하는 것. 품질 기준 위반이다.

## 스킬 작성 체크리스트 (TDD 적응판)

**중요: 아래 체크리스트 항목마다 todo를 만든다.**

**RED 단계 — 실패하는 테스트 작성:**
- [ ] 압박 시나리오 작성 (규율 스킬은 3개 이상 복합 압박)
- [ ] 스킬 **없이** 시나리오 실행 — 기준선 동작을 그대로 기록
- [ ] 합리화/실패의 패턴 식별

**GREEN 단계 — 최소한의 스킬 작성:**
- [ ] 이름이 영문자, 숫자, 하이픈만 사용 (괄호/특수문자 없음)
- [ ] YAML 프론트매터에 필수 `name`과 `description` (최대 1024자, [스펙](https://agentskills.io/specification) 참고)
- [ ] description이 "Use when..."으로 시작하고 구체적 트리거/증상 포함
- [ ] description이 3인칭으로 작성됨
- [ ] 검색을 위한 키워드가 전체에 분포 (에러, 증상, 도구)
- [ ] 핵심 원칙이 담긴 명확한 개요
- [ ] RED에서 식별한 구체적 기준선 실패를 다룸
- [ ] 지침 형식이 실패 유형에 맞음 (실패 유형에 형식을 맞춰라 참고)
- [ ] 동작을 형성하는 지침이라면: 무지침 대조군 대비 워딩 마이크로 테스트 완료 (5회 이상, 표시된 매치 전부 직접 판독) — 순수 참조 스킬은 해당 없음
- [ ] 코드가 인라인이거나 별도 파일로 링크됨
- [ ] 탁월한 예시 하나 (다중 언어 아님)
- [ ] 스킬과 함께 시나리오 실행 — 이제 에이전트가 준수하는지 확인

**REFACTOR 단계 — 구멍 막기:**
- [ ] 테스트에서 나온 **새** 합리화 식별
- [ ] 명시적 반박 추가 (규율 스킬이면)
- [ ] 모든 테스트 반복에서 나온 합리화 표 구축
- [ ] 위험 신호 목록 작성
- [ ] 방탄이 될 때까지 재테스트

**품질 점검:**
- [ ] 판단이 명백하지 않을 때만 작은 플로차트
- [ ] 빠른 참조 표
- [ ] 흔한 실수 섹션
- [ ] 서사적 스토리텔링 없음
- [ ] 보조 파일은 도구나 무거운 참조에만

**배포:**
- [ ] 스킬을 git에 커밋하고 포크에 푸시 (설정되어 있다면)
- [ ] 폭넓게 유용하다면 PR로 기여를 고려

## 발견 워크플로

미래의 에이전트가 이 스킬을 찾는 과정:

1. **문제를 만난다** ("테스트가 flaky하다")
2. **스킬을 검색한다** (description을 grep, 카테고리를 훑음)
3. **SKILL을 찾는다** (description이 일치)
4. **개요를 훑는다** (이게 관련 있나?)
5. **패턴을 읽는다** (빠른 참조 표)
6. **예시를 로드한다** (구현할 때만)

**이 흐름에 최적화한다** — 검색 가능한 용어를 앞쪽에, 자주 넣는다.

## 함께 있는 파일 (원문)

- `testing-skills-with-subagents.md` — 완전한 테스트 방법론
- `anthropic-best-practices.md` — Anthropic 공식 스킬 작성 가이드
- `persuasion-principles.md` — 설득 원리 연구 기반
- `graphviz-conventions.dot` — 다이어그램 스타일 규칙
- `render-graphs.js` — 플로차트를 SVG로 렌더링
- `examples/` — 예시 모음
