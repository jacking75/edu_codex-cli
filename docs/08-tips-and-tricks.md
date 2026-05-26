# 실전 팁 & 단축키

기준일: 2026-05-26

Codex CLI는 프롬프트 품질, 컨텍스트 관리, 승인 정책, 세션 관리에 따라 결과 품질이 크게 달라진다.

## 프롬프트 팁

좋은 기본 형식:

```text
목표:
맥락:
제약:
완료 조건:
```

예시:

```text
목표: 문서 목차를 최신 구조로 정리해줘.
맥락: README.md와 docs/ 폴더를 먼저 읽어줘.
제약: 한국어 문체를 유지하고 공식 링크를 삭제하지 마.
완료 조건: git diff --check가 통과해야 해.
```

## 계획 먼저 세우기

복잡한 작업은 바로 구현하지 말고 `/plan`을 사용한다.

```text
/plan
```

프롬프트 예시:

```text
먼저 관련 파일을 읽고 구현 계획만 작성해줘. 아직 파일은 수정하지 마.
```

## 컨텍스트 관리

긴 세션에서는 `/compact`를 사용한다.

```text
/compact
```

세션을 종료했다가 이어갈 때:

```powershell
codex resume --last
```

## 유용한 키 조작

| 조작 | 용도 |
|---|---|
| `Ctrl+C` | 세션 종료 또는 실행 중단 |
| `Ctrl+L` | 화면 정리 |
| `Ctrl+O` | 최신 완료 출력 복사 |
| `Tab` | 실행 중 follow-up 또는 slash command 예약 |
| `Up` / `Down` | composer draft history 탐색 |
| `Ctrl+R` | prompt history 검색 |

## 작업 안전 팁

- 시작 전에 `git status --short`를 확인하게 한다.
- 큰 변경은 작은 단계로 나눈다.
- 파일 수정 전 관련 테스트를 찾게 한다.
- 완료 후 `git diff` 리뷰를 요청한다.
- 새 의존성 추가는 별도 확인을 요구한다.

## Windows 팁

PowerShell에서 `codex`가 실행 정책 오류로 막히면 다음처럼 호출한다.

```powershell
codex.cmd
```

Linux toolchain이 전제인 프로젝트는 WSL2에서 실행한다.

```powershell
wsl
```

## 출처

- Best practices: https://developers.openai.com/codex/learn/best-practices
- Codex CLI features: https://developers.openai.com/codex/cli/features
- Slash commands: https://developers.openai.com/codex/cli/slash-commands
