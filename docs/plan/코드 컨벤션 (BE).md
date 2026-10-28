# 코드 컨벤션 (BE)

> 2026-10-28 결정 회의 반영. 꼭 지킬 것만 남기고 나머지는 권장으로 낮춘 판

## 전제

> **구현 스택은 아직 정하지 않았습니다**(ARCHITECTURE.md 「기술 스택」 ① 데모 스택 · ② HTML · CSS · JS 위주). 이 문서의 경로 · 도구 · 코드 흐름은 ①을 가정합니다. ②로 정하면 「꼭 지킬 것」 · 「에러 응답」 · 「이름」만 그대로 두고 나머지는 다시 씁니다.

①로 가면 기존 3D 팀 오피스 데모 코드를 고쳐 가며 구현하므로 **그 코드의 언어와 패턴을 먼저 따릅니다.** 서버는 TypeScript + Hono(`@hono/node-server`) + SQLite(`node:sqlite`)이고, 화면과 서버가 한 저장소에서 타입과 순수 규칙(`src/data/rules/`)을 함께 씁니다. Node는 24 LTS로 맞춥니다(`.nvmrc`). 스택과 경로는 ARCHITECTURE.md를 봅니다.

새 규칙과 기존 코드가 다르면 기존 코드는 그대로 두고 새 코드에만 적용합니다.

## 꼭 지킬 것

보안과 제품 정책에서 나온 것이라 리뷰에서 바로 반대합니다.

1. **GitHub API는 `server/src/adapters/github/` 안에서만 부릅니다.** 읽기만 합니다 — 이슈 만들기 · 코멘트 · 라벨 · 머지 · 상태 변경처럼 GitHub 원본을 바꾸는 호출은 만들지 않습니다(CON-02). OAuth 코드 교환과 GitHub App 설치 토큰 발급은 인증 절차라 `POST`여도 괜찮고, 앱 서버 자체 API의 쓰기도 별개입니다.
2. **GitHub 토큰은 서버에만 둡니다.** 브라우저로 보내지 않고, 로그에 토큰 · 쿠키 · 이메일을 남기지 않습니다(NFR-04).
3. **권한은 API에서 확인합니다.** 버튼을 숨긴 것은 권한 확인이 아닙니다(FR-AUTH-04). `X-Actor` 헤더는 데모 봇 표시용이라 사용자 확인에 쓰지 않습니다.
4. **개인 지표(습관 체크 · 학습 타임라인 · 회고 리포트)는 본인 요청의 응답에만 담습니다**(NFR-06). **사람별 합계 · 순위 필드는 만들지 않습니다**(FR-FLOW-06, FR-LRN-09).
5. **GitHub 동기화 실패를 삼키지 않습니다.** 마지막 성공 시각과 실패 사유를 남기고 화면은 마지막 데이터로 둡니다(FR-GH-05, NFR-09).
6. **SQL은 매개변수 바인딩으로만** 씁니다. 사용자 입력을 문자열로 이어 붙이지 않습니다.
7. **시각은 UTC ISO 문자열**로 저장 · 응답합니다. 한국 시간 변환은 화면에서 합니다.

## 에러 응답

데모 모양을 그대로 씁니다. code는 소문자 snake입니다.

```json
{ "error": { "code": "forbidden_role", "message": "이 작업을 할 권한이 없어요" } }
```

| code | HTTP | 언제 |
| --- | --- | --- |
| `invalid` | 400 (요청 위생 검사 실패는 403) | 입력 검증 실패. 이유 없는 반대 투표 등 — 기존 code |
| `not_found` | 404 | 대상 없음 — 기존 code |
| `conflict` | 409 | 상태 충돌 — 기존 code |
| `unavailable` | 503 | 서버 사용 불가 · 알 수 없는 오류 — 기존 code |
| `unauthorized` | 401 | 로그인 안 됨 · 세션 만료 — 신규 |
| `forbidden_role` | 403 | 역할이 맞지 않음 — 신규 |
| `github_unauthorized` | 503 | GitHub 인증 실패(설치 토큰 등) — 신규 |
| `github_rate_limited` | 503 | GitHub 호출 한도 초과 — 신규 |

- 실패는 `DataError(message, code)`를 던지고 라우트의 `onError`가 응답으로 바꿉니다. 새 code를 더하면 `src/data/api/index.ts`의 `toError`와 `DataError` 타입도 같이 고칩니다.
- `message`는 사용자에게 보일 한국어 문장입니다(`src/data/rules/messages.ts`).

## 서버 코드 흐름

데모 코드의 흐름 **라우트 → repo → rules**를 따릅니다.

- 라우트(`server/src/routes.ts`)는 요청을 읽고 repo를 부르는 일만 합니다.
- 검증은 `src/data/rules/validate.ts`의 `check*`처럼 공유 규칙으로 둡니다. 브라우저 mock과 서버가 같은 검증을 씁니다.
- repo(`server/src/repo.ts`)의 쓰기는 `mutate()` 안에서 합니다. 트랜잭션 안에서 저장하고, 커밋이 끝난 뒤에 SSE로 알립니다.
- 새 라우트에도 요청 위생 미들웨어(Host · Origin · Content-Type 검사)를 그대로 거칩니다.

## 인증 (신규)

