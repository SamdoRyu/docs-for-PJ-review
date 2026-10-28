# 코드 컨벤션 (BE)

## 전제

기존 3D 팀 오피스(데모 저장소 `SamdoRyu/warehouseDemo_fork`)를 그대로 쓰므로, 그 코드의 언어와 패턴을 가장 먼저 따릅니다. 서버는 **TypeScript + Hono(Node, `@hono/node-server`) + SQLite(`node:sqlite`)**이고, 화면과 서버가 한 저장소에서 타입과 순수 규칙(`src/data/rules/`)을 함께 씁니다. 스택과 버전은 ARCHITECTURE.md 「기술 스택」을 봅니다.

이 페이지에서 **현재 코드**는 데모 소스에서 확인한 관례(2026-10-28, 코드를 읽어서 확인 · 실행은 안 함)이고, **변경 제안**은 v1을 위해 새로 정하는 규칙입니다. 둘이 다르면 기존 코드를 바꾸지 않고 새 코드부터 적용하는 것을 기본으로 합니다.

## 도메인 용어와 이름

정의서 1.3의 용어를 코드 이름의 기준으로 삼고, 같은 개념은 화면, API, DB, 코드 어디서나 같은 영어 이름을 씁니다. 아래 이름 중 데모 코드에 **이미 있는 것은 `DataSource`와 `Task`뿐**이고, 나머지는 v1에서 새로 만들 타입입니다.

| 정의서 용어 | 코드 이름 | 현재 코드 | 주의 |
| --- | --- | --- | --- |
| 명세 문서 | `SpecDocument` | 없음 (신규) |  |
| 요구사항 | `Requirement` | 없음 (신규) | GitHub 이슈와 섞지 않음 |
| 작업 (이슈 하나) | `Task` | 있음 — 앱에서 만드는 작업(포인트 · 우선순위 · 마감 포함) | v1에서는 GitHub 이슈에서 바꾼 앱 모델. GitHub 원본은 `GithubIssue`. 필드 출처는 06-data.md 「기존 데모 데이터와의 대응」 |
| 제출 (PR) | `Submission` | 없음 (신규) | GitHub 원본은 `GithubPullRequest` |
| 요구사항–이슈 연결 | `RequirementLink` | 없음 (신규) | 앱 서버에만 저장 (FR-REQ-07) |
| 막힘 | `Blocker` | 없음 (신규) | 이유와 경과 시간을 함께 가짐 |
| 찬성·반대 투표 | `Vote` | 없음 (신규 · [제안]) | 값은 `approve`, `reject` |
| 회의 안건 | `AgendaItem` | 없음 — 지금은 `Task.onAgenda` 불리언 | 막힌 이슈와 PR 둘 다 |
| 회의 | (기존 이름 `Meeting`) | 있음 — 참석자 · 안건 작업 id · 결과(담당 지정 · 상태 변경 등) | 결과가 GitHub 원본을 바꾸는 구조라 v1에서 결정 기록으로 바뀜 |
| 스탠드업 | `Standup` | 없음 (신규) |  |
| 컨벤션 규칙·위반 | `ConventionRule`, `ConventionViolation` | 없음 (신규) |  |
| 회고 리포트 | `RetroReport` | 없음 (신규) |  |
| 기술 결정 카드 | `DecisionCard` | 없음 (신규 · [제안]) |  |
| 팩 | `Pack` | 없음 (신규) | v1은 `bootcamp` |
| 데이터 어댑터 | `DataSource` | 있음 — `src/data/DataSource.ts`(브라우저 쪽) | 기존 구조의 이름 유지 (CON-05). 서버 쪽 GitHub 어댑터와 구분 — ARCHITECTURE.md 「데이터 소스는 두 층이다」 |

데모의 `Activity`(작업 · 팀원 변경 로그)는 기획의 "활동 기록"(FR-ACT, 코드 밖 활동)과 다른 것입니다. 이름이 겹치므로 FR-ACT를 만들 때 다른 이름을 씁니다 — 결정 필요.

**이름 규칙**

| 대상 | 현재 코드 관례 | 예 |
| --- | --- | --- |
| React 컴포넌트 파일 | PascalCase `.tsx` | `TaskBox.tsx`, `MeetingDialogs.tsx` |
| 그 밖의 모듈 파일 | camelCase `.ts` | `dropTarget.ts`, `selectors.ts`, `uiState.ts` |
| 서버 파일 | 소문자 한 단어 `.ts` | `routes.ts`, `repo.ts`, `events.ts` |
| 타입 · 인터페이스 | PascalCase, 접두사 없음 | `Task`, `DataSource`, `MeetingStart` |
| 함수 · 변수 | camelCase | `computeSlots`, `openPoints` |
| 상수 | UPPER_SNAKE | `MAX_ACTIVITY`, `TOUCH_WINDOW_MS`, `DONE_SLOTS` |
| 문자열 유니온 값 | 소문자 snake | `'in_progress'`, `'not_found'` |

