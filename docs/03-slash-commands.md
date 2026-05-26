# 슬래시 커맨드 목록

기준일: 2026-05-26

대화형 Codex CLI 세션에서 `/`를 입력하면 slash command 목록을 볼 수 있다. Slash command는 모델, 권한, 컨텍스트, 화면, 플러그인 등을 빠르게 제어하는 키보드 중심 인터페이스다.

## 자주 쓰는 명령

| 명령 | 용도 | 사용할 때 |
|---|---|---|
| `/model` | 모델과 reasoning 수준 조정 | 속도와 추론 깊이를 바꾸고 싶을 때 |
| `/permissions` | 권한과 승인 수준 조정 | 읽기 전용, 자동 승인, 작업 공간 쓰기 등을 바꿀 때 |
| `/status` | 현재 세션 상태 확인 | 모델, 권한, 작업 디렉터리를 확인할 때 |
| `/plan` | 계획 모드 사용 | 복잡한 구현 전 조사와 계획이 필요할 때 |
| `/compact` | 대화 요약으로 컨텍스트 절약 | 긴 세션을 계속 이어갈 때 |
| `/clear` | 화면과 대화 초기화 | 새 대화를 시작할 때 |
| `/copy` | 최신 완료 응답 복사 | 결과를 문서나 이슈에 옮길 때 |
| `/agent` | subagent thread 전환 | 병렬 작업 결과를 확인할 때 |
| `/plugins` | plugin 목록과 검색 | plugin 기능을 확인하거나 관리할 때 |
| `/hooks` | lifecycle hook 확인 | hook을 신뢰하거나 비활성화할 때 |
| `/ide` | IDE 컨텍스트 포함 | 열려 있는 파일이나 선택 영역을 전달할 때 |
| `/vim` | Vim 입력 모드 전환 | composer에서 Vim 키맵을 쓸 때 |

## 실행 중 명령 예약

Codex가 작업 중일 때 slash command를 입력하고 `Tab`을 누르면 다음 턴에 예약할 수 있다.

```text
/compact
```

작업이 끝난 뒤 예약된 명령이 실행된다.

## 권한 관련 명령

권한을 바꿀 때는 `/permissions`를 우선 사용한다.

```text
/permissions
```

Windows에서 현재 sandbox가 읽을 수 없는 외부 디렉터리를 추가해야 할 때는 `/sandbox-add-read-dir`를 사용할 수 있다.

```text
/sandbox-add-read-dir
```

## 세션 정리 명령

긴 대화에서는 `/compact`를 주기적으로 사용한다.

```text
/compact
```

완전히 새 흐름으로 바꾸려면 `/clear`를 사용한다.

```text
/clear
```

## 출처

- Slash commands: https://developers.openai.com/codex/cli/slash-commands
- Codex CLI features: https://developers.openai.com/codex/cli/features
