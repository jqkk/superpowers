# using-git-worktrees — git 워크트리 사용

> 원문: [`skills/using-git-worktrees/SKILL.md`](../../../skills/using-git-worktrees/SKILL.md)
> 트리거(원문 유지): `Use when starting feature work that needs isolation from current workspace or before executing implementation plans - ensures an isolated workspace exists via native tools or git worktree fallback`
> 이 문서는 사람이 읽기 위한 번역본입니다. 에이전트가 따르는 정본은 영어 원문입니다.

---

## 개요

작업이 격리된 공간에서 일어나게 한다. 플랫폼의 네이티브 워크트리 도구를 선호한다. 네이티브 도구가 없을 때만 수동 git 워크트리로 폴백한다.

**핵심 원칙:** 기존 격리를 먼저 감지한다. 그다음 네이티브 도구. 그다음 git 폴백. **하네스와 싸우지 않는다.**

**시작할 때 선언:** "using-git-worktrees 스킬을 사용해 격리된 작업 공간을 준비합니다."

## 0단계: 기존 격리 감지

**무엇을 만들기 전에, 이미 격리된 공간에 있는지 확인한다.**

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

**서브모듈 가드:** `GIT_DIR != GIT_COMMON`은 git 서브모듈 안에서도 참이다. "이미 워크트리다"라고 결론 내리기 전에 서브모듈이 아닌지 확인한다:

```bash
# 경로가 반환되면 워크트리가 아니라 서브모듈이다 — 일반 저장소로 취급
git rev-parse --show-superproject-working-tree 2>/dev/null
```

**`GIT_DIR != GIT_COMMON`이고 서브모듈이 아니면:** 이미 연결된 워크트리 안에 있다. 2단계(프로젝트 셋업)로 건너뛴다. **또 다른 워크트리를 만들지 않는다.**

브랜치 상태와 함께 보고한다:
- 브랜치 위: "이미 `<경로>`의 격리된 작업 공간, 브랜치 `<이름>`에 있습니다."
- detached HEAD: "이미 `<경로>`의 격리된 작업 공간에 있습니다 (detached HEAD, 외부 관리). 마무리 시점에 브랜치 생성이 필요합니다."

**`GIT_DIR == GIT_COMMON`이거나 서브모듈이면:** 일반 저장소 체크아웃이다.

사용자가 이미 지침에서 워크트리 선호를 밝혔는가? 아니라면 워크트리를 만들기 전에 동의를 구한다:

> "격리된 워크트리를 준비할까요? 현재 브랜치를 변경으로부터 보호해 줍니다."

이미 선언된 선호가 있으면 묻지 말고 따른다. 사용자가 거절하면 제자리에서 작업하고 2단계로 건너뛴다.

## 1단계: 격리된 작업 공간 생성

**두 가지 방법이 있다. 이 순서대로 시도한다.**

### 1a. 네이티브 워크트리 도구 (선호)

사용자가 격리된 공간을 요청했다(0단계 동의). 워크트리를 만들 수단이 이미 있는가? `EnterWorktree`, `WorktreeCreate` 같은 이름의 도구, `/worktree` 명령, `--worktree` 플래그일 수 있다. 있다면 그것을 쓰고 2단계로 건너뛴다.

네이티브 도구는 디렉터리 배치, 브랜치 생성, 정리를 자동으로 처리한다. **네이티브 도구가 있는데 `git worktree add`를 쓰면 하네스가 보지도 관리하지도 못하는 유령 상태가 생긴다.**

네이티브 워크트리 도구가 없을 때만 1b로 진행한다.

### 1b. git 워크트리 폴백

**1a가 적용되지 않을 때만 쓴다** — 네이티브 워크트리 도구가 없는 경우. git으로 수동으로 워크트리를 만든다.

#### 디렉터리 선택

이 우선순위를 따른다. **명시적인 사용자 선호가 관찰된 파일시스템 상태를 항상 이긴다.**

1. **지침에 선언된 워크트리 디렉터리 선호가 있는지 확인한다.** 사용자가 이미 지정했으면 묻지 말고 쓴다
2. **기존 프로젝트 로컬 워크트리 디렉터리를 확인한다:**
   ```bash
   ls -d .worktrees 2>/dev/null     # 선호 (숨김)
   ls -d worktrees 2>/dev/null      # 대안
   ```
   있으면 쓴다. 둘 다 있으면 `.worktrees`가 이긴다
