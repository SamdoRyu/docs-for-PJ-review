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

기준: 개발 컨벤션 > 코드 컨벤션 (BE) (`docs/plan/코드 컨벤션 (BE).md`). 구현 스택은 아직 정하지 않았다(`docs/ARCHITECTURE.md` 「기술 스택」). 데모 스택(TypeScript · Hono · `node:sqlite`)을 이어 쓰면 그 패턴을 먼저 따른다. 아래 「꼭 지킬 것」 · 「에러 응답」은 스택과 상관없이 지킨다.

**꼭 지킬 것**

- GitHub API는 `server/src/adapters/github/` 안에서만 부르고, GitHub 원본을 바꾸는 호출은 만들지 않는다 (CON-02). 인증용 POST(OAuth 코드 교환 · 설치 토큰 발급)는 별개다.
- GitHub 토큰은 서버에만 둔다. 로그에 토큰 · 쿠키 · 이메일을 남기지 않는다 (NFR-04).
- 권한은 API에서 확인한다. 역할은 로그인할 때 정해 세션에 두고, 세션은 30분~1시간 뒤 만료된다. `X-Actor`는 인증이 아니다.
- 개인 지표(습관 체크 · 학습 타임라인 · 회고 리포트)는 본인 요청의 응답에만 담는다. 사람별 합계 · 순위 필드는 만들지 않는다 (NFR-06, FR-FLOW-06, FR-LRN-09).
- GitHub 동기화 실패는 삼키지 않는다. 마지막 성공 시각과 실패 사유를 남기고 화면은 마지막 데이터로 유지한다 (FR-GH-05, NFR-09).
- 시각은 UTC로 저장 · 응답한다.

**에러 응답**: 데모 모양 `{ "error": { "code": "...", "message": "..." } }`. code는 소문자 snake — 기존 `invalid` · `not_found` · `conflict` · `unavailable`, 신규 `unauthorized` · `forbidden_role` · `github_unauthorized` · `github_rate_limited`.

**저장**: 데모처럼 엔티티별 `(id, data JSON)` 테이블. 쓰기는 `mutate()` 안에서, SSE 알림은 커밋 뒤.

**기준값**: 팀 설정 화면에서 바꾸는 값(프로젝트 기간, 막힘 기준 시간, 투표 기준)은 팀 설정에서 읽는다. 나머지 숫자는 `src/data/rules/constants.ts` 같은 상수 한 곳에 모은다.

**이름 (권장)**: 새 타입은 정의서 용어의 영어 이름(`Requirement`, `RequirementLink`, `Submission`, `Vote`, `AgendaItem`, `HelpRequest`, `DecisionCard` 등 — 표는 코드 컨벤션 (BE))을 쓰고, 데모에 이미 있는 이름(`Task`, `Member`, `Meeting`, `DataSource`)은 바꾸지 않는다. 시간 값에는 단위(`reviewWaitHours`), GitHub 번호와 앱 ID는 구분(`githubIssueNumber` ↔ `taskId`).
