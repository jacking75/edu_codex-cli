# Codex CLI Subagent: `docs_researcher`, `reviewer`, `worker` 완벽 가이드

## **Subagent란 무엇인가:**
Codex CLI의 Subagent는 메인 에이전트가 복잡한 작업을 처리할 때 **병렬로 생성하는 특수화된 하위 에이전트**입니다. 각 서브에이전트는 자신만의 독립적인 컨텍스트 윈도우, 모델 설정, 파일시스템 권한을 가지고 작동합니다. 이 구조의 핵심 목적은 **Context Pollution(컨텍스트 오염)** 과 **Context Rot(컨텍스트 부식)** 을 방지하는 것입니다. 탐색 로그, 테스트 결과, 스택 트레이스 같은 노이즈가 많은 중간 출력이 메인 대화를 오염시키는 문제를 해결하기 위해, 해당 작업들을 별도의 서브에이전트 스레드로 격리시킵니다.

Codex가 제공하는 내장(built-in) 에이전트는 `default`, `worker`, `explorer` 세 가지이며, `docs_researcher`와 `reviewer`는 OpenAI 공식 문서에서 **Custom Agent(커스텀 에이전트)의 대표적인 예시**로 제시되는 에이전트들입니다. 이들은 `.codex/agents/` 디렉터리 안에 TOML 파일 형태로 정의합니다.

---

## **Subagent 아키텍처 및 TOML 구조:**
모든 커스텀 에이전트는 `~/.codex/agents/` (글로벌) 또는 `.codex/agents/` (프로젝트 스코프) 경로에 `.toml` 파일로 정의됩니다. 프로젝트 스코프의 에이전트가 글로벌보다 높은 우선순위를 가집니다.

TOML 파일의 핵심 필드 구성은 다음과 같습니다:

| 필드 | 필수 여부 | 설명 |
|---|---|---|
| `name` | ✅ 필수 | Codex가 에이전트를 spawn하거나 참조할 때 사용하는 식별자 |
| `description` | ✅ 필수 | Codex가 이 에이전트를 언제 사용할지 결정하는 설명 |
| `developer_instructions` | ✅ 필수 | 에이전트의 행동 방식을 정의하는 핵심 지시문 |
| `model` | 선택 | 사용할 모델 (미설정 시 부모 세션에서 상속) |
| `model_reasoning_effort` | 선택 | `high`, `medium`, `low` 중 선택 |
| `sandbox_mode` | 선택 | `read-only` 또는 `workspace-write` |
| `nickname_candidates` | 선택 | UI 표시용 친숙한 이름 후보 목록 |
| `mcp_servers` | 선택 | 연결할 MCP 서버 설정 |

---

## **1. `worker` — 실행(Execution) 전담 내장 에이전트:**
`worker`는 Codex CLI에 **내장(built-in)된 세 가지 에이전트 중 하나**로, 별도의 TOML 파일 없이 바로 사용 가능합니다. 이 에이전트의 핵심 역할은 **코드를 실제로 수정하고 구현하는 것**입니다.

**역할과 특성:**

`worker`는 읽기와 쓰기 모두 가능한 **read-write 실행 에이전트**입니다. 탐색이나 분석보다는 실제 코드 변경, 버그 수정, 기능 구현에 최적화되어 있습니다. 일반적으로 `sandbox_mode = "workspace-write"` 권한을 가지므로 파일을 생성하고 수정할 수 있습니다. `pr_explorer`나 `reviewer`처럼 증거를 수집하거나 분석하는 역할과 달리, `worker`는 결론을 받아 직접 파일에 손을 대는 에이전트입니다.

**`spawn_agents_on_csv`와의 연계:**

`worker` 타입은 특히 CSV 기반 배치 처리(`spawn_agents_on_csv`)에서 강력하게 활용됩니다. Codex가 CSV를 읽고 각 행(row)마다 하나의 worker 서브에이전트를 spawn하여 병렬로 작업을 처리한 뒤, 완료되면 `report_agent_job_result`를 호출해 결과를 취합합니다. 예를 들어 수십 개의 프론트엔드 컴포넌트를 일괄 검토하거나, PR 목록을 일괄 분석하는 작업에 사용됩니다.

**커스텀 `worker`의 예시 (공식 문서 응용):**

