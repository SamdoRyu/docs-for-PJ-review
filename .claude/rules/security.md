# Security

- secret, token, password, private key를 source code에 넣지 않는다.
- `.env` 계열 파일 내용을 읽거나 출력하지 않는다.
- 로그에 인증정보/개인정보를 남기지 않는다.
- 클라이언트 입력은 server에서 다시 검증한다.
- 인증(authentication)과 인가(authorization)를 구분한다.
- UI에서 버튼을 숨기는 것을 권한 검증으로 간주하지 않는다.
- SQL/query/path/url을 사용자 입력과 단순 문자열 결합하지 않는다.
- 새로운 dependency는 필요성과 유지보수 상태를 확인한 뒤 추가한다.
- 보안에 영향을 주는 변경은 PR의 위험 범위에 명시한다.

## GitHub 연동

- GitHub 토큰은 읽기 전용 최소 권한으로 발급하고, 서버에만 둔다. 브라우저로 내보내지 않는다 (NFR-04).
- GitHub 원본(이슈 · PR · 코멘트 · 라벨 등)을 바꾸는 GitHub API 호출을 보내지 않는다. 앱은 GitHub를 읽기만 한다 (CON-02). OAuth 코드 교환 · GitHub App 설치 토큰 발급 같은 인증 요청과 앱 서버 자체 API의 쓰기는 별개다 (docs/ARCHITECTURE.md 「GitHub 읽기 전용 원칙」).
- GitHub App 키 파일(`*.pem`)은 `secrets/`에만 둔다. 다른 경로에 두거나 커밋하지 않는다.
