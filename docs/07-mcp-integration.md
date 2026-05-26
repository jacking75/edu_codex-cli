# MCP 연동

기준일: 2026-05-26

MCP(Model Context Protocol)는 Codex가 외부 도구와 컨텍스트를 사용할 수 있게 해 주는 연결 방식이다. 문서 검색, 이슈 관리, 내부 도구 호출처럼 로컬 저장소 밖의 맥락이 필요한 작업에 적합하다.

## OpenAI Developer Docs MCP 등록

공식 OpenAI 문서를 Codex에서 검색하려면 다음처럼 등록한다.

```powershell
codex.cmd mcp add openaiDeveloperDocs --url https://developers.openai.com/mcp
```

PowerShell에서 `codex.ps1` 실행이 막히면 `codex.cmd`를 사용한다.

등록 후 새 Codex 세션을 시작하면 MCP 서버가 로드된다.

```powershell
codex
```

## 언제 MCP를 쓰는가

| 상황 | 예시 |
|---|---|
| 최신 공식 문서 확인 | OpenAI API, Codex docs 검색 |
| 사내 도구 연결 | ticket, deploy, observability |
| 외부 시스템 조회 | GitHub, Slack, Linear 등 |
| 반복 작업 자동화 | 문서 fetch 후 요약, 이슈 생성 |

## 설정 위치

MCP 서버 설정은 사용자 설정과 프로젝트 설정 계층의 영향을 받는다. 팀 공통 도구는 프로젝트 문서에 설치 방법을 남기고, secret이나 개인 token은 사용자 환경에 둔다.

## 보안 원칙

- MCP 서버가 어떤 도구를 제공하는지 확인한다.
- 쓰기 권한이 있는 MCP 도구는 승인 흐름을 둔다.
- secret은 config에 직접 쓰지 않는다.
- 팀 도구는 최소 권한 계정을 사용한다.

## 작업 예시

```text
OpenAI 공식 Codex CLI 문서를 MCP로 확인하고, 현재 docs/01-installation.md에서 오래된 설치 명령이 있으면 수정해줘.
```

## 문제 해결

MCP 서버가 보이지 않으면 다음을 확인한다.

- 새 Codex 세션을 시작했는가
- `codex mcp add`가 성공했는가
- 네트워크 접근이 필요한 MCP 서버인데 sandbox나 firewall에 막히지 않았는가
- PowerShell 실행 정책 때문에 `codex` 대신 `codex.cmd`가 필요한가

## 출처

- Codex MCP docs: https://developers.openai.com/codex/mcp
- Config basics: https://developers.openai.com/codex/config-basic
- OpenAI Docs MCP: https://developers.openai.com/mcp