- GitHub 로그인 뒤 서버 세션을 만듭니다. **세션은 30분\~1시간 뒤 만료**됩니다.
- 역할(참여자 · 레포 관리자)은 **로그인할 때 GitHub 권한으로 한 번 정하고** 세션에 둡니다. 요청마다 GitHub에 다시 묻지 않습니다. 전체 관리자는 `MASTER_ADMIN_GITHUB_LOGIN`으로 지정합니다.
- 기초 보안까지만 합니다: 세션 쿠키는 `HttpOnly` · `SameSite`, 쓰기 요청은 기존 Origin 검사를 거침.

## 저장 (SQLite)

- 데모처럼 **엔티티마다 `(id TEXT PRIMARY KEY, data TEXT)` 테이블에 JSON으로 저장**합니다. 새 항목도 같은 방식입니다.
- 마이그레이션 도구는 없습니다. `CREATE TABLE IF NOT EXISTS`와 `repo.migrate()`를 씁니다. 테스트는 `':memory:'` DB.
- GitHub에서 온 값은 `syncedAt`을 같이 저장합니다.

## 이름 (권장)

- **새 타입은 아래 이름을 씁니다.** 데모에 이미 있는 이름(`Task`, `Member`, `Meeting`, `DataSource`, `Activity`)은 바꾸지 않습니다.

  | 정의서 용어 | 코드 이름 |
  | --- | --- |
  | 명세 문서 · 요구사항 | `SpecDocument` · `Requirement` |
  | 제출(PR) | `Submission` (GitHub 원본 타입은 `GithubPullRequest`, 이슈는 `GithubIssue`) |
  | 요구사항–이슈 연결 | `RequirementLink` |
  | 막힘 · 투표 · 회의 안건 · 스탠드업 | `Blocker` · `Vote`(`approve`, `reject`) · `AgendaItem` · `Standup` |
  | 도움 요청 | `HelpRequest` |
  | 컨벤션 규칙 · 위반 | `ConventionRule` · `ConventionViolation` |
  | 회고 리포트 · 기술 결정 카드 | `RetroReport` · `DecisionCard` |
  | 팀 설정 | `TeamSettings` |

- 파일 · 변수 표기는 데모 관례를 따릅니다: 컴포넌트 `PascalCase.tsx`, 그 밖의 모듈 `camelCase.ts`, 서버 파일 소문자, 상수 `UPPER_SNAKE`, 문자열 유니온 값 소문자 snake(`'in_progress'`).
- ID는 데모처럼 접두사 붙은 문자열입니다(`t_`, `m_`, `mt_`). 새 엔티티도 접두사를 하나 정해 씁니다.
- 헷갈리기 쉬운 값만 이름으로 구분합니다.
  - 시간 값에는 단위: `reviewWaitHours`, `sessionTtlMinutes`
  - GitHub 번호와 앱 ID: `githubIssueNumber` ↔ `taskId`
  - 참 · 거짓은 `is` · `has` · `can`으로 시작하면 읽기 좋습니다.
- 함수 동사는 따로 정하지 않습니다. 데모의 `compute` · `plan` · `apply` · `check`를 참고하고, GitHub 코드는 `fetch` · `sync` 정도면 충분합니다.

## 기준값

- **팀 설정 화면에서 바꾸는 값**(프로젝트 기간, 막힘 기준 시간 — 기본 리뷰 대기 48시간 · 커밋 없음 48시간, 투표 기준 — 기본 3명 중 2표, 레포 관리자가 정함 · FR-LRN-03)은 `TeamSettings`에서 읽습니다.
- 그 밖의 숫자(PR 400줄, 폴링 5분, 도움 요청이 안건 후보가 되는 48시간 등)는 `src/data/rules/constants.ts`처럼 **상수 한 곳에 모아 두면 됩니다.** 코드 여기저기에 숫자를 직접 쓰지만 않으면 됩니다.

## 모듈 (권장)

| 모듈 | 경로 |
| --- | --- |
| 순수 규칙 (core) | `src/data/rules/` — GitHub · DB · env를 부르지 않아 단위 테스트가 쉬움 |
| API | `server/src/routes.ts` |
| 저장 | `server/src/repo.ts` · `db.ts` |
| GitHub 어댑터 | `server/src/adapters/github/` (신규) |
| 인증 | `server/src/auth/` (신규) |
| 가짜 데이터 | `src/data/mock/` — 브라우저 mock을 새 모델로 넓혀서 화면 작업 · 시연에 씀 |

- 화면은 `src/data/DataSource.ts` 인터페이스로만 데이터를 받습니다. 화면 코드에서 `fetch('/api/…')`를 직접 부르지 않습니다.
- 요청 · 응답 타입을 따로 둘 필요는 없습니다. 본인 여부로 필드를 빼야 하는 응답만 `*Response` 타입을 따로 둡니다.

## 도구

| 항목 | 내용 |
| --- | --- |
| 린터 | `oxlint` (`npm run lint`). 포매터는 새로 넣지 않습니다 |
| 타입 검사 | `tsc -b`. 서버 · `src/data` 쪽(`tsconfig.tools.json`)은 strict |
| 테스트 | 규칙 · 계약 테스트 `npm run test:contract`, E2E `npm run test:e2e`(Playwright). 명령은 PROJECT_COMMANDS.md |
