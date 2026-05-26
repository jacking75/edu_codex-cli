# config.toml 레퍼런스

기준일: 2026-05-26

Codex CLI의 사용자 기본 설정은 `~/.codex/config.toml`에 둔다. 저장소별 설정은 `.codex/config.toml`에 둘 수 있으며, 프로젝트를 신뢰한 경우에만 로드된다.

## 설정 우선순위

높은 순서부터 적용된다.

1. CLI flag와 `--config`
2. `--profile <name>`
3. 프로젝트 `.codex/config.toml`
4. 사용자 `~/.codex/config.toml`
5. 시스템 설정
6. 내장 기본값

일회성 override:

```powershell
codex -c model=gpt-5.4 -c approval_policy=on-request
```

## 기본 예시

```toml
model = "gpt-5.4"
approval_policy = "on-request"

[features]
shell_snapshot = true
```

## 자주 쓰는 설정

| 키 | 용도 |
|---|---|
| `model` | 기본 모델 |
| `model_provider` | 기본 provider |
| `approval_policy` | 승인 요청 정책 |
| `sandbox_mode` | 기존 sandbox 모드 |
| `default_permissions` | beta permission profile 선택 |
| `project_doc_fallback_filenames` | `AGENTS.md` 외 지침 파일명 |
| `project_doc_max_bytes` | 프로젝트 지침 최대 크기 |
| `log_dir` | 로그 저장 위치 |
| `[features]` | 선택 기능 토글 |

## profile 예시

```toml
[profiles.safe]
approval_policy = "on-request"
default_permissions = ":workspace"

[profiles.readonly]
approval_policy = "never"
default_permissions = ":read-only"
```

사용:

```powershell
codex --profile safe
codex --profile readonly "이 저장소 구조만 요약해줘"
```

## 프로젝트 설정에서 피해야 할 것

프로젝트 `.codex/config.toml`은 공유될 수 있으므로 machine-local 값은 사용자 설정에 둔다. 공식 문서 기준 프로젝트 설정에서는 provider, auth, notification, telemetry routing 계열 키를 무시한다.

## 예시 파일

- [개인 기본 설정](../examples/config-toml/personal-default.toml)
- [읽기 전용 설정](../examples/config-toml/read-only.toml)
- [Windows sandbox 설정](../examples/config-toml/windows-sandbox.toml)

## 출처

- Config basics: https://developers.openai.com/codex/config-basic
- Config reference: https://developers.openai.com/codex/config-reference
