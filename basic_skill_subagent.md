# 스킬과 서브에이전트

## **핵심 구분**
| 개념 | 역할 | 어디에 둠 | 언제 씀 |
|---|---|---|---|
| `AGENTS.md` | 프로젝트 전체 지침 | repo 루트/상위 디렉터리 | “이 코드베이스에서는 이렇게 일해라” |
| Skill | 반복 업무 절차, 참고문서, 스크립트, 템플릿 | 보통 `~/.codex/skills/<skill-name>/` | “이런 종류의 작업이면 이 절차를 써라” |
| Subagent | 병렬/전문화된 작업자 | Codex가 런타임에 spawn | “리뷰/탐색/구현을 나눠 맡겨라” |
| Custom agent | subagent의 역할 프리셋 | `~/.codex/agents/*.toml` 또는 `.codex/agents/*.toml` | “리뷰어/문서조사원/탐색원처럼 고정 역할로 spawn” |
| Plugin | skills, MCP, app 등을 묶은 배포 단위 | `~/.codex/plugins/...` | 여러 기능을 한 번에 설치/공유 |

OpenAI 공식 문서 기준으로도 skill은 `SKILL.md` manifest와 파일 묶음이며, 모델은 skill의 `name`, `description`, `path`를 보고 필요할 때 `SKILL.md`를 읽습니다. 명시적으로 쓰게 하려면 “Use the `<skill name>` skill”처럼 지시할 수 있습니다. [Skills docs](https://developers.openai.com/api/docs/guides/tools-skills)

  
## **Skill 만들기**
가장 작은 skill 구조는 이렇습니다.

```text
~/.codex/skills/csv-insights/
  SKILL.md
  scripts/
  references/
  assets/
  agents/openai.yaml
```

필수는 `SKILL.md`뿐입니다.

```markdown
---
name: csv-insights
description: Summarize CSV files and produce a concise markdown report. Use when the user asks to inspect, clean, compare, or summarize CSV exports.
---

# CSV Insights

When handling CSV files:
1. Inspect headers and row count first.
2. Preserve the original file.
3. Use a structured parser, not ad hoc string splitting.
4. Return a short report with anomalies, totals, and recommended next actions.

If calculations are needed, use `scripts/analyze_csv.py`.
```

좋은 `description`이 제일 중요합니다. Codex는 skill body를 항상 읽는 게 아니라, 먼저 frontmatter의 `name`과 `description`만 보고 “이 skill을 써야겠다”를 판단합니다. 그래서 “무엇을 하는지”뿐 아니라 “언제 발동해야 하는지”를 description에 넣어야 합니다.

현재 환경에는 `skill-creator`가 설치되어 있어서 템플릿 생성 스크립트도 쓸 수 있습니다.

```powershell
python C:\Users\jacki\.codex\skills\.system\skill-creator\scripts\init_skill.py csv-insights --path C:\Users\jacki\.codex\skills --resources scripts,references
```

만든 뒤에는 검증합니다.

```powershell
python C:\Users\jacki\.codex\skills\.system\skill-creator\scripts\quick_validate.py C:\Users\jacki\.codex\skills\csv-insights
```

`agents/openai.yaml`은 이름 때문에 헷갈리지만, 여기서의 `agents/`는 “subagent 정의”가 아니라 **skill의 UI/제품 메타데이터**에 가깝습니다. 예를 들면:

```yaml
interface:
  display_name: "CSV Insights"
  short_description: "Summarize CSV files into clean reports"
  default_prompt: "Use $csv-insights to summarize the CSV files in this repo."
policy:
  allow_implicit_invocation: true
```

## **Skill 사용하기**
Codex가 자동으로 판단하게 할 수도 있고, 명시적으로 부를 수도 있습니다.

```text
Use $csv-insights to summarize the CSV exports in ./reports.
```

또는 한국어로:

```text
$csv-insights 스킬을 사용해서 ./reports 안의 CSV들을 요약해줘.
```

반복되는 워크플로라면 skill에 넣고, 한 프로젝트에만 해당하는 규칙이면 `AGENTS.md`에 넣는 쪽이 낫습니다. 예를 들어 “우리 회사 CSV 포맷 분석법”은 skill, “이 repo에서는 테스트를 `pnpm test`로 돌린다”는 `AGENTS.md`가 어울립니다.

  
## **Custom Agent 만들기**
Codex subagent 문서 기준으로 현재 Codex는 `default`, `worker`, `explorer` 내장 에이전트를 제공합니다. 커스텀 에이전트는 개인용이면 `~/.codex/agents/`, 프로젝트 전용이면 `.codex/agents/` 아래 TOML 파일로 만듭니다. 필수 필드는 `name`, `description`, `developer_instructions`입니다. [Subagents docs](https://developers.openai.com/codex/subagents)

예:

```toml
# .codex/agents/reviewer.toml
name = "reviewer"
description = "PR reviewer focused on correctness, security, behavior regressions, and missing tests."
model = "gpt-5.4"
model_reasoning_effort = "high"
sandbox_mode = "read-only"

developer_instructions = """
Review code like an owner.
Lead with concrete findings.
Prioritize correctness, security, behavior regressions, and missing tests.
Avoid style-only comments unless they hide a real bug.
"""

nickname_candidates = ["Atlas", "Delta", "Echo"]
```

프로젝트 설정에서 동시 실행 한도를 조절할 수도 있습니다.

```toml
# .codex/config.toml
[agents]
max_threads = 6
max_depth = 1
```

공식 문서상 기본 `max_threads`는 6, 기본 `max_depth`는 1입니다. 깊이를 너무 올리면 하위 에이전트가 다시 하위 에이전트를 부르는 식으로 비용과 지연이 커질 수 있습니다.

## **Agent 사용하기**
Codex는 사용자가 명시적으로 요청할 때만 subagent를 spawn합니다. 예를 들어:

```text
이 PR을 main과 비교해서 리뷰해줘. 
보안, 버그, 테스트 누락, 유지보수성 각각을 별도 에이전트로 나눠 조사하고, 결과를 합쳐서 요약해줘.
```

커스텀 에이전트를 지정하려면:

```text
reviewer 에이전트로 이 diff를 리뷰하고, explorer 에이전트로 영향받는 코드 경로를 추적해줘. 둘 다 끝나면 결과를 합쳐줘.
```

CLI 안에서는 `/agent`로 활성 에이전트 스레드를 전환하거나 진행 중인 subagent를 볼 수 있습니다. subagent는 부모 세션의 sandbox/approval 정책을 상속하므로, 파일 쓰기나 네트워크 같은 권한 요청도 부모 흐름에 영향을 받습니다.

참고로 지금 로컬 `codex.cmd features list`에서는 `multi_agent`가 `stable true`로 켜져 있었습니다. 즉 이 환경은 subagent 워크플로를 쓸 수 있는 상태입니다.

## **언제 Skill, 언제 Agent?**
- 같은 절차를 앞으로도 반복한다: skill
- 특정 repo의 규칙을 항상 적용한다: `AGENTS.md`
- 큰 작업을 병렬로 쪼갠다: subagent
- 특정 역할을 자주 재사용한다: custom agent
- skill + MCP + 앱/도구를 묶어 배포한다: plugin

실전 추천 조합은 이렇습니다.

```text
AGENTS.md
  repo 공통 규칙: 테스트 명령, 코드 스타일, 금지사항

~/.codex/skills/
  반복 작업: 릴리즈 노트 작성, 로그 분석, CSV 리포트, 회사 API 운영 절차

.codex/agents/
  프로젝트 역할: reviewer, migration_worker, docs_researcher, qa_explorer
```

가장 좋은 첫 세팅은 `reviewer` custom agent 하나와, 자주 반복하는 업무 하나를 skill로 만드는 것입니다. 작게 시작해서 실제 작업 중 Codex가 헷갈린 부분만 skill에 추가하면 꽤 빨리 “내 팀 방식으로 일하는 Codex” 느낌이 납니다.
  
  
## **유명한 Skill**
Codex CLI에서 “유명한 스킬”은 아직 npm 패키지처럼 순위가 있는 생태계라기보다, 공식 플러그인/커뮤니티 워크플로에서 자주 보이는 패턴에 가깝습니다. 대표적으로는 이런 것들이 많이 쓰입니다.

| Skill | 용도 |
|---|---|
| `superpowers:brainstorming` | 기능 만들기 전에 요구사항/의도를 정리 |
| `superpowers:test-driven-development` | 버그 수정/기능 구현을 TDD 흐름으로 진행 |
| `superpowers:systematic-debugging` | 로그, 재현, 원인 가설을 순서대로 좁히는 디버깅 |
| `superpowers:writing-plans` | 큰 작업을 실행 가능한 체크리스트/계획으로 분해 |
| `superpowers:subagent-driven-development` | 계획을 subagent 기반으로 구현/리뷰 반복 |
| `superpowers:requesting-code-review` | 변경 후 코드 리뷰 subagent에게 점검 요청 |
| `superpowers:verification-before-completion` | “끝났다” 말하기 전에 테스트/검증 강제 |
| `documents` | `.docx`, Word 문서 생성/편집/검증 |
| `presentations` | PowerPoint/PPTX 덱 생성, 렌더링, 검증 |
| `spreadsheets` | Excel/CSV 분석, 수식, 차트, workbook 생성 |
| `openai-docs` | OpenAI API/Codex/모델 관련 최신 공식 문서 확인 |
| `imagegen` | 앱/문서/슬라이드에 쓸 이미지 생성/편집 |

OpenAI 공식 use case에서도 반복 워크플로를 skill로 저장하는 흐름, CSV/스프레드시트 분석, 프론트엔드 디자인 구현, PR 리뷰, 문서/슬라이드 생성 같은 작업이 주요 사례로 소개됩니다. [Codex use cases](https://developers.openai.com/codex/explore/)

**유명한 Agent 패턴**
Codex의 subagent/custom agent는 “유명한 이름”보다 역할 패턴이 중요합니다.

| Agent | 추천 역할 |
|---|---|
| `explorer` | 코드베이스 읽기 전용 조사원. 영향 범위, 호출 경로, 관련 파일 찾기 |
| `worker` | 제한된 파일/모듈을 맡아 실제 구현 |
| `reviewer` | 버그, 보안, 회귀, 테스트 누락 중심 리뷰 |
| `docs_researcher` | 공식 문서/MCP를 보고 API 사용법 검증 |
| `qa_explorer` | 재현 절차, 테스트 누락, edge case 탐색 |
| `migration_worker` | 마이그레이션 작업을 파일군별로 나눠 수행 |
| `frontend_reviewer` | UI 반응형, 접근성, 디자인 일관성 점검 |
| `security_reviewer` | auth, 권한, secret, injection, unsafe shell 등 점검 |

공식 Codex subagents 문서도 `pr_explorer`, `reviewer`, `docs_researcher` 같은 custom agent 예시를 듭니다. 핵심은 좁고 선명한 역할, 적절한 sandbox, 필요한 MCP/skill만 붙이는 것입니다. [Subagents docs](https://developers.openai.com/codex/subagents)

**내가 추천하는 조합**
개인/팀 Codex CLI 세팅이라면 이 5개부터 만들면 체감이 큽니다.

1. `reviewer` agent: PR/branch 리뷰 전용
2. `docs_researcher` agent: 공식 문서 확인 전용
3. `qa_explorer` agent: 테스트/재현/edge case 탐색
4. `release-notes` skill: changelog/릴리즈 노트 반복 작성
5. `repo-debugging` skill: 팀의 로그 위치, 테스트 명령, 배포 환경 지식 정리

한 줄로 말하면, **Skill은 “우리 팀의 반복 노하우를 저장하는 곳”**, **Agent는 “그 노하우를 역할별 작업자에게 나눠 맡기는 방법”**입니다.
