# Code Quality

- 현재 codebase의 패턴을 먼저 따른다.
- 중복을 제거한다는 이유만으로 성급한 추상화를 만들지 않는다.
- 함수/컴포넌트는 하나의 주된 책임을 갖도록 한다.
- 이름은 구현 방식보다 역할과 의미를 표현한다.
- 불필요한 상태를 만들지 않는다.
- 파생 가능한 값은 가능하면 저장 상태로 중복 관리하지 않는다.
- error를 catch한 뒤 무시하지 않는다.
- 외부 데이터의 null/undefined/invalid 가능성을 고려한다.
- magic value가 도메인 의미를 가진다면 상수/타입으로 표현한다.
- 기존 public contract를 바꾸면 호출부 전체를 확인한다.
- 생성 파일을 사람이 수동 편집하지 않는다.

## 오피스 BE 규칙

기준: 개발 컨벤션 > 코드 컨벤션 (BE) (`docs/plan/코드 컨벤션 (BE).md`) · 공통 계약 `docs/CONTRACTS.md`. 백엔드는 Java 21 · Gradle · Spring Boot(Spring MVC · Spring Data JPA · Spring Security 세션) · MySQL 8.x로 새로 만들고, 프론트는 데모(React · TypeScript · R3F)를 재사용한다(2026-11-01 결정 · 추가 결정 — `docs/ARCHITECTURE.md` 「기술 스택」). 데모의 Hono · `node:sqlite` 서버는 참고만 한다. 아래 「꼭 지킬 것」 · 「에러 응답」은 스택과 상관없이 지킨다.

**꼭 지킬 것**

- GitHub API는 백엔드의 GitHub 어댑터 패키지(`github`) 안에서만 부르고, GitHub 원본을 바꾸는 호출은 만들지 않는다 (CON-02). 인증용 POST(OAuth 코드 교환 · 설치 토큰 발급)는 별개다.
- GitHub 토큰은 서버에만 둔다. 로그에 토큰 · 쿠키 · 이메일을 남기지 않는다 (NFR-04).
- 권한은 API에서 확인한다. GitHub 권한으로 정하는 역할(admin = 레포 관리자, 그 밖의 콜라보레이터 = 참여자)은 로그인할 때 정해 세션에 두고, 세션은 30분~1시간 뒤 만료된다. 강사는 승인된 강사 계정 중에서만 지정하고(일반 학생 불가 — 2026-11-01 결정), 강사 지정은 요청마다 앱 DB에서 확인한다 (FR-AUTH-06). 승인된 강사 계정은 서버 설정 허용 목록(GitHub 사용자 ID)이고, 이 계정은 콜라보레이터여도 참여자 · 레포 관리자로 판정하지 않으며 지정 밖 사무실은 `not_found`다(2026-11-01 추가 결정). 사무실(Workspace) 접근도 요청마다 확인하고, SSE도 같은 검사를 거친다. `X-Actor`는 인증이 아니다.
- 개인 지표(학습 타임라인 · 회고 리포트 · 기술 결정 카드)는 본인과 강사 요청의 응답에만 담는다. 사람별 합계 · 순위 필드는 만들지 않는다 (NFR-06, FR-FLOW-06, FR-LRN-09).
- 강사는 보기 전용이다. 학생이 요청한 건의 답변 · 꼬리질문 · 결정 피드백과 컨벤션 라이브러리 편집 밖의 강사 쓰기는 `forbidden_role`로 거부한다 (FR-AUTH-08).
- 보안 심사용 ID/PW 로그인은 명시적으로 켠 배포 환경에서만 동작하고 시연 사무실에만 접근한다. 로그인 뒤에는 GitHub 로그인과 같은 세션 · 인가 검사를 거친다 (P15).
- GitHub 동기화 실패는 삼키지 않는다. 마지막 성공 시각과 실패 사유를 남기고 화면은 마지막 데이터로 유지한다 (FR-GH-05, NFR-09).
- 시각은 UTC로 저장 · 응답한다.

**에러 응답**: 데모 모양 `{ "error": { "code": "...", "message": "..." } }`. code는 소문자 snake — 기존 `invalid` · `not_found` · `conflict` · `unavailable`, 신규 `unauthorized` · `forbidden_role` · `github_unauthorized` · `github_rate_limited`.

**저장**: Spring Data JPA Entity(데모의 `(id, data JSON)` 방식은 쓰지 않음 — 2026-11-01 결정). Controller → Service(트랜잭션) → Repository, Entity를 응답으로 바로 내보내지 않는다. 사무실 단위 데이터는 항상 사무실 조건으로 조회한다. SSE 알림은 커밋 뒤. 공통 계약 변경은 `docs/CONTRACTS.md` 「9. 계약 변경 절차」대로 따로 낸다.

**기준값**: 팀 설정 화면에서 바꾸는 값(프로젝트 기간, 막힘 기준 시간)은 팀 설정에서 읽는다. 나머지 숫자는 상수 한 곳(백엔드 한 곳, 화면 계산용 프론트 한 곳)에 모은다.

**이름 (권장)**: 새 타입은 정의서 용어의 영어 이름(`Requirement`, `RequirementLink`, `Submission`, `Evaluation`, `AgendaItem`, `HelpRequest`, `DecisionCard` 등 — 표는 코드 컨벤션 (BE))을 쓰고, 데모에 이미 있는 이름(`Task`, `Member`, `Meeting`, `DataSource`)은 바꾸지 않는다. 시간 값에는 단위(`reviewWaitHours`), GitHub 번호와 앱 ID는 구분(`githubIssueNumber` ↔ `taskId`).