```toml
name = "ui_fixer"
description = "Implementation-focused agent for small, targeted fixes after the issue is understood."
model = "gpt-5.3-codex-spark"
model_reasoning_effort = "medium"
sandbox_mode = "workspace-write"
developer_instructions = """
Own the fix once the issue is reproduced.
Make the smallest defensible change, keep unrelated files untouched,
and validate only the behavior you changed.
"""
```

이처럼 실제 구현 에이전트는 `sandbox_mode = "workspace-write"` 를 설정하고, 변경 범위를 최소화하도록 지시하는 것이 Best Practice입니다.

---

## **2. `reviewer` — 코드 정확성·보안 심사 커스텀 에이전트:**
`reviewer`는 OpenAI 공식 문서에서 **PR 리뷰 워크플로의 핵심 에이전트**로 소개되는 커스텀 에이전트입니다. 내장 에이전트가 아니므로 직접 TOML 파일로 정의해야 합니다.

**역할과 특성:**

`reviewer`의 임무는 코드를 **소유자(owner)의 시각**으로 검토하는 것입니다. 단순히 스타일이나 포맷을 지적하는 것이 아니라, 정확성(correctness), 보안(security), 행동 회귀(behavior regression), 테스트 누락(missing test coverage)처럼 실제 리스크에 집중합니다. 파일을 수정하지 않으므로 반드시 `sandbox_mode = "read-only"`로 설정하며, 복잡한 로직을 추적하고 엣지 케이스를 파악해야 하기 때문에 `model_reasoning_effort = "high"`와 강력한 추론 모델(`gpt-5.4` 계열)을 사용하는 것이 권장됩니다.

**공식 문서 예시 TOML:**

```toml
name = "reviewer"
description = "PR reviewer focused on correctness, security, and missing tests."
model = "gpt-5.4"
model_reasoning_effort = "high"
sandbox_mode = "read-only"
developer_instructions = """
Review code like an owner.
Prioritize correctness, security, behavior regressions, and missing test coverage.
Lead with concrete findings, include reproduction steps when possible,
and avoid style-only comments unless they hide a real bug.
"""
nickname_candidates = ["Atlas", "Delta", "Echo"]
```

**`nickname_candidates` 활용:**

`nickname_candidates = ["Atlas", "Delta", "Echo"]`처럼 설정하면, 여러 `reviewer` 인스턴스가 동시에 spawn될 때 UI에서 에이전트들을 "Atlas", "Delta", "Echo" 같은 이름으로 구분해 보여줍니다. 내부 식별자는 여전히 `reviewer`이지만, 개발자가 어떤 에이전트 스레드가 어떤 작업을 하고 있는지 시각적으로 명확하게 파악할 수 있습니다.

**실제 활용 프롬프트:**

```
Review this branch against main. Have pr_explorer map the affected
code paths, reviewer find real risks, and docs_researcher verify
the framework APIs that the patch relies on.
```

---

## **3. `docs_researcher` — 문서·API 검증 커스텀 에이전트:**
`docs_researcher`는 세 에이전트 중 가장 독특한 특성을 가집니다. 단순히 코드베이스를 읽는 것이 아니라, **MCP(Model Context Protocol) 서버를 통해 외부 공식 문서에 직접 접근**하여 프레임워크 API와 버전별 동작을 검증합니다.

**역할과 특성:**

`docs_researcher`의 임무는 **코드가 실제로 사용하고 있는 프레임워크나 API가 공식 문서와 일치하는지 확인**하는 것입니다. 예를 들어 패치(patch)가 특정 라이브러리의 API를 사용하는 경우, 해당 API가 현재 버전에서 올바른 방식으로 호출되고 있는지, deprecated된 인터페이스를 쓰고 있지는 않은지를 공식 문서에서 직접 찾아 확인합니다. 파일 수정이 전혀 없으므로 `sandbox_mode = "read-only"`이고, 외부 검색과 조회가 주요 작업이므로 비교적 가벼운 모델(`gpt-5.4-mini` 또는 `gpt-5.3-codex-spark`)을 사용합니다.

**공식 문서 예시 TOML:**

```toml
name = "docs_researcher"
description = "Documentation specialist that uses the docs MCP server to verify APIs and framework behavior."
model = "gpt-5.4-mini"
model_reasoning_effort = "medium"
sandbox_mode = "read-only"
developer_instructions = """
Use the docs MCP server to confirm APIs, options, and version-specific behavior.
Return concise answers with links or exact references when available.
Do not make code changes.
"""

[mcp_servers.openaiDeveloperDocs]
url = "https://developers.openai.com/mcp"
```

**MCP 서버 연동이 핵심:**

