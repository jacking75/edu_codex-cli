# 설치 및 인증

기준일: 2026-05-26

이 문서는 Codex CLI를 설치하고 처음 로그인하는 방법을 정리한다. Codex CLI는 macOS, Windows, Linux에서 사용할 수 있으며, ChatGPT 계정 로그인 또는 API key 방식으로 인증할 수 있다.

## 최신 버전 확인

설치 전후에 현재 설치 버전과 최신 패키지 버전을 확인한다.

```powershell
codex --version
npm view @openai/codex version
```

GitHub 최신 릴리스도 함께 확인한다.

```text
https://github.com/openai/codex/releases/latest
```

2026-05-26 확인 기준 최신 GitHub 릴리스는 `0.133.0`이다.

## Windows 설치

공식 설치 스크립트:

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

npm 사용:

```powershell
npm i -g @openai/codex
```

PowerShell 실행 정책 때문에 npm shim인 `codex.ps1`이 막히면 `codex.cmd`를 사용한다.

```powershell
codex.cmd --version
codex.cmd
```

Linux 도구 체인이 필요한 저장소는 WSL2에서 실행하는 편이 낫다.

```powershell
wsl --install
wsl
```

WSL 안에서:

```bash
npm i -g @openai/codex
codex
```

## macOS 또는 Linux 설치

공식 설치 스크립트:

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

npm 사용:

```bash
npm i -g @openai/codex
```

Homebrew 사용:

```bash
brew install --cask codex
```

## 직접 바이너리 설치

패키지 매니저를 쓸 수 없다면 GitHub 최신 릴리스에서 플랫폼에 맞는 아카이브를 받는다.

```text
https://github.com/openai/codex/releases/latest
```

압축을 푼 뒤 실행 파일 이름을 `codex`로 바꾸고 `PATH`에 포함된 디렉터리에 둔다.

## 인증

처음 실행하면 인증 방식을 선택한다.

```powershell
codex
```

권장 방식은 ChatGPT 계정 로그인이다. OpenAI 공식 문서 기준 ChatGPT Plus, Pro, Business, Edu, Enterprise 플랜에는 Codex 사용이 포함된다.

API key 방식은 자동화, CI, 별도 작업 계정에 적합하다. 개인 로컬 계정과 자동화 계정을 분리하려면 `CODEX_HOME`을 별도로 지정한다.

```bash
CODEX_HOME=$(pwd)/.codex codex exec "현재 인증 상태와 설정 출처를 요약해줘"
```

## 업데이트

npm 설치:

```powershell
npm i -g @openai/codex@latest
```

Homebrew 설치:

```bash
brew upgrade --cask codex
```

## 출처

- OpenAI Codex CLI: https://developers.openai.com/codex/cli
- Windows guide: https://developers.openai.com/codex/windows
- GitHub releases: https://github.com/openai/codex/releases/latest
