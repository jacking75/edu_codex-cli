# 🚀 OpenAI Codex CLI 완전 정리 가이드
  
## **Part 1: 설치와 인증 — 빠른 시작**
 
### 1.1 Codex CLI란 무엇인가

Codex CLI는 OpenAI의 로컬 터미널용 코딩 에이전트다. 공식 GitHub 저장소를 보면 Rust 중심으로 구현된 오픈소스 프로젝트임을 확인할 수 있다. macOS, Windows(PowerShell 또는 WSL2), Linux를 모두 지원하며, Claude Code 요금 부담 때문에 다시 살펴보는 개발자들이 많아지고 있는 추세다.

**핵심 용어 정리:**
- **CLI**: 터미널에 명령어를 입력해서 쓰는 도구 (마우스 GUI 앱이 아님)
- **터미널**: macOS의 Terminal, Windows의 PowerShell처럼 글자로 명령을 실행하는 창
- **저장소(repo)**: 프로젝트 코드가 들어 있는 폴더

### 1.2 설치 방법 3가지

**방법 1 — npm (가장 범용적)**

Node.js가 이미 설치된 환경이라면 이 방법이 가장 빠르다.

```bash
npm install -g @openai/codex
```

최신 버전으로 업데이트할 때:
```bash
npm install -g @openai/codex@latest
```

**방법 2 — Homebrew (macOS 전용)**

2026년 5월 기준 공식 문서와 GitHub README의 명령어 표기가 다소 다르므로 주의가 필요하다.

```bash
# GitHub README(https://github.com/openai/codex) 기준
brew install --cask codex

# 공식 quickstart(developers.openai.com/codex/quickstart) 기준
brew install codex
```

처음 설치할 때는 공식 quickstart의 `brew install codex`를 먼저 시도하고, 실패하면 `--cask` 방식을 사용한다.

**방법 3 — 바이너리 직접 다운로드**

