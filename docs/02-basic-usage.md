# 기본 사용법

기준일: 2026-05-26

Codex CLI는 터미널에서 대화형 TUI로 실행하거나, `codex exec`로 비대화형 작업을 실행할 수 있다.

## 대화형 실행

현재 디렉터리에서 Codex를 시작한다.

```powershell
codex
```

시작 프롬프트를 함께 전달한다.

```powershell
codex "이 저장소 구조와 테스트 방법을 요약해줘"
```

작업 디렉터리를 지정한다.

```powershell
codex --cd F:\github_edu\00_edu_codex-cli
```

## 이전 세션 이어가기

최근 세션 목록에서 선택한다.

```powershell
codex resume
```

현재 디렉터리의 마지막 세션을 바로 연다.

```powershell
codex resume --last
```

다른 디렉터리의 세션까지 포함한다.

```powershell
codex resume --all
```

## 비대화형 실행

스크립트나 CI처럼 한 번 실행하고 종료할 때는 `codex exec`를 사용한다.

```powershell
codex exec "README의 오탈자를 찾아 수정하고 변경 요약을 출력해줘"
```

작업 디렉터리 지정:

```powershell
codex exec --cd F:\github_edu\00_edu_codex-cli "문서 링크가 깨졌는지 확인해줘"
```

stdin으로 프롬프트 전달:

```bash
printf "릴리스 노트를 요약해줘" | codex exec -
```

exec 세션 이어가기:

```powershell
codex exec resume --last "남은 실패 테스트를 계속 고쳐줘"
```

## 좋은 프롬프트 구조

Codex에게 일을 맡길 때는 네 가지를 포함한다.

| 항목 | 설명 |
|---|---|
| 목표 | 무엇을 만들거나 고칠지 |
| 맥락 | 관련 파일, 오류, 로그, 문서 |
| 제약 | 코딩 스타일, 테스트 방식, 보안 조건 |
| 완료 조건 | 어떤 테스트나 동작으로 완료를 판단할지 |

예시:

```text
목표: 로그인 실패 시 에러 메시지를 한국어로 표시해줘.
맥락: src/auth, tests/auth 관련 파일을 먼저 읽어줘.
제약: 기존 i18n 헬퍼를 사용하고 새 의존성은 추가하지 마.
완료 조건: 관련 테스트를 추가하고 npm test가 통과해야 해.
```

## 작업 중 확인할 것

- Codex가 어떤 파일을 읽고 수정하는지 확인한다.
- 위험한 명령은 승인 요청 내용을 읽고 판단한다.
- 완료 후 `git diff`를 직접 검토한다.
- 테스트 실행 결과를 확인한다.

## 출처

- Codex CLI features: https://developers.openai.com/codex/cli/features
- Command line options: https://developers.openai.com/codex/cli/reference
- Best practices: https://developers.openai.com/codex/learn/best-practices
