# 예제 워크플로

기준일: 2026-05-26

이 문서는 Codex CLI에 바로 입력할 수 있는 작업 요청 예시를 모은다.

## 저장소 파악

```powershell
codex "이 저장소의 구조, 빌드 방법, 테스트 방법, 주요 위험 요소를 요약해줘. 파일은 수정하지 마."
```

## 버그 수정

```powershell
codex "tests/auth/login.test.ts 실패를 재현하고 원인을 찾은 뒤, 가장 작은 수정으로 고쳐줘. 수정 후 관련 테스트를 실행해줘."
```

## 코드 리뷰

```powershell
codex "현재 git diff를 리뷰해줘. 버그, 회귀 위험, 테스트 누락을 우선순위대로 알려줘."
```

## 문서 업데이트

```powershell
codex "공식 문서를 확인해 docs/01-installation.md를 최신 설치 방법 중심으로 업데이트해줘. 출처 링크를 하단에 남겨줘."
```

## 테스트 보강

```powershell
codex "최근 변경된 동작에 대한 테스트가 부족한지 확인하고, 가장 중요한 회귀 테스트를 추가해줘. 테스트가 실패하는 이유와 통과 결과를 요약해줘."
```

## 리팩터링

```powershell
codex "src/parser 모듈의 중복을 줄여줘. 공개 API는 바꾸지 말고, 변경 전후 테스트를 실행해줘."
```

## 비대화형 문서 검사

```powershell
codex exec "docs/ 폴더의 링크와 markdown 문법 문제를 점검하고 수정 가능한 것은 고쳐줘"
```

## 릴리스 노트 초안

```powershell
codex "현재 브랜치의 git diff를 바탕으로 사용자 관점의 릴리스 노트 초안을 작성해줘. 내부 구현 세부사항은 줄이고 영향 중심으로 정리해줘."
```

## MCP를 사용한 최신 문서 확인

```text
OpenAI Developer Docs MCP를 사용해 Codex CLI permissions 문서를 확인하고, 이 저장소의 승인/샌드박스 문서가 오래된 부분이 있으면 수정해줘.
```

## 출처

- Best practices: https://developers.openai.com/codex/learn/best-practices
- Command line options: https://developers.openai.com/codex/cli/reference
- Codex CLI features: https://developers.openai.com/codex/cli/features
