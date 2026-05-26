# 승인 모드 & 샌드박스

기준일: 2026-05-26

Codex는 로컬 명령 실행과 파일 접근을 제한하고, 필요한 경우 사용자 승인을 요청한다. 기본 원칙은 작업에 필요한 만큼만 권한을 주는 것이다.

## 권한 프로필

OpenAI 문서 기준 permission profiles는 beta 기능이며, filesystem 규칙과 network 규칙을 조합해 로컬 명령 실행 범위를 제한한다.

내장 프로필:

| 프로필 | 의미 |
|---|---|
| `:read-only` | 로컬 명령 실행을 읽기 중심으로 제한 |
| `:workspace` | 활성 workspace root 안에서 쓰기 허용 |
| `:danger-full-access` | 로컬 sandbox 제한 제거 |

예시:

```toml
default_permissions = ":workspace"
approval_policy = "on-request"
```

## 기존 sandbox 설정과의 관계

공식 문서 기준 permission profiles는 기존 `sandbox_mode` 설정과 함께 조합하지 않는다. 활성 설정에 `sandbox_mode`가 있거나 `--sandbox`를 전달하면 Codex는 기존 sandbox 설정을 사용한다.

## 승인 정책 선택

| 상황 | 권장 |
|---|---|
| 처음 보는 저장소 조사 | read-only 또는 승인 필요 |
| 일반 개발 작업 | workspace 쓰기 + on-request |
| CI 또는 격리 runner | 명시적 자동화 정책 |
| 개인 컴퓨터 전체 접근 | 피하는 편이 좋음 |

## 위험한 우회 옵션

`--dangerously-bypass-approvals-and-sandbox` 또는 `--yolo`는 승인과 sandbox를 우회한다. 격리된 runner에서만 사용한다.

```powershell
codex exec --dangerously-bypass-approvals-and-sandbox "테스트를 실행하고 실패 원인을 요약해줘"
```

일반 로컬 저장소에서는 위 명령을 쓰지 않는 편이 안전하다.

## Windows sandbox

Windows 네이티브 실행은 `elevated` sandbox와 `unelevated` fallback을 지원한다. 가능하면 `elevated`를 사용한다.

```toml
[windows]
sandbox = "elevated" # or "unelevated"
```

`elevated`는 낮은 권한의 sandbox 사용자, filesystem permission boundary, firewall rule 등을 사용한다. `unelevated`는 fallback이며 더 약하지만 enterprise 정책 때문에 elevated setup이 막힌 환경에서 쓸 수 있다.

## 운영 원칙

- 권한을 올리기 전에 명령이 왜 필요한지 확인한다.
- 네트워크 접근은 필요한 host만 허용한다.
- secret, token, key가 있는 디렉터리는 read/write scope에서 제외한다.
- 위험한 작업은 git 상태를 확인한 뒤 진행한다.

## 출처

- Permissions: https://developers.openai.com/codex/permissions
- Windows guide: https://developers.openai.com/codex/windows
- Command line options: https://developers.openai.com/codex/cli/reference