3. **다른 안내가 없으면** 프로젝트 루트의 `.worktrees/`를 기본으로 한다

#### 안전 검증 (프로젝트 로컬 디렉터리에만)

**워크트리를 만들기 전에 디렉터리가 무시되는지 반드시 확인한다:**

```bash
git check-ignore -q .worktrees 2>/dev/null || git check-ignore -q worktrees 2>/dev/null
```

**무시되지 않으면:** .gitignore에 추가하고, 그 변경을 커밋한 뒤 진행한다.

**왜 중요한가:** 워크트리 내용을 실수로 저장소에 커밋하는 것을 막는다.

#### 워크트리 생성

```bash
# 선택한 위치에 따라 경로 결정
path="$LOCATION/$BRANCH_NAME"

git worktree add "$path" -b "$BRANCH_NAME"
cd "$path"
```

**샌드박스 폴백:** `git worktree add`가 권한 에러(샌드박스 거부)로 실패하면, 샌드박스가 워크트리 생성을 막아서 현재 디렉터리에서 작업한다고 사용자에게 알린다. 그다음 셋업과 기준선 테스트를 제자리에서 실행한다.

## 2단계: 프로젝트 셋업

자동 감지하고 적절한 셋업을 실행한다:

```bash
# Node.js
if [ -f package.json ]; then npm install; fi

# Rust
if [ -f Cargo.toml ]; then cargo build; fi

# Python
if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
if [ -f pyproject.toml ]; then poetry install; fi

# Go
if [ -f go.mod ]; then go mod download; fi
```

## 3단계: 깨끗한 기준선 확인

작업 공간이 깨끗하게 시작하는지 테스트를 돌린다:

```bash
# 프로젝트에 맞는 명령을 쓴다
npm test / cargo test / pytest / go test ./...
```

**테스트가 실패하면:** 실패를 보고하고, 진행할지 조사할지 묻는다.
**테스트가 통과하면:** 준비됨을 보고한다.

### 보고

```
워크트리 준비됨: <전체 경로>
테스트 통과 (<N>개, 실패 0)
<기능명> 구현 준비 완료
```

## 빠른 참조

| 상황 | 행동 |
|---|---|
| 이미 연결된 워크트리 안 | 생성을 건너뜀 (0단계) |
| 서브모듈 안 | 일반 저장소로 취급 (0단계 가드) |
| 네이티브 워크트리 도구 있음 | 그것을 사용 (1a단계) |
| 네이티브 도구 없음 | git 워크트리 폴백 (1b단계) |
| `.worktrees/` 존재 | 그것을 사용 (무시 여부 확인) |
| `worktrees/` 존재 | 그것을 사용 (무시 여부 확인) |
| 둘 다 존재 | `.worktrees/` 사용 |
| 둘 다 없음 | 지침 파일 확인, 그다음 기본 `.worktrees/` |
| 디렉터리가 무시되지 않음 | .gitignore에 추가 + 커밋 |
| 생성 시 권한 에러 | 샌드박스 폴백, 제자리에서 작업 |
| 기준선 테스트 실패 | 실패 보고 + 질문 |
| package.json/Cargo.toml 없음 | 의존성 설치 건너뜀 |

## 흔한 합리화

| 변명 | 실제 |
|---|---|
| "딱 봐도 워크트리가 아닌데 확인할 필요 있나" | 0단계를 실행하라. 하네스가 만든 격리와 서브모듈은 둘 다 눈대중을 속인다. 감지 명령이 결정한다. |
| "네이티브 도구 찾느니 `git worktree add`가 빠르지" | 네이티브 도구(예: `EnterWorktree`)가 배치·브랜치·정리를 소유한다. 우회하는 것이 1번 실수다 — 하네스가 보지도 관리하지도 못하는 유령 상태를 만든다. |
| "워크트리 디렉터리는 당연히 이미 무시되겠지" | `git check-ignore`를 실행하라. 무시되지 않은 워크트리 디렉터리는 트리 전체를 저장소에 커밋한다. |
| "디렉터리 이름은 아무거나 돼" | 명시적 지침 > 기존 프로젝트 로컬 디렉터리 > `.worktrees/` 기본값. |
| "새 작업 공간인데 기준선 테스트는 나중에" | 더러운 기준선은 이후의 모든 실패를 모호하게 만든다. 지금 테스트를 돌려라. 실패를 무릅쓰고 진행할지는 사람 파트너가 정한다. |
