# 코드 컨벤션 (BE)

## 전제

기존 3D 팀 오피스 서버를 그대로 쓰므로, 그 서버의 언어와 기존 코드 패턴을 가장 먼저 따릅니다. 이 페이지는 스택과 무관하게 지금 정할 수 있는 용어, 모듈 경계, 데이터 객체 구분, 에러·로그·보안을 확정하고, 스택에 따라 달라지는 것은 마지막 절에 비워 둡니다.

## 도메인 용어와 이름

정의서 1.3의 용어를 코드 이름의 기준으로 삼고, 같은 개념은 화면, API, DB, 코드 어디서나 같은 영어 이름을 씁니다.

| 정의서 용어 | 코드 이름 | 주의 |
| --- | --- | --- |
| 명세 문서 | `SpecDocument` |  |
| 요구사항 | `Requirement` | GitHub 이슈와 섞지 않음 |
| 작업 (이슈 하나) | `Task` | GitHub 원본은 `GithubIssue` |
| 제출 (PR) | `Submission` | GitHub 원본은 `GithubPullRequest` |
| 요구사항–이슈 연결 | `RequirementLink` | 앱 서버에만 저장 (FR-REQ-07) |
| 막힘 | `Blocker` | 이유와 경과 시간을 함께 가짐 |
| 찬성·반대 투표 | `Vote` | 값은 `approve`, `reject` |
| 회의 안건 | `AgendaItem` | 막힌 이슈와 PR 둘 다 |
| 스탠드업 | `Standup` |  |
| 컨벤션 규칙·위반 | `ConventionRule`, `ConventionViolation` |  |
| 회고 리포트 | `RetroReport` |  |
| 기술 결정 카드 | `DecisionCard` |  |
| 팩 | `Pack` | v1은 `bootcamp` |
| 데이터 어댑터 | `DataSource` | 기존 구조의 이름 유지 (CON-05) |

**이름 규칙**

- 함수는 동사로 시작합니다: `fetch`(외부에서 가져옴), `sync`(가져와 저장), `calc`(계산), `find`(앱 저장소에서 찾음).
- 참·거짓 값은 `is`, `has`, `can`으로 시작합니다. 예: `isBlocked`, `hasLinkedIssue`
- 시간 값에는 단위를 붙입니다. 예: `reviewWaitHours`, `syncIntervalMinutes`. 막힘 계산은 시간 단위가 섞이면 바로 틀립니다.
- GitHub 번호와 앱 내부 ID를 구분합니다. 예: `githubIssueNumber`(화면의 #12)와 `taskId`(앱 내부 값).

## 모듈 구조

NFR-08에 따라 핵심 규칙, 데이터 어댑터, 팩을 나눕니다. 폴더 이름은 스택 확인 후 정하되, 어느 모듈이 어느 모듈에 기대도 되는지는 지금 확정합니다.

&#91;embedded content: 모듈 구조 · api, adapters, store, packs, core\]

- core는 순수 규칙만 담아, 외부 서비스 없이 단위 테스트할 수 있게 합니다.
- GitHub API는 `adapters/github` 밖에서 부르지 않습니다. 호출량 관리(NFR-10)와 읽기 전용(CON-02)을 한곳에서 지키기 위해서입니다.
- 화면 시연과 테스트는 fake 어댑터로 합니다. GitHub 없이도 사무실이 채워져야 합니다.
- 담당은 기획안의 역할 분담(A 기획, B 연동 백엔드, C 도메인 백엔드, D 3D·프론트)을 따릅니다.

## 데이터 객체 규칙 (DTO·Entity)

데이터는 네 종류로 나누고 서로 섞지 않습니다. 특히 GitHub 원본을 화면까지 그대로 내보내지 않습니다.

| 종류 | 이름 | 어디서 쓰나 | 규칙 |
| --- | --- | --- | --- |
| GitHub 원본 | `Github*` | `adapters/github` 안 | API 응답 모양 그대로. 어댑터 밖으로 내보내지 않음 |
| 앱 모델·엔티티 | `Task`, `Submission`, `Vote` 등 | core, store | 저장 위치는 정의서 6장의 출처를 따름. GitHub에서 온 값은 `syncedAt`을 함께 저장 |
| 요청 DTO | `*Request` | api | 화면에서 받은 값. 서버에서 다시 검증 |
| 응답 DTO | `*Response` | api | 화면에 보내는 모양. 역할과 본인 여부에 따라 필드를 뺌 |

**응답 DTO에서 지킬 것**

- 습관 체크, 학습 타임라인, 회고 리포트는 본인 요청에만 내보냅니다 (FR-HAB-06, FR-REP-06, NFR-06).
- 사람별 투표 합계, 활동량 합계 같은 필드는 만들지 않습니다 (FR-FLOW-06, FR-LRN-09). 필드가 없으면 화면에서 순위를 만들 수도 없습니다.
- 시각은 UTC로 저장하고 응답에도 UTC로 보내며, 한국 시간 변환은 화면에서 합니다.

## 에러 처리·로그·보안

- **GitHub 실패는 삼키지 않습니다.** 동기화가 실패하면 마지막 성공 시각과 실패 사유를 남기고, 화면은 마지막 데이터로 유지합니다 (FR-GH-05, NFR-09).
- **에러 응답 모양을 통일합니다.** `{ "code": "...", "message": "..." }` 형태이고, code는 대문자 스네이크 케이스입니다. 예: `GITHUB_RATE_LIMITED`, `GITHUB_UNAUTHORIZED`, `FORBIDDEN_ROLE`, `VALIDATION_FAILED`, `NOT_FOUND`
- **로그에 토큰, 쿠키, 이메일을 남기지 않습니다** (NFR-04). 디버깅이 필요하면 앞 4자리만 남기고 가립니다.
- **권한은 API에서 확인합니다.** 화면에서 버튼을 숨긴 것을 권한 확인으로 보지 않고, 요청마다 역할(ROLE-01\~03)을 확인합니다 (FR-AUTH-04).
- **GitHub에 쓰는 호출은 만들지 않습니다.** v1은 읽기 전용이라(CON-02) GitHub API에 POST, PATCH, PUT, DELETE를 보내는 코드는 리뷰에서 바로 반대합니다.
- **기준값을 코드에 박지 않습니다.** 막힘 12시간·24시간, 투표 3명 중 2표, PR 400줄 같은 값은 팩과 레포 설정에서 읽습니다.

## 스택 확인 후 채울 것

| 항목 | 정할 것 |
| --- | --- |
| 언어·프레임워크 버전 | 기존 3D 팀 오피스 서버 기준 |
| 포매터·린터 | 도구와 설정 파일. 설정 파일은 레포에 커밋해 모두 같은 규칙으로 돌림 |
| 파일·클래스·변수 표기 | 언어의 관례를 따름 (예: 파일은 kebab-case, 타입은 PascalCase, 변수는 camelCase) |
| 폴더 이름 | 위 모듈 구조를 실제 경로로 옮김 |
| 테스트 도구 | core의 순수 규칙을 단위 테스트하는 도구 |