- 함수 이름의 동사 — **현재 코드**는 `compute`(계산 · `computeKpis`), `plan`(바꿀 내용을 계산만 · `planMeetingStart`), `apply`(패치 적용 · `applyTaskPatch`), `check`(검증 · `checkTaskInput`), `create`(팩토리 · `createRepo`)를 씁니다. **변경 제안**: 새 GitHub · 동기화 코드에는 `fetch`(외부에서 가져옴), `sync`(가져와 저장), `find`(앱 저장소에서 찾음)를 더합니다. 계산은 기존 관례인 `compute`를 그대로 쓰고 `calc`는 새로 만들지 않는 것을 권장합니다 — 결정 필요(`.claude/rules/code-quality.md`의 `calc`와 함께 맞춤).
- 참·거짓 값은 `is`, `has`, `can`으로 시작합니다. 예: `isBlocked`, `hasLinkedIssue`. 현재 코드에도 `isOpen`, `isOverdue`가 있습니다.
- 시간 값에는 단위를 붙입니다. 예: `reviewWaitHours`, `syncIntervalMinutes`. 현재 코드도 `TOUCH_WINDOW_MS`, `LONG_PRESS_MS`처럼 단위를 붙입니다. 막힘 계산은 시간 단위가 섞이면 바로 틀립니다.
- GitHub 번호와 앱 내부 ID를 구분합니다. 예: `githubIssueNumber`(화면의 #12)와 `taskId`(앱 내부 값). 현재 `Task.number`는 앱이 매기는 번호이므로 GitHub 번호와 섞지 않습니다.
- ID는 현재 코드처럼 접두사가 붙은 문자열입니다(`t_`, `m_`, `mt_`, `a_`). 새 엔티티도 같은 방식을 권장합니다.

## 모듈 구조

NFR-08에 따라 핵심 규칙, 데이터 어댑터, 팩을 나눕니다. 실제 경로는 ARCHITECTURE.md 「모듈 이름과 실제 경로」에 있습니다.

| 모듈 | 현재 경로 | 기댈 수 있는 곳 |
| --- | --- | --- |
| `core` (순수 규칙) | `src/data/rules/` | `src/data/types.ts` · `meta.ts`만. GitHub · DB · env · 브라우저 API를 부르지 않음 |
| `api` | `server/src/routes.ts` | `store` · `core` · 인증 |
| 인증 | 없음 — 권장 `server/src/auth/` | `store` · `adapters/github`(GitHub 호출은 직접 하지 않음) |
| `store` | `server/src/repo.ts` · `db.ts` | `core` · `events.ts`(커밋 뒤 알림) |
| `adapters/github` | 없음 — 권장 `server/src/adapters/github/` | `core`(변환 규칙) · `store` |
| `adapters/fake` | `src/data/mock/` (브라우저 — 이것을 fake로 쓸지는 권장안, 결정 필요) | `core` |
| `packs` | 없음 — 권장 `packs/bootcamp/` | 아무것에도 기대지 않음(값만) |

- core는 순수 규칙만 담아, 외부 서비스 없이 단위 테스트할 수 있게 합니다. 현재 `src/data/rules/`가 이미 그렇게 쓰이고 있고(`tests/contract/rules.test.ts`), `tsconfig.tools.json`에서 strict로 검사됩니다.
- GitHub API는 `adapters/github` 밖에서 부르지 않습니다. 호출량 관리(NFR-10)와 읽기 전용(CON-02)을 한곳에서 지키기 위해서입니다.
- 화면은 `src/data/DataSource.ts` 인터페이스로만 데이터를 받습니다. 화면 코드에서 `fetch('/api/…')`를 직접 부르지 않습니다(현재 코드도 `src/data/api/`에만 있음).
- 화면 시연과 테스트는 fake로 합니다(권장안: 현재 `src/data/mock/`을 확장 — ARCHITECTURE.md 「데이터 소스는 두 층이다」). GitHub 없이도 사무실이 채워져야 합니다.
- 담당은 기획안의 역할 분담(A 기획, B 연동 백엔드, C 도메인 백엔드, D 3D·프론트)을 따릅니다.

## Hono 서버 작성 규칙

현재 코드의 흐름은 **라우트 → repo → rules**입니다.

- **라우트(`routes.ts`)**는 요청 본문을 읽고 repo 함수를 부르고 응답 상태 · 헤더(`X-Seq`)를 정하는 일만 합니다. 업무 판단을 라우트에 쓰지 않습니다.
- **검증**은 `src/data/rules/validate.ts`의 `check*` 함수처럼 공유 규칙으로 둡니다. 브라우저 mock과 서버가 같은 검증을 쓰므로 화면에서 걸러도 서버에서 다시 검증됩니다. 검증 순서(먼저 실패한 것이 이김)도 계약의 일부입니다.
- **repo(`repo.ts`)**의 모든 쓰기는 `mutate()` 안에서 합니다. 트랜잭션(`BEGIN IMMEDIATE`)으로 감싸고, 변경 순번(`seq`)을 같은 트랜잭션에서 저장하고, **커밋이 끝난 뒤에만** SSE로 알립니다. 새 저장 항목도 이 순서를 지킵니다.
- 실패는 `DataError(message, code)`를 던지고, 라우트의 `onError`가 응답으로 바꿉니다. 알 수 없는 오류는 콘솔에 남기고 503으로 응답합니다.
- 서버 시작 · 종료는 `app.ts`의 `startServer` · `close()`가 맡습니다(종료 중에는 503, SSE를 먼저 닫음).
- 새 라우트에는 요청 위생 미들웨어(Host · Origin · Content-Type 검사)가 그대로 적용됩니다. 이 검사를 우회하는 경로를 만들지 않습니다.

## 데이터 객체 규칙 (DTO·Entity)

데이터는 네 종류로 나누고 서로 섞지 않습니다. 특히 GitHub 원본을 화면까지 그대로 내보내지 않습니다.

| 종류 | 이름 | 어디서 쓰나 | 규칙 |
| --- | --- | --- | --- |
| GitHub 원본 | `Github*` | `adapters/github` 안 | API 응답 모양 그대로. 어댑터 밖으로 내보내지 않음 |
| 앱 모델·엔티티 | `Task`, `Submission`, `Vote` 등 | core, store | 저장 위치는 정의서 6장의 출처를 따름. GitHub에서 온 값은 `syncedAt`을 함께 저장 |
| 요청 DTO | `*Request` | api | 화면에서 받은 값. 서버에서 다시 검증 |
| 응답 DTO | `*Response` | api | 화면에 보내는 모양. 역할과 본인 여부에 따라 필드를 뺌 |

- **현재 코드**는 요청 · 응답 타입을 따로 두지 않고 `src/data/types.ts`의 타입을 그대로 씁니다(요청은 `TaskInput` · `TaskPatch` · `MeetingStart` 같은 `Pick` 타입, 응답은 엔티티 그대로). **변경 제안**: 역할 · 본인 여부로 필드를 빼야 하는 응답(투표자, 개인 지표)부터 `*Response` 타입을 따로 둡니다. 나머지는 기존처럼 써도 됩니다.
- 시각은 현재 코드처럼 ISO 문자열(UTC)로 저장 · 응답하고, 날짜만 필요한 값은 `YYYY-MM-DD`입니다.

**응답 DTO에서 지킬 것**

- 습관 체크, 학습 타임라인, 회고 리포트는 본인 요청에만 내보냅니다 (FR-HAB-06, FR-REP-06, NFR-06).
- 사람별 투표 합계, 활동량 합계 같은 필드는 만들지 않습니다 (FR-FLOW-06, FR-LRN-09). 필드가 없으면 화면에서 순위를 만들 수도 없습니다. 현재 코드의 업무량 · 포인트 비교(`computeKpis`의 `busiestId` 등)는 제거 대상입니다 — ARCHITECTURE.md 「사람별 비교 표현 — 제거 대상」.
- 시각은 UTC로 저장하고 응답에도 UTC로 보내며, 한국 시간 변환은 화면에서 합니다.

## 저장 (SQLite)

- **현재 코드**: `node:sqlite`의 `DatabaseSync`, 파일 `server/data/office.db`. 엔티티마다 `(id TEXT PRIMARY KEY, data TEXT)` 테이블에 JSON 문자열로 저장하고, 목록 순서는 `rowid`입니다. 마이그레이션 도구는 없고 `CREATE TABLE IF NOT EXISTS`와 `repo.migrate()`로 옛 데이터를 고칩니다. 테스트는 `':memory:'` DB를 씁니다.
- 새 저장 항목을 같은 JSON blob 방식으로 더할지, 조회가 잦은 칸(예: 이슈 번호, 레포)을 열로 나눌지는 결정 필요입니다. 어느 쪽이든 DB schema 변경이므로 PR의 「영향 범위 / 위험」에 적습니다.
- SQL은 매개변수 바인딩으로만 씁니다. 사용자 입력을 SQL 문자열에 이어 붙이지 않습니다.

## 에러 처리·로그·보안

- **GitHub 실패는 삼키지 않습니다.** 동기화가 실패하면 마지막 성공 시각과 실패 사유를 남기고, 화면은 마지막 데이터로 유지합니다 (FR-GH-05, NFR-09).
- **에러 응답 모양** — 결정 필요. 비교와 권장안은 ARCHITECTURE.md 「에러 응답 형식 — 결정 필요」에 있습니다.
  - 현재 코드: `{ "error": { "code": "invalid", "message": "…" } }`, code는 `invalid` · `not_found` · `conflict` · `unavailable` 네 가지. 클라이언트(`src/data/api/index.ts` `toError`)가 code로 동작을 나눕니다.
  - 기획 초안: `{ "code": "...", "message": "..." }`, code는 대문자 스네이크(`GITHUB_RATE_LIMITED`, `GITHUB_UNAUTHORIZED`, `FORBIDDEN_ROLE`, `VALIDATION_FAILED`, `NOT_FOUND`).
  - 권장안: 바깥 모양은 현재 코드를 유지하고 code 표기만 팀이 정합니다. 정하기 전에는 새 에러 code를 만들 때 리뷰에서 표기를 확인합니다.
- 에러 메시지는 현재 코드처럼 사용자에게 보여 줄 한국어 문장으로 씁니다(`src/data/rules/messages.ts`).
- **로그에 토큰, 쿠키, 이메일을 남기지 않습니다** (NFR-04). 디버깅이 필요하면 앞 4자리만 남기고 가립니다. 현재 서버는 예상하지 못한 오류만 `console`에 남깁니다.
- **권한은 API에서 확인합니다.** 화면에서 버튼을 숨긴 것을 권한 확인으로 보지 않고, 요청마다 역할(ROLE-01\~03)을 확인합니다 (FR-AUTH-04). 현재 코드에는 인증 · 권한 검사가 없고, `X-Actor` 헤더는 데모 봇 표시용이라 사용자 확인에 쓰지 않습니다. 로그인 · 세션 · 역할 검사는 신규로 만듭니다(BACKEND_ASSIGNMENTS.md B1).
- **GitHub 원본을 바꾸는 호출은 만들지 않습니다** (CON-02). 이슈 만들기 · 코멘트 · 라벨 · 머지 · 상태 변경처럼 GitHub의 이슈 · PR을 바꾸는 GitHub API 호출은 리뷰에서 바로 반대합니다. GitHub 로그인의 OAuth 코드 교환과 GitHub App 설치 토큰 발급은 인증 절차라 `POST`여도 허용하고, 앱 서버 자체 API의 쓰기 요청도 별개입니다 — ARCHITECTURE.md 「GitHub 읽기 전용 원칙」.
- **기준값을 코드에 박지 않습니다.** 막힘 12시간·24시간, 투표 3명 중 2표, PR 400줄 같은 값은 팩과 레포 설정에서 읽습니다. 현재 코드의 상수(`src/data/rules/constants.ts`, `WORKLOAD_LEVELS` 등)는 데모 동작용이고, v1 기준값은 그 자리에 새로 박지 않습니다.

## 스택 기준 설정

| 항목 | 현재 코드 | 비고 |
| --- | --- | --- |
| 언어·프레임워크 버전 | TypeScript 6.0.3, React 19.3.0, Hono 4.13.13, Vite 8.3.2 (lock 기준) | ARCHITECTURE.md 「기술 스택」. Node 버전은 결정 필요(권장 Node 24 LTS) |
| 포매터·린터 | 린터 `oxlint`(`.oxlintrc.json` — react · typescript · oxc 플러그인, `react/rules-of-hooks` error). 포매터 없음 | 포매터를 새로 넣을지는 결정 필요. 넣더라도 전체 파일 일괄 포맷은 별도 PR로 |
| 타입 검사 | `tsc -b` (프로젝트 참조). `tsconfig.app.json`(화면)은 `strict`가 꺼져 있고, `tsconfig.tools.json`(서버 · `src/data` · `src/office` · 테스트 · 스크립트)은 `strict: true` | 서버 · 규칙 코드는 strict로 작성 |
| 파일·클래스·변수 표기 | 위 「이름 규칙」 표 | — |
| 폴더 이름 | 위 「모듈 구조」 표 | 신규 폴더 경로는 권장안 |
| 테스트 도구 | 계약 · 규칙 테스트 `node:test` + `tsx --test`(`tests/contract/`), E2E Playwright(`tests/e2e/`) | 명령은 PROJECT_COMMANDS.md. core 규칙은 `rules.test.ts`처럼 단위 테스트 |
