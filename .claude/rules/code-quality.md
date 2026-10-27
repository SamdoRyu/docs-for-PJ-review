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

## 팀 오피스 BE 규칙 (스택 무관)

기준: 개발 컨벤션 > 코드 컨벤션 (BE). 기존 3D 팀 오피스 서버의 언어와 패턴을 먼저 따르고, 아래는 스택과 무관하게 지킨다.
TODO(스택 확인 후): 언어·프레임워크 버전, 포매터·린터, 파일·클래스·변수 표기, 폴더 이름, 테스트 도구.

**용어 사전** — 정의서 용어와 같은 영어 이름을 화면, API, DB, 코드 어디서나 쓴다.

| 정의서 용어 | 코드 이름 |
| --- | --- |
| 명세 문서 · 요구사항 | `SpecDocument` · `Requirement` (GitHub 이슈와 섞지 않음) |
| 작업 · 제출 | `Task` · `Submission` (GitHub 원본은 `GithubIssue` · `GithubPullRequest`) |
| 요구사항–이슈 연결 | `RequirementLink` (앱 서버에만 저장) |
| 막힘 · 투표 · 회의 안건 · 스탠드업 | `Blocker` · `Vote`(`approve`, `reject`) · `AgendaItem` · `Standup` |
| 컨벤션 규칙·위반 | `ConventionRule` · `ConventionViolation` |
| 회고 리포트 · 기술 결정 카드 | `RetroReport` · `DecisionCard` |
| 팩 · 데이터 어댑터 | `Pack`(v1은 `bootcamp`) · `DataSource` |

- 함수는 동사로 시작한다: `fetch`(외부에서 가져옴), `sync`(가져와 저장), `calc`(계산), `find`(앱 저장소에서 찾음).
- 참·거짓 값은 `is`, `has`, `can`으로 시작한다.
- 시간 값에는 단위를 붙인다. 예: `reviewWaitHours`, `syncIntervalMinutes`
- GitHub 번호와 앱 내부 ID를 구분한다. 예: `githubIssueNumber`와 `taskId`

**모듈 경계** (NFR-08)

- core는 순수 규칙만 담아 외부 서비스 없이 단위 테스트할 수 있게 한다.
- GitHub API는 `adapters/github` 밖에서 부르지 않는다 (NFR-10, CON-02).
- 화면 시연과 테스트는 fake 어댑터로 한다.

**데이터 객체 4종** — 서로 섞지 않는다.

| 종류 | 이름 | 규칙 |
| --- | --- | --- |
| GitHub 원본 | `Github*` | `adapters/github` 밖으로 내보내지 않는다 |
| 앱 모델·엔티티 | `Task`, `Submission`, `Vote` 등 | GitHub에서 온 값은 `syncedAt`을 함께 저장한다 |
| 요청 DTO | `*Request` | 화면에서 받은 값. 서버에서 다시 검증한다 |
| 응답 DTO | `*Response` | 역할과 본인 여부에 따라 필드를 뺀다 |

- 습관 체크, 학습 타임라인, 회고 리포트 같은 개인 지표는 본인 요청의 응답에만 담는다 (FR-HAB-06, FR-REP-06, NFR-06).
- 사람별 투표 합계, 활동량 합계 같은 사람별 합계 필드는 만들지 않는다 (FR-FLOW-06, FR-LRN-09).
- 시각은 UTC로 저장하고 UTC로 응답한다. 한국 시간 변환은 화면에서 한다.

**에러 처리와 기준값**

- 에러 응답은 `{ "code": "...", "message": "..." }` 형태로 통일하고, code는 대문자 스네이크 케이스다. 예: `GITHUB_RATE_LIMITED`, `GITHUB_UNAUTHORIZED`, `FORBIDDEN_ROLE`, `VALIDATION_FAILED`, `NOT_FOUND`
- GitHub 동기화 실패는 삼키지 않는다. 마지막 성공 시각과 실패 사유를 남기고 화면은 마지막 데이터로 유지한다 (FR-GH-05, NFR-09).
- 기준값을 코드에 하드코딩하지 않는다. 막힘 12시간·24시간, 투표 3명 중 2표, PR 400줄 같은 값은 팩과 레포 설정에서 읽는다.
