# AGENT.md

## 저장소 목적
OpenAI Codex CLI의 사용법, 팁, 설정 예시를 한국어로 정리하는 **문서 저장소**다.
코드 실행이 아닌 **문서 작성과 구조화**가 핵심이다.

---

## 저장소 구조

```
codex-cli-guide/
├── AGENT.md
├── README.md                  ← 소개 및 목차
├── docs/
│   ├── 01-installation.md     ← 설치 및 인증
│   ├── 02-basic-usage.md      ← 기본 사용법
│   ├── 03-slash-commands.md   ← 슬래시 커맨드 목록
│   ├── 04-agents-md.md        ← AGENTS.md 작성 가이드
│   ├── 05-config.md           ← config.toml 레퍼런스
│   ├── 06-approval-sandbox.md ← 승인 모드 & 샌드박스
│   ├── 07-mcp-integration.md  ← MCP 연동
│   ├── 08-tips-and-tricks.md  ← 실전 팁 & 단축키
│   └── 09-sample-workflows.md ← 예제 워크플로
└── examples/
    ├── agents-md/             ← AGENTS.md 예시 모음
    └── config-toml/           ← config.toml 예시 모음
```

---

## 문서 작성 규칙
- 본문은 **한국어**, 명령/파일명/옵션은 **영어 원문** 그대로 사용한다.
- 문체는 간결한 설명체(`~한다`, `~이다`)를 사용한다.
- 모든 명령과 설정에는 **복사 즉시 사용 가능한 예시**를 최소 1개 포함한다.
- 코드 블록은 언어를 반드시 명시한다.
- 각 문서 하단에 관련 **공식 문서 링크**를 추가한다.
- 신규 파일 추가 시 `README.md` 목차도 함께 갱신한다.
  
  
---

## 금지 사항
- 공식 문서에서 확인되지 않은 명령/플래그를 임의로 추가하지 않는다.
- 예시 코드에 실제 API 키나 시크릿을 포함하지 않는다 (`YOUR_API_KEY` 플레이스홀더 사용).

---

## 작업 완료 조건
1. 내용이 공식 문서와 일치한다.
2. 모든 명령/설정에 동작 가능한 예시가 있다.
3. 마크다운 문법 오류가 없다.
4. 기존 문서와 스타일이 일관된다.

---

## 참고 링크

| 문서 | URL |
|---|---|
| Codex CLI 공식 문서 | https://developers.openai.com/codex/cli |
| 슬래시 커맨드 레퍼런스 | https://developers.openai.com/codex/cli/slash-commands |
| AGENTS.md 가이드 | https://developers.openai.com/codex/guides/agents-md |
| config.toml 레퍼런스 | https://developers.openai.com/codex/config-reference |
| Best Practices | https://developers.openai.com/codex/learn/best-practices |
| GitHub (openai/codex) | https://github.com/openai/codex |  