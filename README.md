# Codex CLI 학습 문서

OpenAI Codex CLI를 설치하고 실제 개발 작업에 쓰기 위해 알아야 할 내용을 한국어로 정리한 문서 저장소입니다.

기준일: 2026-05-26
기준 출처: OpenAI 공식 Codex 문서, `openai/codex` GitHub 최신 릴리스

## 목차

| 문서 | 내용 |
|---|---|
| [01-installation.md](docs/01-installation.md) | 설치 및 인증 |
| [02-basic-usage.md](docs/02-basic-usage.md) | 기본 사용법 |
| [03-slash-commands.md](docs/03-slash-commands.md) | 슬래시 커맨드 목록 |
| [04-agents-md.md](docs/04-agents-md.md) | AGENTS.md 작성 가이드 |
| [05-config.md](docs/05-config.md) | config.toml 레퍼런스 |
| [06-approval-sandbox.md](docs/06-approval-sandbox.md) | 승인 모드 & 샌드박스 |
| [07-mcp-integration.md](docs/07-mcp-integration.md) | MCP 연동 |
| [08-tips-and-tricks.md](docs/08-tips-and-tricks.md) | 실전 팁 & 단축키 |
| [09-sample-workflows.md](docs/09-sample-workflows.md) | 예제 워크플로 |
  
- [OpenAI Codex CLI 완전 정리 가이드](docs/openai-codex-cli-complete-guide.md)
- [PLANS.md를 사용하여 몇 시간씩 걸리는 문제 해결](docs/codex_exec_plans.md) 
- [스킬과 서브에이전트](docs/basic_skill_subagent.md)
- [Codex CLI Subagent: `docs_researcher`, `reviewer`, `worker` 완벽 가이드](docs/custom_agent-docs_researcher-reviewer-worker.md)
     


## 예시
- [AGENTS.md 예시 모음](examples/agents-md/)
- [config.toml 예시 모음](examples/config-toml/)

## 빠른 시작

```powershell
npm i -g @openai/codex
codex
```

Windows에서 PowerShell 실행 정책 때문에 `codex.ps1`이 막히면 다음처럼 실행합니다.

```powershell
codex.cmd
```
