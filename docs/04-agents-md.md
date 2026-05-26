# AGENTS.md 작성 가이드

기준일: 2026-05-26

`AGENTS.md`는 Codex가 작업 전에 읽는 프로젝트 지침 파일이다. 반복해서 말해야 하는 규칙, 테스트 명령, 코드 스타일, 보안 주의사항을 여기에 둔다.

## 발견 순서

Codex는 실행 시점에 지침 파일을 읽는다.

1. 전역 scope: 기본적으로 `~/.codex/AGENTS.override.md`가 있으면 먼저 사용하고, 없으면 `~/.codex/AGENTS.md`를 사용한다.
2. 프로젝트 scope: Git root에서 현재 디렉터리까지 내려오며 각 디렉터리의 `AGENTS.override.md`, `AGENTS.md`, fallback 파일을 확인한다.
3. 병합 순서: 루트에서 현재 디렉터리 방향으로 붙인다. 가까운 디렉터리의 지침이 뒤에 오므로 더 구체적인 규칙이 된다.

## 전역 지침 예시

```bash
mkdir -p ~/.codex
```

```markdown
# ~/.codex/AGENTS.md

## Working agreements

- 변경 전 관련 파일을 먼저 읽는다.
- JavaScript 파일을 수정하면 `npm test`를 실행한다.
- 새 production dependency 추가 전 확인을 요청한다.
```

## 저장소 지침 예시

```markdown
# AGENTS.md

## Repository expectations

- 문서는 한국어로 작성한다.
- 명령어, 파일명, 옵션명은 원문 표기를 유지한다.
- 각 문서 끝에 공식 출처 링크를 남긴다.
```

## 하위 디렉터리 override

특정 영역만 다른 규칙을 써야 하면 가까운 디렉터리에 `AGENTS.override.md`를 둔다.

```markdown
# services/payments/AGENTS.override.md

## Payments service rules

- 결제 도메인은 `make test-payments`를 실행한다.
- API key, token, secret 값을 로그나 문서에 남기지 않는다.
```

## fallback 파일명 설정

이미 `TEAM_GUIDE.md` 같은 파일을 쓰고 있다면 fallback 이름으로 등록할 수 있다.

```toml
# ~/.codex/config.toml
project_doc_fallback_filenames = ["TEAM_GUIDE.md", ".agents.md"]
project_doc_max_bytes = 65536
```

## 작성 원칙

- 항상 지켜야 하는 규칙만 쓴다.
- 명령은 복사해 실행 가능한 형태로 쓴다.
- 저장소별 규칙과 개인 취향을 분리한다.
- 보안상 민감한 값은 쓰지 않는다.
- 임시 override는 작업 후 삭제한다.

## 예시 파일

- [개인 기본 지침](../examples/agents-md/personal-default.md)
- [문서 저장소 지침](../examples/agents-md/docs-repository.md)
- [서비스별 override](../examples/agents-md/service-override.md)

## 출처

- AGENTS.md guide: https://developers.openai.com/codex/guides/agents-md
- Config reference: https://developers.openai.com/codex/config-reference
