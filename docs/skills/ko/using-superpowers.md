# using-superpowers — 슈퍼파워 사용하기

> 원문: [`skills/using-superpowers/SKILL.md`](../../../skills/using-superpowers/SKILL.md)
> 트리거(원문 유지): `Use when starting any conversation - establishes how to find and use skills, requiring skill invocation before ANY response including clarifying questions`
> 이 문서는 사람이 읽기 위한 번역본입니다. 에이전트가 따르는 정본은 영어 원문입니다.

---

<서브에이전트-중단>
특정 작업을 수행하라고 서브에이전트로 파견되었다면, 이 스킬은 무시한다.
</서브에이전트-중단>

<매우-중요>
지금 하려는 일에 어떤 스킬이 해당될 가능성이 **1%라도** 있다고 생각한다면, 반드시 그 스킬을 호출해야 한다.

작업에 스킬이 해당된다면, 선택지는 없다. 반드시 사용해야 한다.

이것은 협상 대상이 아니다. 합리화로 빠져나갈 수 없다.
</매우-중요>

## 규칙

**관련되거나 요청된 스킬을 어떤 응답이나 행동보다 먼저 호출한다** — 명확화 질문, 코드베이스 탐색, 파일 확인보다도 먼저다. 막상 열어보니 상황에 맞지 않는다면 쓰지 않아도 된다.

**플랜 모드 진입 전:** 아직 브레인스토밍을 하지 않았다면 brainstorming 스킬을 먼저 호출한다.

그다음 "Using [스킬] to [목적]"이라고 선언하고 스킬을 정확히 따른다. 체크리스트가 있다면 항목마다 todo를 하나씩 만든다.

## 스킬 우선순위

여러 스킬이 해당될 때는 **프로세스 스킬이 먼저**다. 프로세스 스킬이 접근 방식을 정하고, 그다음 구현 스킬(frontend-design 등)이 그것을 수행한다. brainstorming과 systematic-debugging이 Superpowers에서 가장 흔한 프로세스 스킬이지만, 규칙은 모든 스킬에 적용된다.

- "X를 만들자" → superpowers:brainstorming 먼저, 그다음 구현 스킬
- "이 버그 고쳐줘" → superpowers:systematic-debugging 먼저, 그다음 도메인 스킬

## 위험 신호 (Red Flags)

아래와 같은 생각이 들면 멈춰라 — 합리화하고 있는 것이다.

| 생각 | 실제 |
|---|---|
| "이건 그냥 간단한 질문인데" | 질문도 작업이다. 스킬을 확인하라. |
| "컨텍스트가 더 필요해" | 스킬 확인이 명확화 질문보다 먼저다. |
| "코드베이스부터 좀 보고" | 스킬이 *어떻게* 탐색할지 알려준다. 먼저 확인하라. |
| "git이나 파일 정도는 빨리 볼 수 있잖아" | 파일에는 대화의 맥락이 없다. 스킬을 확인하라. |
| "정보부터 모으자" | 스킬이 *어떻게* 정보를 모을지 알려준다. |
| "이 정도에 정식 스킬까지는" | 스킬이 존재하면 사용한다. |
| "그 스킬 기억나" | 스킬은 계속 바뀐다. 현재 버전을 읽어라. |
| "이건 작업이라고 하기엔" | 행동 = 작업. 스킬을 확인하라. |
| "스킬은 과하다" | 간단한 일이 복잡해진다. 사용하라. |
| "일단 이것 하나만 먼저" | 무엇이든 하기 **전에** 확인하라. |
| "지금 생산적인 느낌인데" | 규율 없는 행동은 시간을 낭비한다. 스킬이 그것을 막는다. |
| "그게 무슨 뜻인지 알아" | 개념을 아는 것 ≠ 스킬을 사용하는 것. 호출하라. |

## 플랫폼 적응

자신의 하네스가 아래에 있다면, 해당 참조 파일을 읽고 특수 지침을 따른다.

- Claude Code: `references/claude-code-tools.md`
- Codex: `references/codex-tools.md`
- Pi: `references/pi-tools.md`
- Antigravity: `references/antigravity-tools.md`
- Hermes Agent: `references/hermes-tools.md`
- Muse: `references/muse-tools.md`

## 사용자 지침

사용자 지침(CLAUDE.md, AGENTS.md, GEMINI.md 등, 직접 요청)이 스킬보다 우선하고, 스킬이 기본 동작보다 우선한다. 스킬의 워크플로나 지침을 건너뛰는 것은 사람 파트너가 명시적으로 그렇게 하라고 했을 때뿐이다.
