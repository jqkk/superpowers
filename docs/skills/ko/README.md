# Superpowers 스킬 레퍼런스 (한국어)

`skills/*/SKILL.md` 15개를 한국어로 정리한 참조 문서입니다.

> **정본은 영어 원문입니다.** 에이전트가 실제로 읽고 따르는 것은 `skills/<이름>/SKILL.md`이고,
> 이 디렉터리는 사람이 읽기 위한 번역·요약입니다. 원문과 내용이 다르면 원문이 맞습니다.
>
> 각 스킬의 `description:` 프론트매터는 **에이전트의 트리거 조건**이라 번역하지 않고 원문 그대로 인용했습니다.
> (AGENTS.md: 스킬 내용 변경은 eval 증거가 필요하며, 트리거 문구는 특히 민감합니다.)

## 전체 흐름

```
using-superpowers  ← 세션 시작 시 항상 로드되는 진입점
        │
        ▼
  brainstorming ──────────────────────────────┐
  (spike / bounded / architectural 분류)       │ spike → 답변만
        │ architectural                        │ bounded → 짧은 설계 승인 후 바로 구현
        ▼                                      │
  writing-plans  (계획 문서 작성 + 실행 방식 선택)│
        │                                      │
   ┌────┴─────────────────┐                    │
   ▼                      ▼                    │
executing-plans   subagent-driven-development   │
(인라인 실행)       (태스크별 서브에이전트)        │
   └────────┬─────────────┘                    │
            ▼                                  │
finishing-a-development-branch  ←───────────────┘
(머지 / PR / 정리)

상시 적용:  test-driven-development · systematic-debugging · verification-before-completion
보조:      using-git-worktrees · dispatching-parallel-agents
리뷰:      requesting-code-review · receiving-code-review
메타:      writing-skills · diagnosing-superpowers
```

## 한눈에 보기

| 스킬 | 언제 발동하나 | 핵심 한 줄 |
|---|---|---|
| [using-superpowers](using-superpowers.md) | 모든 대화 시작 시 | 1%라도 해당되면 스킬을 먼저 호출한다 |
| [brainstorming](brainstorming.md) | 무언가 만들기 전 | spike/bounded/architectural로 분류하고 승인받기 전엔 구현 금지 |
| [writing-plans](writing-plans.md) | 스펙이 나왔고 다단계 작업일 때 | 컨텍스트 0인 엔지니어용 계획서, 스텝당 2~5분 |
| [executing-plans](executing-plans.md) | 인라인 실행을 선택했을 때 | 원장에 기록하며 혼자 실행, 마지막에 전체 리뷰 1회 |
| [subagent-driven-development](subagent-driven-development.md) | 태스크가 독립적일 때 | 태스크마다 새 구현자 + 새 리뷰어, 수정 루프 최대 5라운드 |
| [test-driven-development](test-driven-development.md) | 모든 기능·버그픽스 구현 전 | 실패하는 테스트 없이 프로덕션 코드 없음 |
| [systematic-debugging](systematic-debugging.md) | 모든 버그·테스트 실패·이상 동작 | 근본 원인 조사 없이 수정 없음, 4단계 |
| [verification-before-completion](verification-before-completion.md) | "다 됐다"고 말하기 직전 | 방금 실행한 검증 증거 없이는 주장 금지 |
| [requesting-code-review](requesting-code-review.md) | 태스크·기능 완료 후, 머지 전 | 리뷰어 서브에이전트를 띄운다, 내 컨텍스트로 diff를 읽지 않는다 |
| [receiving-code-review](receiving-code-review.md) | 리뷰 피드백을 받았을 때 | 감사·동조 표현 금지, 코드베이스와 대조 검증 먼저 |
| [finishing-a-development-branch](finishing-a-development-branch.md) | 구현 완료 + 테스트 통과 | 정확히 3개 옵션을 제시하고 사람이 고른다 |
| [using-git-worktrees](using-git-worktrees.md) | 격리된 작업 공간이 필요할 때 | 감지 먼저 → 네이티브 도구 → git 폴백 |
| [dispatching-parallel-agents](dispatching-parallel-agents.md) | 독립적인 작업이 2개 이상 | 한 응답에 여러 디스패치 = 병렬 |
| [writing-skills](writing-skills.md) | 스킬을 만들거나 고칠 때 | 스킬 작성 = 문서에 적용한 TDD |
| [diagnosing-superpowers](diagnosing-superpowers.md) | 세션이 이상하게 흘러갔을 때 | 증거(`path:line`)와 함께 보고만, 진단은 하지 않음 |

## 처음 읽는다면

1. [using-superpowers](using-superpowers.md) — 시스템이 어떻게 작동하는지
2. [brainstorming](brainstorming.md) — 가장 자주 발동하는 프로세스 스킬
3. [test-driven-development](test-driven-development.md) — 구현 중 항상 적용됨
4. [systematic-debugging](systematic-debugging.md) — 버그를 만나면 항상 적용됨

나머지는 해당 상황이 왔을 때 찾아보면 됩니다.
