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
- 사용자의 GitHub 원본 변경 동작(이슈 · PR 생성 · 닫기 · 병합, 라벨 · 담당자 변경)은 서버가 대신하지 않고 GitHub 웹페이지로 이동시킨다 (P3).

## 심사용 로그인

- 보안 심사용 ID/PW 계정의 비밀번호는 안전한 해시로만 저장한다. 비밀번호 · 해시를 저장소 · 이슈 · PR에 넣지 않는다.
- 심사용 로그인은 명시적으로 켠 배포 환경에서만 동작한다(기본은 꺼짐). 심사용 계정은 시연 사무실에만 접근한다 (P15).
- 테스트 계정이라는 이유로 세션 · CSRF · 인가 검사를 건너뛰지 않는다.