npm이나 Homebrew를 사용할 수 없는 환경(서버, 폐쇄망 등)이라면 [GitHub Releases](https://github.com/openai/codex)에서 플랫폼별 바이너리를 직접 내려받을 수 있다. macOS(Apple Silicon/x86_64), Linux(x86_64/arm64)를 지원한다.

**IDE 확장으로 시작하는 방법 (터미널이 부담스럽다면)**

Codex는 터미널이 아닌 IDE에서도 사용할 수 있다. 공식 IDE 확장은 다음 에디터를 지원한다: VS Code, VS Code Insiders, Cursor, Windsurf 같은 VS Code 계열 에디터 및 JetBrains IDE. 설치 후 에디터 사이드바에서 Codex를 열고 ChatGPT 계정 또는 API 키로 로그인해 사용할 수 있다. 터미널 명령어가 낯설다면 플러그인 방식으로 먼저 시작해보는 것을 권장한다.

---

### 1.3 인증 방법 2가지

설치가 끝나면 터미널에 `codex`를 입력해 실행한다. 첫 실행 시 두 가지 인증 방식 중 하나를 선택한다.

**방법 A — ChatGPT 계정으로 로그인**

2026년 5월 기준 Codex는 ChatGPT Free, Go, Plus, Pro, Business, Edu, Enterprise 플랜에 포함된다. 브라우저가 자동으로 열리면 사용 중인 ChatGPT 계정으로 인증을 완료하고 터미널로 돌아와 Enter를 누르면 된다.

**방법 B — OpenAI API 키로 로그인**

CI/CD처럼 사람이 직접 개입하지 않는 자동화 흐름에 어울리는 방식이다. 다만 ChatGPT 계정 대신 API 키를 쓰면 GitHub 코드 리뷰나 Slack 같은 클라우드 기반 기능은 사용할 수 없다.

```bash
printenv OPENAI_API_KEY | codex login --with-api-key
```

**인증 방식 선택 가이드:**

| 상황 | 권장 방식 |
|---|---|
| 개인 개발, 대화형 작업 | ChatGPT 계정 로그인 |
| CI/CD, 자동화 스크립트 | API 키 인증 |
| 브라우저를 열 수 없는 서버/원격 환경 | `codex login --device-auth` |

**로그인 상태 확인 및 로그아웃:**

```bash
codex login status   # 현재 로그인 상태 확인
codex logout         # 로그아웃
```

**보안 주의사항**: 인증 정보는 기본적으로 `~/.codex/auth.json`에 저장된다. 이 파일은 비밀번호처럼 다뤄야 한다. Git에 커밋하거나 채팅에 붙여 넣으면 절대 안 된다.

---

### 1.4 처음 실행해보기

인증 후 프로젝트 디렉토리로 이동한 뒤 `codex`를 실행하면 터미널 UI(TUI)가 열린다.

**예시 1 — 프로젝트 분석 (첫 번째로 해볼 명령)**

```
Tell me about this project
```

또는 한국어로 `"해당 프로젝트에 대해 간단히 요약해줘"`처럼 입력해도 된다. Codex가 현재 폴더의 코드를 읽고 프로젝트 구조를 설명해준다.

**예시 2 — 기능 구현**

```
Build a classic Snake game in this repo
```

**예시 3 — 버그 수정**

```
Find and fix bugs in my codebase with minimal, high-confidence changes
```

`"minimal, high-confidence changes"`는 "작고 확실한 수정만 해달라"는 뜻이다. 범위를 지정하지 않으면 Codex가 생각보다 넓게 파일을 바꿀 수 있다.

**예시 4 — 기능 추가**

```
Add a new command-line option --json that outputs JSON.
```

---

### 1.5 좋은 프롬프트의 4요소 (공식 best practices)

공식 best practices 문서는 프롬프트에 다음 4가지 요소를 넣으라고 권장한다.

| 요소 | 설명 |
|---|---|
| **Goal** | 무엇을 만들거나 고치고 싶은가 |
| **Context** | 관련 파일, 오류 메시지, 참고 문서가 무엇인가 |
| **Constraints** | 건드리면 안 되는 범위나 지켜야 할 규칙은 무엇인가 |
| **Done when** | 어떤 테스트나 확인이 끝나야 완료로 볼 것인가 |

**실제 적용 예시:**

```
Goal: 로그인 오류를 고쳐줘.
Context: 에러 메시지는 "Invalid token"이고, 관련 파일은 src/auth.ts야.
Constraints: API 응답 형식은 바꾸지 말아줘.
Done when: 기존 테스트를 통과하고, 로그인 실패 케이스 테스트를 하나 추가해줘.
```

---

### 1.6 샌드박스와 승인 정책 — 핵심 안전 설정

Codex는 파일을 읽는 것뿐 아니라 파일을 편집하고 셸 명령도 실행할 수 있다. 그래서 "어디까지 자동으로 맡길 것인가"를 반드시 설정해야 한다.

**두 가지 안전 축:**
- **`sandbox_mode`**: Codex가 **어디까지 접근할 수 있는지**를 정한다 (파일 수정 범위, 네트워크 접근, 셸 명령 실행 범위)
- **`approval_policy`**: Codex가 작업하다가 **언제 사용자에게 물어볼지**를 정한다

**샌드박스 3단계:**

| 모드 | CLI 플래그 값 | 언제 쓰나 |
|---|---|---|
| **read-only** | `--sandbox read-only` | 낯선 저장소를 처음 분석할 때. 파일 수정이나 명령 실행은 막아두고 읽기만 할 때 |
| **workspace-write** | `--sandbox workspace-write` | 일반적인 로컬 개발 기본값. 현재 작업 폴더 안의 파일 읽기/수정 허용, 바깥 폴더 수정이나 네트워크는 기본 제한 |
| **danger-full-access** | `--sandbox danger-full-access` | 샌드박스 경계를 제거해야 하는 특수 상황. 로컬 입문자에게는 비권장 |

**처음에는 반드시 `workspace-write`로 시작하자.** 어느 파일을 바꾸는지 감이 잡히고 저장소를 충분히 신뢰할 수 있을 때만 권한을 넓힌다.

**승인 정책 3단계:**

| 값 | 의미 | 주의점 |
|---|---|---|
| **untrusted** | 신뢰된 명령 목록에 없는 명령을 실행하기 전에 사용자 승인을 요청 | 보수적으로 시작하고 싶을 때 유용 |
| **on-request** | 샌드박스 안에서 가능한 작업은 진행하고, 경계를 넘을 필요가 있을 때 승인 요청 | 일반적인 대화형 로컬 개발에 가장 무난 |
| **never** | 사용자 승인을 요청하지 않음. 실패는 곧바로 모델에게 돌아감 | 승인만 끄는 것이지 샌드박스를 자동으로 끄는 것은 아님 |

**상황별 권장 조합:**

| 상황 | 권장 조합 |
|---|---|
| 처음 보는 저장소를 읽기만 할 때 | `read-only + on-request` |
| 일반적인 로컬 개발 | `workspace-write + on-request` |
| CI/자동화 | `workspace-write` 기반으로 별도 설계 |

---

## **Part 2: 핵심 개념 4가지 — Prompting, Memories, Sandboxing, Models**

### 2.1 4개 개념 한눈에 보기

| 개념 | 결정하는 것 | 흔한 실패 |
|---|---|---|
| **프롬프트(Prompting)** | 이번 작업의 목표와 완료 기준 | 범위가 넓어져 엉뚱한 파일까지 수정 |
| **메모리(Memories/AGENTS.md)** | 반복해서 지킬 배경 규칙 | 매번 같은 설명을 다시 하거나 팀 규칙을 놓침 |
| **샌드박스(Sandboxing)** | Codex가 실제로 실행할 수 있는 경계 | 권한이 좁아 작업이 막히거나, 너무 넓어 위험해짐 |
| **모델(Models)** | 속도, 비용, 추론 깊이 | 가벼운 작업에 과한 모델을 쓰거나 복잡한 작업에 너무 약한 모델을 씀 |

**초심자 추천 순서:** 먼저 프롬프트로 할 일을 정한다 → 반복해서 지켜야 할 규칙은 AGENTS.md에 둔다 → 샌드박스로 Codex가 건드릴 수 있는 파일과 명령을 제한한다 → 마지막으로 작업 난이도와 비용에 맞춰 모델을 고른다.

---

### 2.2 프롬프트 원칙 4가지 (공식 Prompting 문서 기반)

**원칙 1 — 검증 가능성(Verifiability): 결과를 확인할 수 있게 만든다**

"이 버그를 고쳐라"보다 "버그를 고친 후 `npm test`로 회귀 테스트가 통과하는지 확인하라"처럼 검증 조건을 명시하면 Codex가 스스로 작업 완성 여부를 판단할 수 있다.

**원칙 2 — 작업 분해(Task Decomposition): 작게 나눌수록 잘 처리한다**

"게시판을 개선해줘"보다 "검색 버그를 고치고, 그다음 빈 결과 화면 문구를 바꿔줘"처럼 나눈다. 작은 단위의 작업은 Codex가 테스트하기도 쉽고 개발자가 검토하기도 쉽다.

**원칙 3 — 계획 먼저(Plan First): `/plan`으로 분해가 막힐 때**

분해 방법이 막힐 때는 Codex에게 먼저 계획을 제안하게 하면 된다. 큰 리팩토링, 마이그레이션, 원인 모르는 장애처럼 바로 수정하면 위험한 일은 먼저 `/plan`으로 접근하는 편이 안전하다. CLI에서 `Shift+Tab`으로 플랜 모드로 전환할 수 있다.

```
이 기능을 추가할 계획을 먼저 만들어라. 구현은 내가 검토한 뒤 진행하겠다.
```

**원칙 4 — 컨텍스트 제공(Context): 관련 파일과 이미지를 함께 넣는다**

CLI에서는 `/mention <path>`로 관련 파일을 현재 대화에 붙이는 방법이 가장 편리하다. IDE 확장은 열린 파일과 선택한 코드가 자동으로 컨텍스트에 포함된다.

---

### 2.3 실전 프롬프트 예시 5가지 (공식 문서 기반)

💡 **왜 영문 프롬프트인가?** Codex를 포함한 대부분의 AI 코딩 도구는 영문 프롬프트에서 더 정확하고 일관된 결과를 낸다. 학습 데이터의 대부분이 영어 코드베이스와 문서이기 때문이다. 실제 사용 시에는 영문으로 작성하는 것을 권장한다.

**예시 1 — 프로젝트 분석**

```
Analyze this project and provide a summary covering:
- Main purpose and architecture
- Key modules and their responsibilities
- Dependencies and how they interact
- Any potential issues or technical debt
```

한글 설명: 포함할 항목을 명시하면 Codex가 더 유용한 분석을 돌려준다.

**예시 2 — 기능 추가**

```
Add a new command-line option --json that outputs the result in JSON format
instead of the default human-readable output.
```

한글 설명: 새 옵션의 이름과 동작을 한 줄로 정했기 때문에 Codex가 어디서부터 어디까지 작업해야 하는지 판단하기 쉽다.

**예시 3 — 디버깅**

```
Debug the login failure. Steps to reproduce:
1. Run npm start
2. Navigate to /login
3. Enter valid credentials

The error appears: "Invalid token"
Relevant file: src/auth.ts

Do not change the API response format.
Reproduce the issue first, then propose a minimal patch.
```

한글 설명: 재현 단계를 구체적으로 적었고, 건드리면 안 되는 범위를 못 박았고, Codex가 먼저 문제를 재현한 뒤 패치를 제안하도록 순서를 정했다.

**예시 4 — 리팩토링 전 계획 세우기**

```
I need to refactor the authentication module. Before making any changes:
1. Analyze the current implementation in src/auth/
2. List all files that will be affected
3. Identify the test coverage gaps
4. Propose a step-by-step refactoring plan

Do not make any code changes yet. Wait for my approval.
```

한글 설명: Codex가 먼저 영향 범위와 테스트 기준을 정리하므로 개발자가 계획을 보고 범위를 줄이거나 빠진 조건을 보완한 뒤 실행으로 넘어갈 수 있다.

**예시 5 — 실패한 테스트 기준으로 원인 좁히기**

```
The test `UserService.createUser should validate email` is failing.
Run: npm test -- --grep "should validate email"

Do not modify the test itself.
The fix must maintain the existing UserService interface contract.
Done when: the specific test passes without breaking other UserService tests.
```

한글 설명: 실패한 테스트 이름, 바꾸면 안 되는 계약, 완료 조건을 함께 주면 Codex가 "대충 고쳐 보기"보다 원인을 좁혀서 움직이기 쉽다.

---

### 2.4 바이브 코딩 vs 구조화된 프롬프트

"넌 전문가야" 같은 역할 주입보다 `Constraints(제약)`가 더 정확하게 범위를 잡아준다. `"넌 바닐라 JS 전문가야"`는 스타일 힌트지만 `"외부 라이브러리 없이 index.html 하나로 동작해야 한다"`는 범위 제한이다. Codex는 후자를 훨씬 잘 따른다.

모델이 강해질수록 프롬프트의 최소 요건이 낮아진다. 구조를 알면 정확하게 쓸 수 있고, 몰라도 일단 시작할 수 있다.

---

### 2.5 실전 튜토리얼 프롬프트 3가지 (따라해보기)

세 예시 모두 **목표(Goal) → 설정(Setup) → 기능/규칙 → 제약(Constraints) → 완료 기준(Completion)** 구조를 따른다.

**예시 A — HTML 파일 하나로 슈팅 게임 만들기 (바이브 코딩 버전)**

```
갤러그 스타일 우주 슈팅 게임을 index.html 하나로 만들어줘.
Canvas API 사용, 외부 라이브러리 없이.
```

원문에 따르면 갓대희 작성자가 실제 테스트 결과 3분 34초 만에 개발을 완료했다. (모델: GPT-5.5, reasoning effort: high, fast 모드)

**예시 B — React 할 일 목록 앱 만들기 (바이브 코딩 버전)**

```
Vite + React로 할 일 목록 앱 만들어줘. useState만 쓰고 백엔드 없이.
```

결과: 서버를 직접 띄우고 브라우저까지 열어 테스트까지 자동 완료했다.

**예시 C — Next.js 소개 페이지 만들기 (바이브 코딩 버전)**

```
Next.js로 간단한 개인 소개 페이지 2개 만들어줘. DB 없이, 로그인 없이.
```

---

### 2.6 스레드(Thread) 관리

- **로컬 스레드**: 내 컴퓨터에서 샌드박스 안에 실행되는 일반 세션
- **클라우드 스레드**: 저장소를 별도로 가져와 원격 환경에서 작업 (장기 작업, 병렬 처리)
- 여러 스레드를 동시에 실행할 수는 있지만, **두 스레드가 같은 파일을 동시에 수정하지 않도록** 주의해야 한다

---

## **Part 3: Subagents와 Workflows — 큰 작업 나눠서 처리하기**
  
### 3.1 왜 작업을 쪼개야 하는가

하나의 대화창에서 요구사항 정리부터 디버깅, 문서 작성까지 모두 처리하면 두 가지 문제가 발생한다.

공식 서브에이전트 문서는 이를 다음과 같이 설명한다:

> "Context pollution: useful information gets buried under noisy intermediate output. Context rot: performance degrades as the conversation fills up with less relevant details."

- **Context pollution(컨텍스트 오염)**: 유용한 정보가 불필요한 중간 출력(로그, 스택 트레이스 등)에 묻혀 버린다
- **Context rot(컨텍스트 부식)**: 덜 관련된 세부 정보로 대화창이 채워지면서 성능이 점점 저하된다. 쉽게 말해 Codex가 처음 목표를 잊어버리기 시작한다

**해결 방법**: 메인 스레드에는 "요구사항·결정·최종 출력"만 남기고, 파일 탐색이나 테스트처럼 로그가 많이 나오는 일은 서브에이전트에게 따로 맡긴다.

---

### 3.2 핵심 용어 3가지

공식 서브에이전트 문서([링크](https://developers.openai.com/codex/concepts/subagents))는 세 가지 용어를 정의한다:

- **서브에이전트 워크플로(Subagent workflow)**: Codex가 여러 에이전트를 병렬로 실행하고 결과를 합치는 전체 흐름
- **서브에이전트(Subagent)**: 특정 작업을 담당하도록 Codex가 따로 띄운 보조 에이전트. 프리랜서에 가깝다
- **에이전트 스레드(Agent thread)**: CLI에서 `/agent`로 전환하거나 확인할 수 있는 에이전트별 독립 작업창

---

### 3.3 서브에이전트 사용 여부 판단표

| 상황 | 판단 | 이유 |
|---|---|---|
| 한 파일 안의 작은 수정 | 쓰지 않는 편이 낫다 | 나누는 시간보다 직접 처리하는 시간이 짧다 |
| 낯선 저장소 구조 파악 | 읽기 전용이면 권장 | 구조, 테스트, 의존성을 따로 읽어도 충돌이 거의 없다 |
| PR 다각도 리뷰 | 관점이 분명하면 권장 | 보안, 테스트, 유지보수성처럼 렌즈를 나눌 수 있다 |
| 같은 파일을 여러 에이전트가 수정 | 초보자는 피한다 | 충돌과 재검증 비용이 커질 가능성이 높다 |
| 외부 API, DB, 결제, 메일 발송이 걸린 테스트 | 먼저 멈추고 확인한다 | 병렬 실행보다 부작용 통제가 먼저다 |

---

### 3.4 중요: 서브에이전트는 명시적 지시만 가능하다

공식 문서가 명확히 못 박는다:

> "Codex doesn't spawn subagents automatically, and it should only use subagents when you explicitly ask for subagents or parallel agent work."

Codex는 서브에이전트를 마음대로 만들지 않는다. **사용자가 명시적으로 지시해야 한다.** 이 제한은 오히려 초보자에게 유익하다. 나도 모르는 사이에 여러 에이전트가 실행되면 비용, 속도, 작업 흐름을 파악하기 어려워지기 때문이다.

**지시 표현 예시 (공식 문서 기준):**

```
"spawn two agents"         → 에이전트 두 개를 띄워라
"delegate this work in parallel"  → 이 작업을 병렬로 위임해라
"use one agent per point"  → 항목마다 에이전트 하나씩 써라
```

---

### 3.5 실전 서브에이전트 프롬프트 3가지

**예시 1 — 읽기 전용 병렬 조사 (초보자에게 가장 안전)**

```
Use two parallel subagents for read-only exploration.
One subagent should map the changed files.
The other should find related tests.
Do not edit files. Return short summaries with file paths.
```

한국어 버전:
```
읽기 전용 조사에 서브에이전트 2개를 병렬로 써라.
에이전트 1: 변경된 파일 목록을 정리해라.
에이전트 2: 관련 테스트 파일을 찾아라.
파일 수정 금지. 파일 경로가 포함된 짧은 요약만 돌려줘.
```

**예시 2 — PR 다각도 리뷰 (3개 관점)**

```
Review this branch with parallel subagents.
Spawn one subagent for security risks, one for test gaps,
and one for maintainability.
Wait for all three, then summarize the findings by category
with file references.
```

한국어 버전:
```
이 브랜치를 병렬 서브에이전트로 리뷰해라.
보안 리스크를 볼 에이전트, 테스트 빈틈을 볼 에이전트, 유지보수성을 볼 에이전트를 각각 하나씩 띄워라.
세 결과가 모두 나오면 파일 경로와 함께 카테고리별로 요약하라.
```

**예시 3 — PR 6개 관점 분산 리뷰**

```
Review the current PR (this branch vs main) from these 6 perspectives.
Spawn one agent per point, wait for all of them, and summarize by point.
1. Security issues
2. Code quality
3. Bugs
4. Race conditions
5. Test flakiness
6. Code maintainability
```

**주의**: 6개 에이전트는 기초 실습에는 많다. 토큰 낭비로 이어질 수 있다. 평소에는 2~3개 관점부터 시작하는 편이 낫다.

---

### 3.6 `/agent` 명령으로 스레드 확인하기

서브에이전트가 실행 중일 때 `/agent` 슬래시 명령을 쓰면 각 에이전트의 작업창으로 이동할 수 있다. 공식 문서 정의:

> "/agent — Switch the active agent thread. Inspect or continue work in a spawned subagent thread."

**승인 요청이 뜨면 먼저 출처를 확인한다**: 서브에이전트가 여러 개일 때는 내가 보고 있지 않은 에이전트 스레드에서 명령 승인 요청이 올라올 수 있다. 바로 허용하지 말고 `/agent`로 해당 스레드를 열어 어떤 작업 중인지 확인한 뒤 승인한다.

---

### 3.7 워크플로 패턴 3가지

| 패턴 | 언제 쓰나 | 흔한 실패 |
|---|---|---|
| **병렬 독립** | 역할이 완전히 나뉘어 파일 충돌이 없을 때 | 결과 취합 기준이 없어 응답이 뒤섞임 |
| **순차 직렬** | 이전 결과가 다음 에이전트의 입력이 될 때 | Phase 완료 체크 없이 다음 단계 바로 실행 |
| **오케스트레이터** | 복잡한 작업을 총괄 에이전트가 조율할 때 | 서브에이전트가 같은 파일을 덮어씀 |

처음에는 **병렬 독립(읽기 전용 2개)** 패턴으로 시작하자. 결과가 잘 나오면 순차 직렬을 추가하고, 오케스트레이터는 역할이 완전히 정리된 뒤 도입하자.

**더 정밀한 프롬프트 구조 — XML 블록 패턴:**

```xml
<task>
  이 브랜치의 보안 리스크와 테스트 갭을 병렬로 분석한다.
</task>
<action_safety>
  파일 수정 금지. 읽기만 허용.
</action_safety>
<completeness_contract>
  두 서브에이전트 결과가 모두 반환되면 완료.
  파일 경로와 줄 번호를 포함해 요약한다.
</completeness_contract>
```

---

## **Part 4: AGENTS.md와 Rules — 팀 규칙 설정**
  
### 4.1 규칙을 파일로 관리해야 하는 이유
채팅에 적은 규칙은 세션이 끝나면 사라진다. 저장소 파일로 옮겨야 다음 세션과 다음 팀원에게 같은 기준이 전달된다.

**핵심 개념 비교:**

| 개념 | 한 줄 설명 | 비유 |
|---|---|---|
| `AGENTS.md` | 팀 규칙을 자연어로 적어 두는 프로젝트 지침 파일. Codex가 프로젝트를 열 때 자동으로 읽음 | 팀 위키/사규집 |
| `.rules` | 특정 셸 명령을 허용·확인·차단하는 규칙 파일 | 출입 보안 게이트 |
| `config.toml` | 모델·승인 정책·샌드박스 수준 등 기본 실행 환경 설정 파일 | 권한 카드 발급 설정 |

---

### 4.2 지금 바로 따라 할 수 있는 가장 작은 AGENTS.md
프로젝트 루트에 `AGENTS.md` 파일을 만들고 아래 내용을 저장한다.

```markdown
# AGENTS.md

## 빌드와 테스트
- 작업을 완료했다고 말하기 전에 `npm test`를 실행한다.

## 수정 금지
- `.env`, `.env.local`은 읽거나 수정하지 않는다. API 키가 들어 있을 수 있다.
```

**중요한 점**: 영어 표현이 아니라 명확한 완료 조건과 금지 경계가 핵심이다. AGENTS.md는 한국어로 써도 된다.

---

### 4.3 AGENTS.md 파일 위치 3단계

공식 문서 기준으로 `AGENTS.md`는 세 위치에 둘 수 있다.

| 위치 | 적용 범위 | 적기 좋은 내용 |
|---|---|---|
| `~/.codex/AGENTS.md` | 모든 저장소에 걸친 개인 선호 | 응답 길이, 리뷰 스타일, 기본 커뮤니케이션 방식 |
| `{repo}/AGENTS.md` | 저장소 전체의 팀 규칙 | 빌드/테스트 명령, 리뷰 기준, 프로젝트 구조 |
| `{subdir}/AGENTS.md` | 해당 디렉토리 작업 | 모듈별 금지 사항, 로컬 테스트 명령, 도메인 규칙 |

**로딩 순서:**

```
~/.codex/AGENTS.md          ← 1단계: 전역 (개인 선호)
         │
         ▼
{repo}/AGENTS.md            ← 2단계: 저장소 루트 (팀 규칙)
(같은 위치에 AGENTS.override.md 있으면 대체)
         │
         ▼
{subdir}/AGENTS.md          ← 3단계: 하위 디렉토리 (모듈별)

─────────────────────────────────────────
config.toml                 ← 모델·승인·샌드박스 런타임 설정
.rules                      ← 셸 명령 허용/차단 정책
```

---

### 4.4 AGENTS.md에 무엇을 적어야 효과적인가

**잘 작동하는 규칙 vs 무시되기 쉬운 규칙:**

❌ 이렇게 하면 무시된다:
```markdown
## 규칙
- 좋은 코드를 작성한다.
- 문제를 만들지 않는다.
- migrations/는 중요하다.
```

✅ 이렇게 하면 잘 따른다:
```markdown
## 빌드와 테스트
- 작업을 완료했다고 말하기 전에 `pytest`를 실행한다.

## 수정 금지
- `migrations/`는 커밋 전에 DBA 검토가 필요하다.
  직접 수정하면 데이터 손실 위험이 있다.
```

**이유가 없는 금지("문제 만들지 않는다")는 다음 세션에서 무시되기 쉽다. 완료 조건과 금지 이유를 함께 적어야 Codex가 예외 상황에서도 의도를 지킨다.**

**AGENTS.md는 길수록 좋지 않다**: 공식 가이드도 "중요한 지시사항만으로 시작하라"고 명시한다. 날카로운 규칙 10줄이 위키 dump 100줄보다 낫다.

---

### 4.5 AGENTS.md 스타터 템플릿 4가지

**예시 A — 개인: Node.js 프로젝트**

```markdown
# AGENTS.md

## 빌드와 테스트
- 작업을 완료했다고 말하기 전에 `npm test`를 실행한다.
- `npm install`은 자동으로 실행하지 않는다. 새 패키지가 필요하면
  패키지 이름과 이유를 먼저 설명하고 확인을 기다린다.

## 코드 스타일
- 새 비동기 코드는 `.then()` 체인보다 `async/await`로 작성한다.
- 커밋될 코드에 `console.log`를 남기지 않는다. `lib/log.ts`의 logger를 사용한다.

## 수정 금지
- `package-lock.json`은 직접 편집하지 않는다. `npm install`로만 갱신한다.
- `.env`, `.env.local`은 읽거나 수정하지 않는다.
```

**예시 B — 소팀: 프론트(Node) + 백엔드(Python)**

```markdown
# AGENTS.md

## 프로젝트 구조
- 프론트엔드: `apps/web/` (Next.js, pnpm)
- 백엔드: `apps/api/` (FastAPI, pytest)
- 공유 타입: `packages/types/`

## 빌드와 테스트
- 프론트엔드 변경: `pnpm --filter web lint && pnpm --filter web test`
- 백엔드 변경: `pytest apps/api/tests/ -q`
- 양쪽에 걸친 변경을 건드렸다면 프론트엔드와 백엔드 검증을 모두 실행한다.

## 코드 스타일
- 프론트엔드: 기존 디자인 토큰을 사용한다. 컴포넌트 안에 일회성 hex 색상값을 넣지 않는다.
- 백엔드: 모든 API 요청/응답 스키마는 Pydantic 모델로 정의한다.
- 공통: TODO 주석을 남길 때는 연결된 이슈 번호를 함께 적는다.

## 수정 금지
- `apps/api/migrations/`는 DBA 검토가 필요하므로 자동 수정하지 않는다.
- `packages/types/`는 양쪽 팀 계약에 해당하므로 변경 전에 영향 범위를 설명한다.
```

**예시 C — Python 프로젝트**

```markdown
# AGENTS.md

## 빌드와 테스트
- 작업을 완료했다고 말하기 전에 `pytest`를 실행한다.
- 데이터베이스 마이그레이션은 자동 실행하지 않는다. 먼저 마이그레이션 파일을
  보여주고 검토 확인을 기다린다.

## 코드 스타일
- 모든 함수 시그니처에 타입 힌트를 작성한다.
- `except:`만 단독으로 쓰지 않는다. 처리할 예외 타입을 명확히 적는다.

## 수정 금지
- `migrations/` 파일은 커밋 전에 수동 검토가 필요하므로 자동 수정하지 않는다.
- `.env`, `.env.local`은 읽거나 수정하지 않는다.
```

---

### 4.6 AGENTS.override.md — 임시 예외 처리

같은 위치에 `AGENTS.override.md`가 있으면 그 디렉토리의 `AGENTS.md`는 무시되고 override 파일만 지침으로 포함된다. 임시로 강한 예외가 필요할 때 사용한다.

**사용 예시 (계약직 개발자에게 특정 폴더만 접근 허용):**

```markdown
# tests/AGENTS.override.md
# 원본 AGENTS.md: "tests/ 수정 금지"
# 이 파일이 있으면 tests/ 안에서는 아래 규칙만 적용된다

## 덮어쓰기 규칙 (feature/contractor-scope 브랜치용)
- tests/integration/ 하위 파일은 수정 가능하다.
- tests/unit/ 하위 파일은 원래 규칙대로 수정 금지.
- 작업 완료 후 이 파일을 삭제하고 PR을 올린다.
```

---

### 4.7 언제 AGENTS.md를 업데이트해야 하는가

**AGENTS.md로 옮길지 판단하는 체크리스트:**
- 같은 피드백을 세 번 이상 반복해서 말하고 있는가?
- 새 세션에서도 반드시 지켜야 하는 팀 규칙인가?
- 규칙을 지키지 않으면 테스트 실패, 보안 위험, 배포 사고로 이어질 수 있는가?
- 반대로 오래된 도구명, 사라진 폴더, 더 이상 쓰지 않는 명령은 없는가?

**config.toml에서 파일명 커스텀 및 용량 한도 조정:**

```toml
# ~/.codex/config.toml
project_doc_fallback_fallback_filenames = ["TEAM_GUIDE.md", ".agents.md"]
project_doc_max_bytes = 65536
```

---

## **Part 5: MCP·Plugin·Skill — 외부 도구 연결**
  
### 5.1 왜 MCP가 필요한가

Codex의 기본 능력은 현재 프로젝트를 읽고, 코드를 수정하고, 셸 명령을 실행하는 데 있다. 하지만 실제 개발 업무는 저장소 안에서만 끝나지 않는다. GitHub 이슈 트래커, 문서 사이트, 디자인 파일, 브라우저, 로그 시스템까지 함께 봐야 할 때가 많다.

**MCP(Model Context Protocol)**: Codex가 외부 도구(GitHub, DB, 브라우저)에 표준 방식으로 요청·응답을 주고받는 통신 규약. USB-C 포트에 비유할 수 있다.

**MCP가 진짜 필요한 3가지:**

1. 저장소 밖 데이터를 Codex가 직접 읽어야 할 때 (문서, 이슈 트래커)
2. Codex 작업 결과를 외부 시스템에 써야 할 때 (GitHub PR 생성)
3. 브라우저·로그 같은 런타임 상태를 확인해야 할 때

이 3가지 중 하나가 아니면 MCP 없이 프롬프트로 충분하다.

**MCP, Plugin, Skill 비교:**

| 개념 | 한 줄 설명 | 언제 쓰나 |
|---|---|---|
| MCP 서버 | Codex가 외부 도구·데이터에 접근하는 연결 통로 | GitHub 이슈, 라이브러리 문서, 브라우저 등 저장소 밖 자원이 필요할 때 |
| Plugin | Skills·앱 연동·MCP 서버를 묶어 설치 가능한 형태로 배포하는 단위 | 팀 전원에게 동일한 확장 묶음을 배포해야 할 때 |
| Skill | 반복 작업 절차를 SKILL.md 파일로 정의해 Codex가 재사용하는 지침 | 릴리스 노트 작성, 보안 점검 등 팀 내 반복 작업을 표준화할 때 |

---

### 5.2 MCP 서버 등록 — STDIO와 HTTP

**방법 1 — STDIO (로컬 프로세스)**

```bash
codex mcp add <이름> -- <실행_명령>
```

**context7 등록 예시 (공식 문서 예시):**

```bash
codex mcp add context7 -- npx -y @upstash/context7-mcp
```

**주의**: `npx -y`는 매번 최신 버전을 내려받는다. 팀 환경에서는 버전 고정(`@버전`)을 추가하지 않으면 동일 서버 버전을 보장하기 어렵다.

**방법 2 — HTTP (외부 SaaS 서비스)**

TOML을 직접 편집한다:

```toml
# ~/.codex/config.toml
[mcp_servers.figma]
url = "https://mcp.figma.com/mcp"
bearer_token_env_var = "FIGMA_OAUTH_TOKEN"
```

또는 CLI로:

```bash
codex mcp add figma --url https://mcp.figma.com/mcp
```

---

### 5.3 CLI ↔ TOML 매핑 빠른 참조

| 상황 | CLI 명령 | TOML 설정 (동일 결과) |
|---|---|---|
| STDIO (로컬, 인증 없음) | `codex mcp add context7 -- npx -y @upstash/context7-mcp` | `[mcp_servers.context7]` / `command = "npx"` / `args = ["-y", "@upstash/context7-mcp"]` |
| HTTP + 토큰 (외부 SaaS) | CLI보다 TOML 직접 설정 권장 | `[mcp_servers.figma]` / `url = "https://mcp.figma.com/mcp"` / `bearer_token_env_var = "FIGMA_OAUTH_TOKEN"` |
| 팀 공유 | `.codex/config.toml`을 저장소에 커밋 | `startup_timeout_sec = 15` 추가 권장 |

**중요 보안 원칙**: 토큰·비밀 값은 절대 TOML에 직접 쓰지 않는다. `bearer_token_env_var`·`env_vars`로 환경변수 이름만 기록한다.

---

### 5.4 권장 MCP 서버 후보 3종

| 서버 | 용도 | 공식 문서 |
|---|---|---|
| **GitHub** | 저장소 관리, 이슈·PR 관리 | [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) |
| **context7** | 라이브러리 공식 문서 실시간 참조 | [Codex MCP 공식 문서](https://developers.openai.com/codex/mcp) |
| **Playwright** | 브라우저 자동화·UI 검증 | [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) |

context7은 읽기 전용이라 가장 안전하게 시작할 수 있다. GitHub·Playwright는 토큰 및 실행 환경 확인 후 추가한다.

---

### 5.5 도구 권한과 타임아웃 제어

```toml
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]
startup_timeout_sec = 10   # 서버가 켜질 때까지 기다리는 시간 (기본값 10초)
tool_timeout_sec = 60      # MCP 도구 하나가 응답할 때까지 기다리는 시간 (기본값 60초)
enabled_tools = ["<tool-name>"]  # 허용 목록 (비워두면 전체 허용)
```

**MCP 운영 3원칙:**
1. **쓰기 가능 도구는 읽기 가능 도구와 반드시 분리한다**
2. **MCP tool result를 사용자 입력과 같은 수준으로 다룬다** — 외부 서버가 반환하는 텍스트는 신뢰 경계 밖에서 온다. 그 안에 지시문이 있으면 Codex가 따를 수 있다 (prompt injection 위험)
3. **장애 시 빠르게 좁힐 수 있는 구조를 미리 만든다** — `enabled = false` 또는 `disabled_tools`로 즉시 차단 가능한 상태 유지

**MCP 서버 켜기 전 체크리스트:**
- 서버 출처와 설치 명령이 공식 문서나 신뢰 가능한 저장소에서 확인되는가?
- 서버가 노출하는 도구 목록에서 읽기 전용과 쓰기 가능 도구를 구분했는가?
- 토큰 값 자체가 아니라 환경변수 이름만 config에 남기는가?

---

## **Part 6: 슬래시 명령어 완전 정리**     
Codex CLI의 TUI 안에서 `/`를 입력하면 명령 popup이 열린다. 총 30개 이상의 슬래시 명령이 있으며, 이를 8개 카테고리로 분류한다.

### 6.1 사용법 기본기  
TUI의 composer(메시지 입력란)에서 `/`를 입력하면 명령 자동완성 팝업이 열린다. 키워드 일부를 더 치면 목록이 좁혀지고, 화살표로 골라 **Enter로 즉시 실행**하거나 **Tab으로 큐잉(예약)**할 수 있다.

**용어 정리:**
- **popup**: `/` 입력 시 composer 위에 뜨는 명령 자동완성 창
- **큐잉(queueing)**: 현재 턴이 끝나기 전에 다음 명령을 미리 등록해 두는 동작
- **feature 플래그(게이트)**: 일부 명령은 `[features]` 테이블의 특정 키가 켜져 있어야만 동작

---

### 6.2 세션 관리 명령 (8개)

| 명령 | 기능 | 활용 팁 |
|---|---|---|
| **`/new`** | 새 대화 스레드 시작. 이전 컨텍스트를 완전히 끊고 새로 시작 | 주제 전환 시 이전 맥락 끊고 새로 시작 |
| **`/resume`** | picker가 열려 최근 세션 목록 표시. 이전 세션 이어 받기 | 중단된 작업 이어서 컨텍스트 복구. CLI에서 `codex resume --last`도 가능 |
| **`/fork`** | 현재 스레드를 새 분기로 복제. 본 흐름을 망가뜨리지 않고 "다르게 가면?"을 시험 | 기존 맥락을 보존한 채 분기 실험 |
| **`/side`** | 메인 스레드를 그대로 둔 채 보조 대화창 열기. ESC로 닫으면 메인으로 복귀 | 메인 흐름 건드리지 않고 버그 원인 가설 검증. 컨텍스트 오염 방지 |
| **`/clear`** | 같은 스레드 유지하면서 화면과 컨텍스트 초기화 | 컨텍스트 누적으로 판단이 흔들릴 때. 초기화 전 TODO 3줄 요약 습관 권장 |
| **`/compact`** | 긴 대화를 요약해서 압축. 핵심만 남기고 토큰 줄이기 | 대화가 너무 길어져 응답이 느려질 때 |
| **`/exit` / `/quit`** | TUI 정상 종료 (둘은 완전히 동일한 alias) | 종료 전 `/diff`로 미커밋 여부 확인 후 실행 권장 |
| **`/logout`** | 저장된 인증 자격 모두 제거 | 계정 전환이나 공용 노트북에서 흔적 지울 때 |

---

### 6.3 모델·승인·권한 명령 (5개)

| 명령 | 기능 | 활용 팁 |
|---|---|---|
| **`/model`** | AI 모델을 picker로 선택. 같은 화면에서 추론 강도(reasoning effort)도 조정 가능 | 가벼운 질문은 작은 모델, 깊은 분석은 큰 모델로 그때그때 전환 |
| **`/fast`** | 빠른 응답 모드 토글. 속도 우선 | 아이디어 발산·로그 훑기 초반 탐색에 좋음. 최종 수정 전에는 해제 권장 |
| **`/permissions`** | 현재 승인 정책과 샌드박스 수준을 한 화면에서 확인하고 변경 | 위험한 작업 전에 가장 먼저 확인해야 할 명령 |
| **`/sandbox-add-read-dir`** | 워크스페이스 바깥의 디렉터리를 한시적으로 읽기 허용 목록에 추가 (Windows 전용) | macOS/Linux는 `--add-dir` CLI 플래그 사용 |
| **`/experimental`** | 메모리·목표·터미널 리플로우 같은 실험 기능을 그 자리에서 토글 | 개인 브랜치에서 A/B 테스트 후 채택 판단. 팀 표준 브랜치에서는 비권장 |

---

### 6.4 도구·맥락 명령 (5개)

| 명령 | 기능 | 활용 팁 |
|---|---|---|
| **`/mention`** | 파일·앱·플러그인 등 특정 컨텍스트 항목을 메시지에 정확히 삽입 | `@` 자동완성보다 popup 기반이라 초보자에게 친화적 |
| **`/mcp`** | 연결된 MCP 서버 목록과 각 서버 도구 정보 확인 | 도구가 작동 안 할 때 가장 먼저 확인. 인증 만료·권한 부족 진단 |
| **`/plugins`** | 설치된 플러그인과 마켓플레이스 탐색. v0.128.0에서 정식 추가 | 플러그인 가용성 먼저 판단 |
| **`/apps`** | 외부 앱·커넥터를 프롬프트에 삽입하는 전체 화면 앱 선택기 열기 | 외부 서비스 연동 작업 흐름 점검 |
| **`/agent`** | 이미 생성된 메인 스레드와 서브에이전트 스레드 사이를 전환 | 병렬 조사·구현 중 어느 에이전트가 작업 중인지 확인 |

---

### 6.5 코드 작업 명령 (4개)

| 명령 | 기능 | 활용 팁 |
|---|---|---|
| **`/init`** | 저장소 루트에 `AGENTS.md`를 만들거나 보강. Codex가 저장소 구조를 읽고 초안 생성 | 처음 AGENTS.md를 만들 때 막막하면 이 명령으로 시작 |
| **`/diff`** | 현재 워킹 트리의 변경 내역을 시각화해서 보여줌 | Codex가 방금 만든 변경이 의도와 일치하는지 검증. 가장 자주 쓰는 명령 |
| **`/review`** | 현재 변경 또는 지정한 베이스 브랜치/커밋 대상으로 코드 리뷰 시작. `codex review` 서브커맨드로도 진입 가능 | 머지 직전 회귀·품질 리스크 집중 점검. 보안·성능 등 관점을 명시하면 효과 상승 |
| **`/plan`** | 바로 코드를 고치는 대신 단계별 실행 계획을 먼저 작성. `Shift+Tab`으로도 접근 가능 | 구현 전 단계·리스크를 구조화. 복잡한 변경에서 충돌·범위 미리 식별 |

---

### 6.6 진단·디버그 명령 (4개)

| 명령 | 기능 | 활용 팁 |
|---|---|---|
| **`/status`** | 모델, 승인 정책, 샌드박스, 연결된 MCP 수, 토큰 사용량을 한 화면에 정리 | 다른 사람과 환경 비교하거나 이슈 리포트 쓸 때 첫 줄에 붙이기 좋음 |
| **`/debug-config`** | 전역·프로젝트·CLI 플래그가 어떻게 병합되어 현재 설정이 만들어졌는지 확인 | "내 config.toml이 제대로 읽히고 있나?" 의심될 때 가장 먼저 사용 |
| **`/feedback`** | 현재 세션 로그를 첨부해 Codex 팀에 피드백 전송 | 재현 단계(명령→기대결과→실제결과)를 포함해야 처리 속도 빠름 |
| **`/copy`** | 마지막 응답을 클립보드로 복사. v0.129.0에서 tmux passthrough 없이도 동작하도록 수정 | PR 템플릿·위키·이슈 본문으로 옮길 때 유용 |

---

### 6.7 UI·UX·테마 명령 (4개)

| 명령 | 기능 | 활용 팁 |
|---|---|---|
| **`/theme`** | 코드 블록의 구문 강조 테마를 picker로 선택 | OS 다크모드 전환 시 함께 변경 권장 |
| **`/title`** | 터미널 창 또는 탭 제목 설정. v0.128.0에서 활성 턴 중 편집도 가능 | "레포-브랜치-목표" 패턴으로 통일하면 검색·회고에 유리 |
| **`/statusline`** | TUI 하단 상태 표시줄에 표시할 항목 구성 (v0.128.0~) | 승인 정책·샌드박스·모델명을 우선 노출하도록 설정하면 실수 예방 |
| **`/keymap`** | TUI 단축키를 대화형으로 재매핑 (v0.128.0~) | IDE 공통 단축키와 충돌 최소화 기준으로 설정 권장 |

---

### 6.8 게이트·조건부 명령 (4개)

이 명령들은 특정 feature 플래그가 켜져 있어야만 popup에 표시된다.

| 명령 | 게이트 조건 | 기능 |
|---|---|---|
| **`/goal`** | `features.goals = true` | 긴 작업의 목표를 명시적으로 박아 두면 Codex가 응답마다 목표 정합성을 따짐 |
| **`/personality`** | 모델 의존 | 응답 톤을 friendly·pragmatic·none 중에서 선택 |
| **`/ps`** | 실험적 | 실행 중인 서브에이전트·프로세스 상태 보기 |
| **`/stop`** | 항상 표시 | 현재 실행 중인 에이전트 작업 즉시 중단 |

---

## **부록 A: config.toml 핵심 설정 레퍼런스**

`~/.codex/config.toml`에 저장되는 주요 설정 키들이다.

```toml
# 모델과 추론 강도
model = "codex-mini-latest"           # 사용할 모델
reasoning_effort = "medium"           # low / medium / high

# 승인 정책과 샌드박스
approval_policy = "on-request"        # untrusted / on-request / never
sandbox_mode = "workspace-write"      # read-only / workspace-write / danger-full-access

# AGENTS.md 관련
project_doc_fallback_filenames = ["TEAM_GUIDE.md", ".agents.md"]
project_doc_max_bytes = 32768         # 기본 32KiB

# UI 설정
[tui]
theme = "dark"
personality = "pragmatic"             # friendly / pragmatic / none

# 실험적 기능 활성화
[features]
goals = false                         # /goal 명령 활성화 여부

# MCP 서버 설정 (STDIO 예시)
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]
startup_timeout_sec = 10
tool_timeout_sec = 60

# MCP 서버 설정 (HTTP 예시)
[mcp_servers.figma]
url = "https://mcp.figma.com/mcp"
bearer_token_env_var = "FIGMA_OAUTH_TOKEN"
```

---

## **부록 B: CLI 플래그 및 `codex exec` 사전**

**자주 쓰는 CLI 플래그:**

```bash
# 샌드박스 지정
codex --sandbox read-only "분석 요청"
codex --sandbox workspace-write "코드 수정 요청"

# 승인 정책 지정
codex --ask-for-approval on-request "요청"
codex --ask-for-approval never "자동화 요청"

# 추가 읽기 허용 경로 (macOS/Linux)
codex --add-dir /path/to/docs "해당 문서를 참고해서..."

# 비대화형 실행 (CI/CD에 유용)
codex exec --sandbox read-only "src/ 폴더에서 타입 오류 알려줘" > report.md

# 마지막 세션 이어서
codex resume --last

# 특정 세션 이어서
codex resume <SESSION_ID>
```

**`codex exec` 패턴 — 3단계 자동화:**

```bash
# Level 1: 로컬에서 결과 파일로 저장
codex exec --sandbox read-only "이 저장소 구조 요약해줘" > reports/structure.md

# Level 2: package.json에 npm 스크립트로 등록 (팀 공유)
# "codex:check": "codex exec --sandbox read-only 'src/ 타입 오류·버그 알려줘' > reports/check.md"

# Level 3: GitHub Actions에서 PR마다 자동 리뷰
# .github/workflows/codex-review.yml 참고
```

---

## **부록 C: 핵심 원칙 요약**

**안전 기본값 5가지 (항상 기억할 것):**
1. 처음에는 반드시 `--sandbox read-only`로 시작한다
2. API 키는 절대 하드코딩하지 않는다. 환경변수로만 전달한다
3. PR 본문이나 커밋 메시지를 raw로 프롬프트에 넣지 않는다
4. 결과는 항상 파일이나 PR 코멘트로 저장해 사람이 검토한다
5. 자동 커밋·푸시 단계는 수동 검토가 안정화된 뒤에만 추가한다

**AGENTS.md 3대 원칙:**
1. 날카로운 규칙 10줄이 위키 dump 100줄보다 낫다
2. "하지 마" 한 줄보다 "하지 마 — 이유" 형식이 훨씬 잘 지켜진다
3. 완료 조건을 명시하지 않으면 Codex는 완료라고 말하고 끝낸다

**서브에이전트 3대 원칙:**
1. "읽기 전용 에이전트 2개"에서 시작한다
2. 프롬프트가 명확한 에이전트 하나 > 역할이 흐릿한 에이전트 셋
3. 에이전트 수 ≠ 속도. 분담이 모호하면 세 방향으로 흩어질 뿐이다