`[mcp_servers.openaiDeveloperDocs]` 섹션이 이 에이전트를 다른 에이전트와 차별화하는 핵심 요소입니다. MCP 서버 URL을 지정하면, `docs_researcher`는 해당 서버가 제공하는 도구(tool)를 통해 실시간으로 최신 공식 문서를 조회할 수 있습니다. 위 예시에서는 OpenAI Developer Docs에 연결되어 있지만, 원하는 어떤 MCP 서버로든 교체 가능합니다(예: Anthropic 문서, AWS 문서, MDN Web Docs 등).

**VoltAgent awesome-codex-subagents의 `docs-researcher.toml` 패턴:**

오픈소스 커뮤니티 리포지터리에서는 `docs_researcher`를 다음과 같은 방식으로 더 구체화합니다. 정확한 동작/질문을 식별하고, 공식 문서에서 근거를 찾은 뒤, 문서화된 사실과 추론을 명확히 구분하여 인용 기반의 간결한 답변을 반환하도록 지시합니다.

---

## **세 에이전트의 비교 요약:**

| 구분 | `worker` | `reviewer` | `docs_researcher` |
|---|---|---|---|
| **타입** | 내장(Built-in) | 커스텀(Custom) | 커스텀(Custom) |
| **핵심 역할** | 코드 구현·수정 | 코드 정확성·보안 심사 | API·문서 검증 |
| **Sandbox 모드** | `workspace-write` | `read-only` | `read-only` |
| **추론 강도** | `medium` | `high` | `medium` |
| **권장 모델** | `gpt-5.3-codex-spark` | `gpt-5.4` | `gpt-5.4-mini` |
| **파일 수정** | ✅ 가능 | ❌ 불가 | ❌ 불가 |
| **MCP 사용** | 선택적 | 선택적 | ✅ 핵심 기능 |

---

## **세 에이전트의 협업 워크플로:**
공식 문서에서 제시하는 **PR Review 패턴**은 이 세 에이전트가 어떻게 시너지를 내는지 가장 잘 보여줍니다.

```
Review this branch against main. Have pr_explorer map the affected code
paths, reviewer find real risks, and docs_researcher verify the framework
APIs that the patch relies on.
```

이 프롬프트 하나로 Codex는 다음 흐름을 실행합니다.

먼저 `pr_explorer`(또는 내장 `explorer`)가 변경된 코드 경로를 탐색하고 영향 받는 심볼과 파일을 정리합니다. 동시에 `reviewer`는 `read-only + high reasoning` 모드로 변경 코드를 오너 시각에서 심층 분석하여 보안 취약점, 회귀 버그, 테스트 누락을 찾아냅니다. 또한 동시에 `docs_researcher`는 MCP 서버를 통해 해당 패치가 의존하는 프레임워크 API가 공식 문서와 일치하는지 검증하고, 버전별 동작 차이나 deprecated 여부를 확인합니다. 마지막으로 세 에이전트의 결과가 모두 돌아오면, 메인 에이전트가 이를 하나의 종합적인 리뷰 리포트로 취합합니다.

이 구조 덕분에 메인 컨텍스트는 깨끗하게 유지되면서, 각 전문 에이전트가 자신의 영역에서 깊이 있는 분석을 병렬로 수행할 수 있습니다.

---

## **설정 및 사용 방법:**

**프로젝트 설정 (`.codex/config.toml`):**

```toml
[agents]
max_threads = 6      # 동시에 열릴 수 있는 에이전트 스레드 최대 수 (기본값: 6)
max_depth = 1        # 중첩 spawn 허용 깊이 (기본값: 1)
```

**에이전트 파일 배치:**

```bash
# 글로벌 설정
mkdir -p ~/.codex/agents
cp reviewer.toml ~/.codex/agents/

# 프로젝트 스코프 설정 (우선순위 높음)
mkdir -p .codex/agents
cp docs-researcher.toml .codex/agents/
cp reviewer.toml .codex/agents/
```

**주의사항:** Codex는 서브에이전트를 **자동으로 spawn하지 않습니다.** 반드시 프롬프트에서 명시적으로 "spawn one agent per point", "use reviewer subagent", "delegate this work in parallel" 같은 지시를 해야만 해당 에이전트가 생성됩니다. 또한 각 서브에이전트는 자체 모델 호출을 수행하므로, 서브에이전트 워크플로는 단일 에이전트 실행보다 더 많은 토큰을 소비한다는 점을 고려해야 합니다.
