# services/payments/AGENTS.override.md

## Payments service rules

- 결제 도메인 변경 전 관련 테스트와 fixture를 먼저 읽는다.
- 테스트는 `make test-payments`를 사용한다.
- API key, token, secret 값은 로그, 테스트 fixture, 문서에 남기지 않는다.
- 외부 결제 provider 호출은 mock 또는 sandbox 환경에서만 수행한다.
