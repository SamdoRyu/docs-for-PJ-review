# Architecture

화면과 서버 코드를 만들기 전에 이 문서를 읽는다. **git 흐름(브랜치·커밋·PR·리뷰·머지)은 여기 없다 — `.claude/rules/git-workflow.md`(개발 컨벤션 > Git 전략)를 본다.**

- 누가 어느 화면을 맡는지: [SCREEN_ASSIGNMENTS.md](./SCREEN_ASSIGNMENTS.md)
- 누가 어느 서버 기능을 맡는지: [BACKEND_ASSIGNMENTS.md](./BACKEND_ASSIGNMENTS.md)
- 실행·검증 명령과 환경 변수: [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md)

> **근거와 표기**: 기존 3D 팀 오피스는 데모 저장소 `SamdoRyu/warehouseDemo_fork`(`FG-Kaki/warehouseDemo`의 fork, `main`)다. 이 문서의 "현재 구현"은 2026-10-28에 그 저장소의 소스를 GitHub API로 읽어 확인한 내용이다 — **클론 · 실행 · 테스트 실행은 하지 않았다.** 경로는 데모 저장소 기준 상대 경로다.
>
> - **현재 구현**: 데모 코드에 실제로 있는 것
> - **신규 예정**: 오피스 v1을 위해 새로 만들 것. 경로는 권장안이고 아직 없다
> - **결정 필요**: 팀이 정해야 하는 것. 모아서 PRD.md 「열린 질문」

## 두 저장소

| 저장소 | 역할 | 비고 |
| --- | --- | --- |
| `SamdoRyu/docs-for-PJ-review` (이 저장소) | 기획 · 설계 · 개발 규칙 문서, Issue · PR 템플릿 초안 | 앱 코드가 없다 |
| `SamdoRyu/warehouseDemo_fork` | 기존 3D 데모 코드. 오피스 v1의 동작 · 화면 구성을 참고하는 기준 구현이다(CON-05) | 참고 원본. **실제 개발은 새로 만들 개발 레포에서 한다**(2026-10-28 결정). 레포 설정은 Git 전략 「8」 |

데모 저장소의 `docs/PLAN.md`는 과거 팀 관리 대시보드(관리자 1명 + 팀원 6~10명) 기획이다. 오피스 v1의 요구사항 기준으로 쓰지 않는다.

## 기술 스택

**구현 스택은 아직 정하지 않았다 — 결정 필요.** 코드 가이드라인과 부트캠프 기술 조건이 나온 뒤 아래 둘 중 하나로 정한다. 어느 쪽이든 오피스 v1의 기능(로그인, GitHub 동기화, 요구사항 구역, 투표, 회의실 안건, 도움 요청, 습관 체크, 회고 리포트 등)은 새로 구현한다.

| 안 | 내용 | 데모 코드와의 관계 |
| --- | --- | --- |
| ① 데모 스택을 이어 쓴다 | React · TypeScript · Three.js · Hono · SQLite(아래 표) | 데모 코드를 고쳐 가며 구현. 이 문서의 경로 · 대응표가 그대로 작업 위치가 된다 |
| ② HTML · CSS · JavaScript 위주로 새로 구현 | 프레임워크 없이 또는 최소한으로. 3D 라이브러리 · 서버 구성은 그때 정한다 | 데모는 동작 · 데이터 흐름 · 화면 구성을 참고만 한다. 이 문서의 경로는 "참고할 곳"으로 읽는다 |

②를 고르면 CON-05("기존 3D 팀 오피스 위에 구축하고, 기존 DataSource 구조의 이름을 유지")를 어떻게 지킬지(구조와 이름만 이어받기 등)도 함께 정한다. 그 전까지 이 문서와 코드 컨벤션(BE)은 ①을 가정하고 쓰여 있다.

### 데모가 쓰는 스택 (참고 — 데모 `package.json` · `package-lock.json`)

| 구분 | 패키지 | `package.json` 범위 | lock 실제 버전 |
| --- | --- | --- | --- |
| 언어 | TypeScript | `~6.0.2` | 6.0.3 |
| 화면 | react · react-dom | `^19.2.8` | 19.3.0 |
| 3D | three | `^0.186.1` | 0.186.1 |
| 3D | @react-three/fiber | `^9.8.1` | 9.8.1 |
| 3D | @react-three/drei | `^10.7.9` | 10.7.9 |
| 상태 관리 | zustand | `^5.0.15` | 5.0.15 |
| 서버 | hono | `^4.13.13` | 4.13.13 |
| 서버 | @hono/node-server | `^2.1.3` | 2.1.3 |
| DB | `node:sqlite` (Node 내장 `DatabaseSync`) | 의존성 아님 | Node 버전에 따름 |
| 빌드 | vite (Rolldown 기반) | `^8.3.0` | 8.3.2 |
| 빌드 | @vitejs/plugin-react | `^6.1.1` | 6.1.2 |
| 실행 | tsx | `^4.23.15` | 4.23.15 |
| 실행 | concurrently | `^10.0.5` | 10.0.5 |
| 린터 | oxlint | `^1.81.0` | 1.87.0 |
| 테스트 | @playwright/test (E2E) | `^1.64.0` | 1.64.0 |
| 테스트 | `node:test` + tsx (계약 테스트) | 의존성 아님 | — |
| 타입 | @types/node | `^24.13.3` | 24.19.1 |

- 앱 코드는 전부 TypeScript(`.ts` · `.tsx`)다. JavaScript는 `scripts/check-bundle.mjs` 같은 스크립트에만 있고, HTML은 `index.html` 하나, CSS는 `src/index.css`와 `src/ui/*.css`다.
- 서버 변경 알림은 SSE(직접 구현, 라이브러리 없음), 포매터는 없다(oxlint만).
- **Node 버전**: 데모에는 `engines`와 `.nvmrc`가 없다. 의존성 조건으로 보면 Node 22.13 이상이 필요하다(`node:sqlite`를 플래그 없이 쓰려면 22.13+, concurrently ≥22, vite ≥22.12). **Node 24 LTS로 통일한다**(2026-10-28 결정) — 개발 저장소에 `.nvmrc`를 추가한다(Git 전략 「8」).
- **부트캠프 기술 조건**: 기본 조건은 HTML · CSS · JavaScript이고 다른 라이브러리 · 프레임워크도 허용된다고 전달받았다. 별도 평가 기준(예: 순수 JS 비중)이 있는지 확인되지 않아, 위 ① · ② 중 어느 쪽으로 갈지 정하지 않았다 — 결정 필요.

### 신규 예정 (선정 필요)

| 구분 | 상태 |
| --- | --- |
| GitHub API 호출 방식(직접 `fetch` 또는 클라이언트 라이브러리) | 선정 필요 |
| GitHub App JWT 서명 · 설치 토큰 발급 | 선정 필요(라이브러리 또는 직접 구현) |
| 로그인 세션(쿠키 서명 방식) | 선정 필요 — Hono 내장 기능으로 될지 먼저 확인 |
| 명세 문서(마크다운) 파싱 | 선정 필요(형식은 ## 제목만 — TBD-01, 2026-10-28 결정) |

새 dependency는 필요성과 유지보수 상태를 확인하고 팀에 먼저 말한 뒤 추가한다(`.claude/rules/security.md`). 프레임워크 교체는 하지 않는다.

## 폴더 구조

### 현재 구현

```text
warehouseDemo_fork/
├─ index.html · vite.config.ts · tsconfig*.json · .oxlintrc.json · playwright.config.ts
├─ src/
│  ├─ main.tsx · App.tsx          앱 시작. ?source=api 로 DataSource 를 고른다
│  ├─ app/                        SceneCanvas(Canvas) · SceneBoundary(3D 오류 경계)
│  ├─ scene/                      3D 장면 — OfficeScene, TaskBox(es), slots, Avatar(s), Desks, Room,
│  │                              Furniture, MeetingRoomState, DragLayer, dropTarget, CameraRig, Overlays …
│  ├─ office/layout.ts            사무실 크기 · 책상 10개 · 구역(회의실 · 라운지 · 백로그 선반 · 완료 선반 · 입구)
│  ├─ data/
│  │  ├─ DataSource.ts            화면이 데이터를 받는 인터페이스 · DataError
│  │  ├─ types.ts · meta.ts       모델 타입 · 라벨 · 상수
│  │  ├─ selectors.ts · insights.ts   계산(업무량 · KPI · 신호 · 추천)
│  │  ├─ rules/                   mock 과 서버가 함께 쓰는 순수 규칙(검증 · 시드 · 회의 · 데모 봇 계획)
│  │  ├─ mock/                    브라우저 localStorage 데이터 소스 · 데모 시뮬레이션
│  │  └─ api/                     서버 API 클라이언트(index.ts) · SSE 수신(sse.ts)
│  ├─ store.ts                    zustand 스토어 — 데이터 사본 · 선택 · 드래그 · 낙관적 갱신
│  ├─ theme.ts · index.css
│  └─ ui/                         HUD — TopBar, RightPanel, Detail, Kanban/List/Team/WeekView,
│                                 MeetingDialogs, Dialogs, Suggestions, ActivityFeed, Toasts …
├─ server/src/
│  ├─ index.ts · app.ts           서버 시작(127.0.0.1, PORT ?? 8787) · 조립
│  ├─ routes.ts                   Hono 라우트 · 요청 위생 미들웨어 · 에러 응답
│  ├─ repo.ts · db.ts             저장 로직 · SQLite(node:sqlite)
│  ├─ events.ts                   SSE 허브
│  └─ bot/index.ts                로컬 데모 봇(가상 팀원)
├─ tests/contract/ · tests/e2e/   계약 테스트(node:test) · E2E(Playwright)
└─ scripts/                       check-bundle.mjs(빌드 후 번들 검사) · data-smoke.ts
```

### 모듈 이름과 실제 경로

모듈 이름과 의존 방향은 코드 컨벤션(BE) 「모듈」 · NFR-08 그대로다. 실제 경로는 아래처럼 대응한다.

| 모듈 | 현재 구현에서 해당하는 곳 | 신규 예정 (권장 경로) |
| --- | --- | --- |
| `core` (순수 규칙) | `src/data/rules/` — 이미 mock과 서버가 함께 쓰는 순수 규칙이고 `tsconfig.tools.json`에서 strict로 검사된다 | 막힘 계산 · 컨벤션 검사 · 투표 판정 · 카드 조립을 같은 폴더에 더한다 |
| `api` | `server/src/routes.ts` | 인증 · 역할 검사 미들웨어, 응답 DTO 조립 |
| `store` | `server/src/repo.ts` · `server/src/db.ts` | 06-data.md 「저장 항목」 테이블 |
| `adapters/github` | 없음 | `server/src/adapters/github/` — GitHub API를 부르는 유일한 곳 |
| `adapters/fake` | `src/data/mock/` (브라우저 쪽) | 브라우저 mock을 새 모델로 확장해 쓴다(2026-10-28 결정 — 「데이터 소스는 두 층이다」) |
| `adapters/local` | 없음 | 무엇을 뜻하는지 기준 문서에 정의가 없다 — 결정 필요 |
| `packs` | 없음(기준값은 `src/data/rules/constants.ts` · `src/data/meta.ts`에 상수로 있음) | `packs/bootcamp/` — 기준값 기본값 · 용어 |
| 인증 | 없음 | `server/src/auth/` — 세션 · 역할 판정. OAuth 코드 교환 · 사용자 정보 · 레포 권한 조회처럼 GitHub를 부르는 일은 `adapters/github`의 함수를 거친다("GitHub API는 `adapters/github`에서만" 규칙 유지) |

데모의 `CLAUDE.md`는 `src/data/types.ts` · `store.ts` · `layout.ts` · `selectors.ts` · `insights.ts`를 계약 파일로 보고 메인 세션에서만 바꾸게 한다. 팀 작업에서도 이 파일들은 「건드리기 전에 말해야 하는 곳」이다(SCREEN_ASSIGNMENTS.md).

## 실행 구조

### 현재 구현된 구조

```text
브라우저 (Vite :5173)
  └─ src/App.tsx — ?source=api 이면 API DataSource, 아니면 mock DataSource
       ├─ mock: src/data/mock — localStorage(`office-dashboard:v1`), 서버 없음
       └─ api : src/data/api — fetch /api/* (Vite 가 127.0.0.1:API_PORT ?? 8787 로 프록시)
                 └─ Hono (server/src/routes.ts, 127.0.0.1 에만 바인딩)
                      └─ repo.ts → SQLite server/data/office.db
                 ◀─ SSE GET /api/events (변경 알림 · Last-Event-ID 재생 · 10초 ping)
데모 봇 (server/src/bot) ─ HTTP + X-Actor 헤더 ─▶ 같은 API
```

- 3D는 lazy 로딩이고 HUD가 먼저 뜬다. `npm run build` 뒤 `scripts/check-bundle.mjs`가 첫 HTML이 three · r3f 청크를 바로 부르면 빌드를 실패시킨다 — 코드상 NFR-02("상단 바와 패널이 3D보다 먼저")를 지키는 구조다(실행 확인 전).
- 1024px 반응형 레이아웃이 있다(`src/ui/responsive.css`, E2E `11-responsive`) — 코드상 NFR-07과 맞는다(실행 확인 전).

### 확장할 구조 (신규 예정)

```text
GitHub ──(설치 토큰으로 읽기만 · 폴링 · 웹훅 없음)──▶ 서버 adapters/github
                                                        │ GitHub 원본(Github*) → 앱 모델(Task · Submission) 변환
                                                        ▼
                                                  store (SQLite) ── syncedAt · 동기화 상태
                                                        │
브라우저 ◀── SSE 변경 알림 ── Hono api (인증 · 역할 검사) ◀── DataSource(api) ── 3D 화면 · HUD
              │
              └─ 앱 자체 기록(요구사항 연결 · 회의 · 안건 · 투표 …)은 인증된 앱 API 로 저장
```

- **GitHub 폴링과 SSE는 다른 일이다.** 폴링은 서버가 GitHub의 변경을 가져오는 방법이고(CON-03, FR-GH-03), SSE는 앱 서버가 브라우저에 "바뀌었다"를 알리는 기존 방법이다. 동기화 결과도 SSE로 브라우저에 알린다. SSE를 없앨 이유는 없다.
- GitHub 웹훅은 쓰지 않는다(CON-03, 기획안 「이번 범위에서 제외」).
- 화면은 GitHub를 직접 부르지 않는다. GitHub 토큰은 서버에만 있다(NFR-04).

## 데이터 소스는 두 층이다

기획안은 "기존 DataSource 구조 위에 GitHub 어댑터를 붙인다"고 했다(CON-05). 실제 코드에서 `DataSource`는 **브라우저 쪽 인터페이스**다. GitHub 토큰은 브라우저로 나갈 수 없으므로(NFR-04) GitHub 어댑터는 **서버 안**에 새로 만들어야 한다.

| 층 | 현재 구현 | 신규 예정 |
| --- | --- | --- |
| 브라우저 `DataSource` (`src/data/DataSource.ts`) | `mock`(localStorage) · `api`(Hono 클라이언트). `?source=api` 쿼리로 고른다 | 이름과 교체 구조를 유지한다. 화면은 계속 이 인터페이스로만 데이터를 받는다 |
| 서버 데이터 원천 | 없음 — 서버는 자기 DB만 안다 | `adapters/github`(실제 GitHub) · fake(시연)를 고르는 서버 쪽 선택. 기획 문서의 `DATA_SOURCE` 환경 변수(`github` · `fake` · `local`)가 이 층이다 |

**정함 (2026-10-28 결정)**: 화면 작업 · 발표 시연의 가짜 데이터는 **기존 브라우저 mock을 새 모델로 확장**해서 쓴다(서버 `DATA_SOURCE=fake`가 아니다). 서버 `DATA_SOURCE`는 동기화 원천 선택에만 쓰는 것이 권장안이다. 기존 mock · api 계약 테스트 구조를 그대로 살릴 수 있다.

### DataSource 인터페이스에서 바꿀 것

현재 메서드(`src/data/DataSource.ts`): 읽기 `load` · `subscribe`, 쓰기 `createTask` · `updateTask` · `deleteTask` · `createMember` · `updateMember` · `deleteMember` · `startMeeting` · `endMeeting`, 선택 `setDemoMode` · `getDemoMode` · `reset`.

| 책임 | 현재 | v1 방향 |
| --- | --- | --- |
| GitHub 원본 읽기 | 없음 | 서버 `adapters/github`가 폴링으로 읽는다(B2) |
| GitHub 원본 → 앱 모델 변환 | 없음 | 서버 `adapters/github`. GitHub 모양은 어댑터 밖으로 내보내지 않는다 |
| 앱 모델 저장 · 동기화 상태 | `repo.ts`(Task · Member · Meeting CRUD) | `store`에 `syncedAt` · 마지막 성공 시각 · 실패 사유와 함께 저장(FR-GH-05) |
| 이슈 · 팀원 만들기 · 고치기 · 지우기 | `createTask` · `updateTask` · `deleteTask` · `createMember` … | **변경 대상** — GitHub에서 오는 값은 앱에서 바꾸지 않는다(CON-02). 이 메서드들은 없애거나 앱 자체 칸(예: 요구사항 연결)만 바꾸는 메서드로 바뀐다 |
| 앱 자체 기록 쓰기 | `startMeeting` · `endMeeting` | 요구사항–이슈 연결, 회의 · 안건, 스탠드업, 투표 등 쓰기 메서드 신규. 시그니처는 아직 정하지 않는다 |
| 화면 표시 | `load` → 스토어 → 장면 · HUD, `subscribe`로 변경 반영 | 유지 |
| 시연 | mock + 데모 봇 | mock을 새 모델로 확장. 데모 봇은 시연용으로 고친다(2026-10-28 결정) — 안건 상태를 앱에서 바꾸지 못하는 것은 그대로 두고, GitHub에서 바꿀 것은 GitHub 화면으로 넘기는 경로를 둔다 |

이 인터페이스에 기대는 곳이 많아 변경 영향이 크다. 실제 TypeScript 인터페이스는 이번 문서 작업에서 바꾸지 않았다.

| 파일 | 지금 기대는 것 | 바꿔야 하는 이유 |
| --- | --- | --- |
| `src/store.ts` | Task · Member CRUD 낙관적 갱신, `endDrag`의 담당 지정 · 상태 변경 · 마감일 변경 · 안건 올리기 | 담당 · 상태 · 마감은 GitHub 원본이라 드래그로 바꿀 수 없다. 드래그 결과는 요구사항 연결 · 안건 올리기로 바뀐다(주간 탭의 드래그로 마감 바꾸기도 뺀다 — 2026-10-28 추가 결정) |
| `src/data/selectors.ts` · `insights.ts` | `estimate`(포인트) · `capacity` · `dueDate`로 업무량 · KPI · 추천 계산 | 포인트 · 용량은 GitHub에 없고, 사람별 비교는 금지다(FR-FLOW-06) |
| `src/scene/slots.ts` | `status` · `assigneeId` · `onAgenda`로 위치 계산 | 검토 영역 · 머지 준비 선반 · 요구사항 구역 위치가 새로 필요하다 |
| `src/ui/MeetingDialogs.tsx` · `src/data/rules/meetings.ts` | 회의 종료 결과로 담당 지정 · 상태 변경 · 백로그로 | GitHub 원본을 바꾸는 결정이라 CON-02와 충돌한다. 결정 기록 · 배운 점 저장으로 바뀐다 |
| `server/src/bot/index.ts` | API로 작업을 만들고 옮긴다 | 위와 같은 이유로 바뀐다 |
| `tests/contract/*` | 위 CRUD 계약 | 계약이 바뀌면 함께 바뀐다 |

## GitHub 원본 상태와 앱 상태

두 종류의 상태를 섞지 않는다. 값 목록은 05-policy.md 「상태값」이 기준이다.

| 종류 | 예 | 누가 바꾸나 | 어디에 |
| --- | --- | --- | --- |
| GitHub 원본 상태 | 이슈 담당자 · 열림 · 닫힘, PR 열림 · 리뷰 요청 · 머지 · 닫힘 | GitHub(사람이 GitHub에서). 앱은 다음 동기화에 반영만 한다 | `adapters/github`가 읽어 앱 모델(`Task` · `Submission`)로 바꾸고 `syncedAt`과 함께 `store`에 둔다 |
| 계산한 값 | 사무실 위치(백로그 · 책상 · 검토 · 완료 선반, 머지 준비 선반), 막힘, 진행률, 팀 지표, 규칙 위반(06 「규칙 위반 (계산)」), PR 투표 상태(투표에서 계산 — 06) | `core`가 원본 상태와 앱 상태에서 계산한다. FR-HAB-11 새 위반 알림처럼 이전 값이 필요하면 저장 여부를 다시 정한다 | `core` |
| 앱 상태 | 요구사항–이슈 연결, 동기화 상태, 회의 안건 상태, PR 투표, 도움 요청 상태 | 앱 사용자 · 앱 규칙 | `store` (앱 서버에만 — CON-02) |

- **지금 데모의 `Task.status`(`todo` · `in_progress` · `review` · `done`)는 앱이 직접 바꾸는 값이다.** v1에서는 GitHub 원본(열림 · 닫힘, 담당자, 연결 PR 상태)에서 계산하는 값으로 바뀐다. 필드별 출처는 06-data.md 「기존 데모 데이터와의 대응」.
- PR이 회의 안건이 되어도 PR 투표 상태는 바뀌지 않는다. 안건은 따로 저장한다(06-data.md 「PR 투표 상태」).
- 원본 상태를 앱이 바꾸는 경로는 없다. 머지 · 이슈 만들기는 GitHub 화면 링크로 넘긴다(P3).

## 서버 API

### 기존 API와 신규 API

| 구분 | 경로 | 상태 | v1에서 |
| --- | --- | --- | --- |
| 상태 확인 | `GET /api/health` | 현재 구현 | 유지 |
| 전체 데이터 | `GET /api/workspace` (`X-Seq` 헤더) | 현재 구현 | 유지하되 응답이 새 모델(이슈 · PR · 요구사항 · 안건 …)로 바뀐다. 역할 · 본인 여부에 따라 필드를 뺀다 |
| 작업 CRUD | `POST /api/tasks` · `PATCH` · `DELETE /api/tasks/:id` | 현재 구현 | 변경 대상 — 이슈는 GitHub에서만 만든다 |
| 팀원 CRUD | `POST /api/members` · `PATCH` · `DELETE /api/members/:id` | 현재 구현 | 변경 대상 — 팀원은 GitHub 레포 참여자에서 온다 |
| 회의 | `POST /api/meetings` · `POST /api/meetings/:id/end` | 현재 구현 | 수정 · 확장 — 안건 · 결정 · 배운 점 기록(FR-MTG-03 · 04) |
| 데모 · 초기화 | `GET` · `PUT /api/demo`, `POST /api/reset` | 현재 구현 | 시연용으로 남길지 결정 필요. 운영 서버에서는 막는다 |
| 변경 알림 | `GET /api/events` (SSE) | 현재 구현 | 유지 |
| 로그인 · 세션 | — | 신규 예정 | GitHub 로그인 시작 · 콜백 · 로그아웃 · 현재 사용자 |
| 조직 · 레포 · 사무실 열기 | — | 신규 예정 | FR-GH-01 · P2 |
| 수동 동기화 · 동기화 상태 | — | 신규 예정 | FR-GH-03 · 05 · 06 |
| 요구사항 · 연결 | — | 신규 예정 | FR-REQ |
| 스탠드업 · 일정판 · 습관 체크 · 회고 리포트 | — | 신규 예정 | FR-MTG · FR-PLAN · FR-HAB · FR-REP |
| 투표 · 안건 · 도움 요청 | — | 신규 예정 | FR-LRN (도움 요청은 FR-LRN-15~20 — 2026-10-28 추가 결정) |

경로 이름은 아직 정하지 않는다. 기존 계약은 데모의 `docs/API.md`에 있지만 실제 코드와 다른 곳이 있다 — PROJECT_COMMANDS.md 「데모 docs/API.md와 코드의 차이」.

### GitHub 읽기 전용 원칙 (CON-02)

CON-02는 **앱이 GitHub의 원본(이슈 · PR · 코멘트 · 라벨 등)을 바꾸지 않는다**는 뜻이다. HTTP 메서드 이름으로 판단하지 않는다.

| 요청 | 허용 | 이유 |
| --- | --- | --- |
| GitHub API로 이슈 · PR · 커밋 · 리뷰 · 라벨 · 참여자 읽기(GET) | 허용 | 읽기 |
| GitHub 로그인의 OAuth 코드 교환(`POST https://github.com/login/oauth/access_token`) | 허용 | 인증 절차. 원본을 바꾸지 않는다 |
| GitHub App 설치 토큰 발급(`POST /app/installations/{id}/access_tokens`) | 허용 | 인증 절차. 원본을 바꾸지 않는다 |
| 이슈 만들기 · 코멘트 · 라벨 붙이기 · 머지 · 상태 변경 등 GitHub 원본을 바꾸는 호출 | **금지** | CON-02. 할 일은 GitHub 화면 링크로 넘긴다(P3) |
| 브라우저 → 앱 서버 API의 `POST` · `PATCH` · `PUT` · `DELETE` | 허용 | 앱 자체 기록(요구사항 연결 · 회의 · 투표 등)을 앱 서버에 저장한다 |

GitHub App 권한을 Metadata · Contents · Issues · Pull requests 모두 **읽기 전용**으로 두면, 실수로 쓰기 호출을 만들어도 GitHub가 거부한다.

### 에러 응답 형식

데모 모양 `{ "error": { "code", "message" } }`를 그대로 쓰고, 새 code도 소문자 snake로 더한다(2026-10-28 결정). 클라이언트 `src/data/api/index.ts`의 `toError`(`not_found`면 로컬에서 지우고 `unavailable`이면 SSE 재연결), `DataError` 타입, 계약 테스트가 이 모양에 기댄다.

- 기존: `invalid`(400 · 위생 검사 실패는 403) · `not_found`(404) · `conflict`(409) · `unavailable`(503)
- 신규: `unauthorized` · `forbidden_role` · `github_unauthorized` · `github_rate_limited` — 표는 코드 컨벤션(BE) 「에러 응답」

## 인증과 권한

### 현재 구현

- **로그인 · 세션 · 권한 검사가 없다.** 데모 `docs/API.md`도 "Local-only: no auth"라고 적는다.
- 서버는 요청 위생 미들웨어(`server/src/routes.ts`)만 가진다 — `Host`가 `localhost:<포트>` · `127.0.0.1:<포트>`가 아니면 거부(DNS 리바인딩 방지), `Origin`이 있으면 `http://localhost` · `http://127.0.0.1`만 허용, 쓰기 요청은 `Content-Type: application/json` 필수. 기획안 「시스템 구조」의 "기존 서버의 요청 검사 규칙 유지"(NFR-05)는 이 미들웨어를 가리킨다.
- `X-Actor: <memberId>` 헤더는 데모 봇이 "어느 팀원이 한 일인지"를 표시하는 값이다. 누구나 아무 값이나 보낼 수 있으므로 **인증이 아니다.** 헤더가 없으면 관리자(사용자)로 처리한다.
- 서버는 `127.0.0.1`에만 바인딩한다 — 다른 기기에서 접속할 수 없다.

### 신규 예정

GitHub 로그인과 레포 읽기는 **GitHub App 하나**로 처리한다. OAuth App은 비공개 레포를 읽으려면 쓰기까지 포함된 `repo` 권한이 필요해서 읽기 전용 최소 권한(NFR-04)을 지킬 수 없기 때문이다 — 개발 컨벤션 > 환경 변수 가이드.

| 단계 | 내용 | 상태 |
| --- | --- | --- |
| 로그인 | `GITHUB_APP_CLIENT_ID` · `GITHUB_APP_CLIENT_SECRET`으로 GitHub 로그인을 처리해 "누가 들어왔는지"를 정한다 | 신규 예정 |
| 세션 | 로그인한 사용자를 서명된 쿠키로 기억한다(`SESSION_SECRET`). 세션은 30분~1시간 뒤 만료된다(2026-10-28 결정 — 코드 컨벤션(BE) 「인증」). 로그아웃 처리는 결정 필요 | 신규 예정 |
| 역할 판정 | 팀 레포의 GitHub 권한에서 정한다 — write = 참여자(ROLE-01), admin = 레포 관리자(ROLE-02), `MASTER_ADMIN_GITHUB_LOGIN` = 전체 관리자(ROLE-03) · P1. 역할은 로그인할 때만 확인해 세션에 두고, 요청마다 GitHub에 다시 묻지 않는다(2026-10-28 결정 · 05-policy.md P1). 어느 자격 증명으로 권한을 조회하는지는 결정 필요(BACKEND_ASSIGNMENTS.md B1에서 정한다) | 신규 예정 |
| 요청마다 권한 검사 | API에서 요청마다 역할을 확인한다(FR-AUTH-04). 화면에서 버튼을 숨긴 것을 권한 확인으로 보지 않는다. 클라이언트가 보낸 사용자 값(`X-Actor` 같은 헤더 포함)을 믿지 않는다 | 신규 예정 |
| 설치 토큰 | `GITHUB_APP_ID`와 키 파일(`GITHUB_APP_PRIVATE_KEY_PATH`)로 설치 토큰을 받아 레포를 동기화한다. 토큰은 서버 메모리에서만 쓰고 만료 전에 다시 발급한다. DB 저장 여부는 결정 필요(권장: 저장하지 않음). `adapters/github` 안에서만 쓰고 브라우저로 내보내지 않는다(NFR-04) | 신규 예정 |
| 요청 위생 | 기존 미들웨어를 유지하되, 시범 서버에서는 `APP_ORIGIN`을 허용 출처에 넣고 바인딩 주소를 바꿔야 한다 | 수정 · 확장 |

- GitHub App 권한은 Metadata, Contents, Issues, Pull requests를 모두 **읽기 전용**으로 둔다.
- 레포 등록은 우리 조 조직 안의 레포만 받는다(ASM-02 · P2). 앱이 설치되지 않은 조직은 "승인 요청하기"로 GitHub 요청 화면에 넘긴다.
- 토큰 · 쿠키 · 이메일을 로그에 남기지 않는다. 디버깅이 필요하면 앞 4자리만 남기고 가린다(NFR-04).
- 키 파일(`*.pem`)은 `./secrets/` 에만 둔다. **데모 저장소의 `.gitignore`에는 `.env` · `*.pem` · `secrets/`가 아직 없다** — 개발 저장소에서 먼저 추가한다(환경 변수 가이드).

## 저장소 (SQLite)

- **현재 구현**: `server/src/db.ts`가 `node:sqlite`로 `server/data/office.db`(gitignore됨)를 연다. WAL 모드, `busy_timeout` 3초. 테이블은 `projects` · `members` · `tasks` · `meetings` · `activity`가 모두 `(id TEXT PRIMARY KEY, data TEXT)` — 엔티티 전체를 JSON 문자열로 저장한다. `meta` 테이블에 `seq` · `nextNumber` · `demo`를 둔다. 경로를 바꾸는 환경 변수는 없다.
- 모든 쓰기는 `repo.ts`의 `mutate()`가 트랜잭션(`BEGIN IMMEDIATE`)으로 감싸고, 커밋 뒤에 SSE로 알린다.
- 마이그레이션 도구가 없다. `CREATE TABLE IF NOT EXISTS`와 시작 시 `repo.migrate()`로 옛 데이터를 고친다.
- **신규 예정**: 06-data.md 「저장 항목」(팀 설정, 동기화 상태, GitHub 원본에서 바꾼 앱 모델, 요구사항–이슈 연결, 스탠드업, 회의, 회의 안건, 컨벤션 규칙, 회고 리포트, 채택된 제안 항목)도 같은 JSON 방식으로 더한다(2026-10-28 결정 — 코드 컨벤션(BE) 「저장」). 저장 방식을 바꾸면 DB schema 변경이므로 PR에 위험 범위로 적는다.

## 기존 데모와의 대응

> **구현 스택은 아직 정하지 않았다.** 아래 표의 데모 경로 · 파일 · 명령은 데모 스택(React · TypeScript · Three.js · Hono)을 이어 쓸 때의 기준이다. HTML · CSS · JavaScript 위주로 새로 구현하게 되면 같은 내용을 "참고할 동작과 구조"로 읽는다 — ARCHITECTURE.md 「기술 스택」.

### 3D 장면

| 기능 | 기존 파일 | 판정 | 필요한 수정 | 관련 요구사항 |
| --- | --- | --- | --- | --- |
| 사무실 공간 · 벽 · 바닥 · 조명 | `src/scene/Room.tsx` · `Lighting.tsx` · `Furniture.tsx` · `src/office/layout.ts` | 데모와 같음 | 요구사항 구역을 그릴 바닥 영역을 `layout.ts`에 구현한다 | FR-OFF-01 |
| 책상 · 아바타 | `Desks.tsx` · `Avatar.tsx` · `Avatars.tsx` | 수정 · 확장 | 책상은 10개 고정(`MAX_MEMBERS = 10`)이라 4인 팀에 충분하다. 팀원 → 책상 배정은 GitHub 참여자 기준으로 자동 배정. GitHub 프로필 사진 표시(FR-OFF-06) 신규. 머리 위 신호 다이아 · "업무량 140%" 태그는 사람별 비교라 제거 대상 | FR-OFF-01 · 06, FR-FLOW-06 |
| 작업 상자 | `TaskBox.tsx` · `TaskBoxes.tsx` · `TaskTooltip.tsx` | 수정 · 확장 | 둥근 상자 한 종류뿐이다. 높이 = 포인트(`estimate`), 색 = 우선순위 · 프로젝트 · 마감 위험이다. 도형 색은 우선순위로 구분한다. 우선순위 · 포인트는 GitHub 이슈에 없어 작업 필드를 새로 기획 예정이다(2026-10-28 추가 결정 · P6) | FR-OFF-02 · 04 |
| PR 도형(삼각기둥) | 없음 | 신규 | `shared.tsx`의 기본 도형 패턴으로 만든다. 새 3D 모델링은 하지 않는다 | FR-OFF-02, FR-LRN-03 |
| 안건 도형 5종(상자 · 삼각기둥 · 구 · 팔면체 · 납작한 메모) | 없음 — 상자 1종만 있다 | 신규 | 이슈 상자를 빼면 4종이 새 도형이다. 도형 5종이 이미 있다고 가정하지 않는다 | FR-OFF-02, FR-LRN-10 |
| 위치 배치 | `slots.ts` (`home`: `desk` · `backlog` · `done` · `agenda`) | 수정 · 확장 | 검토 영역(review 위치가 지금 없다 — 책상에서 할 일과 같게 보인다), 머지 준비 선반, 요구사항 구역 위치를 더한다. 완료 선반은 최근 16개만 그린다. 과부하 책상 기울기(`wobble`)는 제거 대상 | FR-OFF-02 · 03, FR-FLOW-06 |
| 백로그 · 완료 선반 | `Furniture.tsx`(`BacklogShelf` · `DoneShelf`) · `slots.ts` | 데모와 같음 | 백로그 = 담당자 없는 열린 이슈, 완료 = 닫힌 이슈 · 머지된 PR로 기준만 바꾼다 | FR-OFF-02 |
| 회의실 | `MeetingRoomState.tsx` · `Room.tsx` | 수정 · 확장 | 회의 중 표시 · 안건 수 라벨 · 드롭 강조는 데모와 같게 구현한다. 안건은 지금 `Task.onAgenda` 하나뿐이라 종류가 없다 → `AgendaItem`으로 바꾸고 5종 도형을 놓는다. 안건 노출은 21개까지 | FR-MTG-03 · 04, FR-LRN-04 · 10 |
| 요구사항 구역 · 연결 | 없음 | 신규 | 바닥 구역 · 구역별 진행률 · 상자를 구역으로 드래그(ROLE-02) | FR-REQ-02 · 04 · 05, FR-OFF-03 |
| 막힘 · 리뷰 대기 표시 | 비슷한 것: `TaskBox`의 지연 깜빡임, `Overlays.tsx`의 업무량 원판 | 수정 · 확장 | 막힘은 강조하고 "막힘" 표시만 붙인다 — 이유 문구 없음(FR-FLOW-02 · 2026-10-29 결정). 업무량 원판(사람별)은 제거 대상 | FR-FLOW-01 · 02 |
| PR 투표 · 머지 준비 | 없음 | 신규 | 삼각기둥 위 찬성 · 반대 수, 머지 준비 선반 | FR-LRN-01~03 |
| 공용 게시 공간 | 없음 | 신규 | 회의 결정 기록 · 팀 규칙 · 도움 요청 · 배운 점 (공지 없음 — 2026-10-28 결정) | FR-MTG-04, 추가 제안 후보 |
| 정보 뷰(최근 변화 · 담당 · 막힘 — 2026-10-29 결정) | 비슷한 것: `TopBar`의 `OverlaySwitch`(기본 · 업무량 · 마감 위험 · 프로젝트)와 `Overlays.tsx` | 수정 · 확장 | 오버레이 전환 구조는 데모를 참고하고, 정보 뷰 내용은 새로 구현한다. 업무량 오버레이는 제거 대상 | 기획안 「정보 뷰」 |
| 카메라 회전 · 줌 · 이동 | `CameraRig.tsx` | 데모와 같음 | — | FR-OFF-01 |
| 도형 드래그 | `DragLayer.tsx` · `dropTarget.ts` · `store.ts` `endDrag` | 수정 · 확장 | 드롭 대상이 지금은 팀원(담당 지정) · 백로그 · 상태 열 · 회의실 · 날짜다. 담당 · 상태 · 마감은 GitHub 원본이라 바꿀 수 없으므로 요구사항 구역(연결)과 회의실(안건)만 남긴다(날짜 드롭 = 마감 바꾸기도 뺀다 — 2026-10-28 추가 결정). 완료 상자는 드래그가 막혀 있다 | FR-REQ-04, FR-LRN-04 |
| 상세 패널 연동 | `src/ui/Detail.tsx` · `RightPanel.tsx` | 수정 · 확장 | 선택 → 패널 구조는 데모를 참고해 구현한다. 편집 입력란(상태 · 우선순위 · 포인트 · 담당 · 마감)은 읽기 전용 + GitHub 링크로 바꾼다 | FR-OFF-05 |
| 위치 이동 애니메이션 | `TaskBox.tsx`(호 그리며 이동 · 등장 · 흔들림) | 데모와 같음 | 흔들림(과부하)만 제거 | FR-OFF-02 |

### 화면 (HUD)

화면별 대응은 07-screens.md 「기존 데모 화면과의 대응」, 담당과 수정 규모는 SCREEN_ASSIGNMENTS.md를 본다.

### 사람별 비교 표현 — 제거 대상

FR-FLOW-06 · P6("팀원 간 순위 · 비교와 사람별 합계 금지")와 충돌한다. v1에서는 빼거나 본인에게만 보이게 바꾼다. 주간 탭(`WeekView`)과 칸반 · 목록 탭(`KanbanView` · `ListView`)은 화면 모양(팀원 × 날짜 그리드 · 탭)만 유지하고, 아래 포인트 표현과 편집 기능(드래그로 마감 바꾸기, 상태 · 담당 편집)은 뺀다(2026-10-28 추가 결정).

| 위치 | 지금 보이는 것 |
| --- | --- |
| `src/ui/TopBar.tsx` 균형 칩 · `src/data/selectors.ts` `computeKpis` | 업무량 쏠림 정도와 "가장 바쁜 팀원" 이름 |
| `src/ui/TeamView.tsx` `WorkloadBar` | 팀원별 `포인트/용량` 막대와 여유 · 과부하 등급을 나란히 |
| `src/ui/WeekView.tsx` | 팀원별 주간 포인트 막대, 칸별 포인트 합계 |
| `src/ui/Detail.tsx` `Vitals` | 팀원별 업무량 % · 마감 압박 · 집중도 |
| `src/data/insights.ts` `memberSignal` → `src/scene/Avatar.tsx` | 아바타 위 신호 다이아와 "업무량 140%" 태그 |
| `src/data/insights.ts` `suggestActions` → `src/ui/Suggestions.tsx` | "{이름} 님 업무량 130%", 가장 한가한 팀원에게 넘기라는 추천 |
| `src/scene/Overlays.tsx` `WorkloadDiscs` · `slots.ts` `wobble` | 책상별 업무량 원판, 과부하 책상 기울기 |
| `src/ui/Dialogs.tsx` `MemberDialog` | 팀원별 주간 용량(포인트) 입력 |

## 개발 경로

기획안 「2주 개발 일정」의 순서와 우선순위를 바꾸지 않는다. 기존 데모에서 출발할 때의 기술적 순서다.

| 단계 | 목표 | 주로 바뀌는 곳 | 선행 | 규모 메모 |
| --- | --- | --- | --- | --- |
| 1차 기존 데모 유지 (1~2일차) | 설치 · 실행 확인, mock으로 3D 사무실 표시, 컴포넌트 · 데이터 구조 파악, 데모에서 참고할 범위와 구현 스택(① · ②) 확정 | 코드 변경 없음. 실행 결과 · 결정 사항 기록 | 개발 저장소 확정, Node 버전 | 4명이 함께 — 기획안 「역할 분담」 |
| 2차 GitHub 이슈를 사무실에 (5일차 첫 목표) | GitHub 연결, 이슈 읽기, 앱 모델로 변환, 기존 상자로 표시, 담당자 · 상태에 따른 위치 | 서버 `auth` · `adapters/github` · `store` 신규, `types.ts` · `slots.ts` · `store.ts` 수정 | 1차, GitHub App 생성, Task 필드 출처 결정(06) | 가장 큰 단계. 로그인 없이 설치 토큰만으로 먼저 이슈를 띄우고 로그인을 나중에 붙이는 순서도 가능하다 — 결정 필요 |
| 3차 기획 핵심 확장 | 요구사항 구역과 진행률, PR 시각화, 회의실 안건 · 결정 기록, 회고 리포트, PR 투표 | `core`(`src/data/rules/`) · 장면 도형 · 회의 화면 · 새 패널 | 2차, 명세 문서 형식(TBD-01 — ## 제목만, 2026-10-28 결정) | 7일차 데모의 PR 투표(FR-LRN-01~03)는 2026-10-28에 채택되어 일정에 든다 |

## 공용 컴포넌트

여러 화면이 쓰는 화면 조각이다. 3D 표현은 기존 3D 팀 오피스의 표현을 참고해 구현하고 새 모델링은 하지 않는다(기획안 「기능 동결 규칙」). 기존 경로는 데모 기준이다.

### 상단 바

기존 `src/ui/TopBar.tsx`. 레포 드롭다운, D-day, 요구사항 진행률, 열린 PR 수, 평균 리뷰 대기, 동기화 상태, "기록되지 않은 기여는 반영되지 않습니다" 안내 — 07-screens.md 「상단 바」. 지금은 KPI 칩 · 균형 칩 · 회의 버튼 · 오버레이 전환이 있고, 레포 선택 · D-day · 동기화 상태는 없다(신규). 3D보다 먼저 뜬다(NFR-02 — 이미 lazy 로딩).

### 상세 패널

기존 `src/ui/Detail.tsx`(`RightPanel.tsx`의 서랍). 도형 · 아바타를 선택하면 제목, 담당자, 연결 PR, 리뷰 상태, 찬성·반대와 이유, 투표 버튼, GitHub 링크를 보여 준다 — 07-screens.md 「상세 패널」 · FR-OFF-05.

### 도형 5종

이슈는 정육면체 상자, PR은 삼각기둥, 도움 요청은 구, 배운 점 공유는 팔면체(보석 모양), 기타는 납작한 메모지다. 2D 팀 보드 아이콘은 ■ ▲ ● ◆ 모서리 접힌 메모로 1:1 대응한다. 도형은 종류만 나타내고, 상태는 도형을 바꾸지 않고 배지 · 테두리 · 크기로 표시한다(기획안 「안건 도형과 상태 표시」). **지금 있는 것은 상자(`src/scene/TaskBox.tsx`) 하나뿐이다.** 공유 도형 · 재질 캐시는 `src/scene/shared.tsx`에 있다.

### 정보 뷰

최근 변화 · 담당 · 막힘 뷰 세 가지를 같은 사무실 위에 겹쳐 보여 준다(2026-10-29 결정). 최근 변화 뷰는 가장 최근 스탠드업 날 이후 상태가 바뀐 도형을 밝게, 그대로인 도형을 흐리게 보이고(요구사항 ID 없음), 담당 뷰는 몰린 사람 책상과 담당자 없는 도형을, 막힘 뷰는 막힌 도형 · 아바타를 강조한다. 담당 · 막힘 표시에는 이유 문구와 숫자 비교가 없다. 요구사항 진행률 · 빈 구역은 상단 바와 요구사항 패널, 추천 리뷰어는 상세 패널, 마감 · 뒤처짐은 일정판에서 본다. 일정상 밀리면 패널 목록으로 대신한다(기획안 「정보 뷰」 · 「기능 동결 규칙」). 기존 오버레이 전환(`TopBar.tsx` `OverlaySwitch`, `store.ts`의 `overlay`, `src/scene/Overlays.tsx`)을 틀로 쓴다.

## 디자인 토큰

기존 값은 세 곳에 있다. v1에서 쓸 토큰은 이 파일들을 기준으로 정리한다.

- `src/theme.ts` `PALETTE` — 3D 색(배경 · 바닥 · 벽 · 가구 · 강조색 `accent` · 선택 · 드롭 가능 · 지연 강조). 공간은 흰색 계열이고 색은 데이터에만 쓴다는 원칙이 주석에 있다.
- `src/data/meta.ts` — 상태 · 우선순위 · 접속 상태 라벨과 색, 팀원 색 목록 `MEMBER_COLORS`, 업무량 등급 `WORKLOAD_LEVELS`(사람별 비교라 제거 대상).
- `src/index.css`의 CSS 변수와 `src/ui/*.css` — HUD 색 · 레이아웃.

### 색

색은 사람이 아니라 **우선순위**에 부여한다 — 화면이 사람 비교처럼 보이지 않게 하기 위해서다(P6). 우선순위의 출처는 작업 필드 새로 기획 예정이다. 요구사항 구역별 색은 두지 않는다(2026-10-28 추가 결정 — FR-OFF-03과 다름). 안건 종류는 색으로 구분하지 않는다(도형으로 구분). 지금 데모는 상자 색을 우선순위 · 프로젝트에, 아바타 색을 팀원(`Member.color`)에 준다 — 아바타 색을 팀원 식별용으로 남길지는 결정 필요.

### 글자 크기

TODO: 대응 내용 없음 (삭제 여부 확인)

### 모서리 · 그림자

TODO: 대응 내용 없음 (삭제 여부 확인)

### 간격

TODO: 대응 내용 없음 (삭제 여부 확인)

### 한 번만 쓰는 값

TODO: 대응 내용 없음 (삭제 여부 확인)

## 렌더링 방식

브라우저에서 모두 그리는 SPA다(Vite + React, 서버 렌더링 없음). 서버(Hono)는 `/api/*`만 응답하고 정적 파일을 서빙하지 않는다 — 시범 서버(팀원 컴퓨터 한 대 — TBD-09, 2026-10-28 결정)에서 빌드 결과(`dist/`)를 어떻게 서빙할지는 결정 필요. GitHub 토큰과 비밀 값은 서버에만 두고 브라우저로 내보내지 않는다(NFR-04). 상단 바와 패널이 3D보다 먼저 뜨고 3D는 이어서 로딩한다(NFR-02).

## 이미지

TODO: 대응 내용 없음 (삭제 여부 확인)

## 새 화면 만들기

기존 데모의 흐름을 따른다. 화면 목록과 보이는 것은 07-screens.md, 담당은 [SCREEN_ASSIGNMENTS.md](./SCREEN_ASSIGNMENTS.md)를 따른다.

1. 필요한 데이터가 `src/data/types.ts`에 있는지 본다. 없으면 타입과 DataSource 변경을 먼저 합의한다(계약 파일).
2. 계산은 `src/data/selectors.ts` · `src/data/rules/`에 순수 함수로 둔다. 화면에서 직접 계산하지 않는다.
3. 화면 상태는 `src/store.ts`(데이터 · 선택 · 드래그)와 `src/ui/uiState.ts`(탭 · 패널 같은 UI 전용 값) 중 맞는 곳에 둔다.
4. HUD는 `src/ui/`, 3D는 `src/scene/`에 만든다. 3D 쪽은 첫 화면 번들에 들어가지 않게 lazy 경로 안에 둔다(`npm run build`의 번들 검사).
5. mock(`?source=` 없이)과 api(`?source=api`) 두 경로에서 확인하고, 필요하면 E2E에 시나리오를 더한다.

## 기획 문서와 다르게 구현했다면

TODO: 대응 내용 없음 (삭제 여부 확인) — 구현이 기획 문서와 달라졌을 때 어디에 어떻게 기록할지 기준 문서에 정한 바가 없다. 정해지면 이 절에 쓴다.

## 플랫폼 제약

v1은 **웹 서비스**이고 **1024px 이상 노트북 · 태블릿**에서 쓴다(NFR-07). 모바일 반응형은 범위 밖이다.

- GitHub 변경은 웹훅 없이 **폴링**으로 받는다(CON-03). 그래서 내부망 서버도 가능하다(기획안 「리스크 및 대응 방안」, TBD-09).
- 연결이 끊겨도 마지막 데이터로 화면을 유지한다(NFR-09). 데모에는 이미 오프라인 배너와 재연결(`src/ui/Toasts.tsx` `ConnectionBanner`, SSE 재연결 1~10초 backoff)이 있다.

## 함수 리전

TODO: 대응 내용 없음 (삭제 여부 확인) — 서버 위치는 팀원 컴퓨터 한 대(같은 네트워크)로 정했다(TBD-09, 2026-10-28 결정).

## 아직 없는 것

- **개발 저장소** — 새로 만들기로 했고 아직 만들지 않았다. 만든 뒤 Git 전략 「8」 설정을 한다.
- **실행 확인** — 데모의 설치 · 실행 · 테스트를 이 문서 작성 중에 돌리지 않았다. 1차 단계에서 확인한다(PROJECT_COMMANDS.md).
- **정의서 v0.1 원문** — 범위로만 인용된 요구사항과 6장 데이터 요구사항(03 · 06 머리말).
- **미결 TBD** — 2026-10-28 결정 회의에서 TBD-01 · 04 · 05 · 06 · 09를 정했다. 남은 [?]는 TBD-02(공개 범위)다(05-policy.md 서비스 정책). 막힘 기준은 리뷰 대기 48시간 · 커밋 없음 48시간으로 정했다(2026-10-28 추가 결정). 그 밖의 결정 필요 항목(이 문서의 결정 필요 항목 등)은 PRD.md 「열린 질문」.
- **제안 채택 여부** — 투표 판정, 회의 안건 종류, 도움 요청, 카드 조립은 2026-10-28에 채택했다. 도움 요청은 FR-LRN-15~20(안건 후보 시간 48시간)이 됐고, 활동 기록(FR-ACT)도 채택했다(2026-10-28 추가 결정).
