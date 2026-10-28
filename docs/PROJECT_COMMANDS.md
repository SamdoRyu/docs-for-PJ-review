# Project Commands

개발에 쓰는 명령 전부다. `.claude/rules/testing.md` 가 이 문서를 가리킨다.

**두 저장소를 구분한다.** 이 저장소(`docs-for-PJ-review`)는 문서뿐이라 실행할 명령이 없다. 아래 명령은 모두 **기존 3D 데모 저장소(`SamdoRyu/warehouseDemo_fork`) 루트**에서 실행한다(CON-05). 실제 개발 저장소를 그 fork로 할지 새로 옮길지는 결정 필요다(ARCHITECTURE.md 「두 저장소」).

> **확인 범위**: 명령과 동작은 2026-10-28에 데모의 `package.json` · 설정 · 소스를 읽어서 정리했다. **이 문서를 쓰면서 설치 · 실행 · 테스트를 돌리지 않았다** — 통과 여부는 모른다. 1~2일차(ARCHITECTURE.md 「개발 경로」 1차)에 실제로 돌려 보고 이 문서를 고친다.

## 처음 받았을 때

```bash
git clone https://github.com/SamdoRyu/warehouseDemo_fork.git   # 개발 저장소가 정해지면 주소를 바꾼다
cd warehouseDemo_fork
npm install          # package-lock.json 이 있으므로 npm 을 쓴다 (CI 라면 npm ci)
npm run dev:all      # 화면(Vite :5173) + API 서버(:8787) 함께
```

→ 화면 `http://localhost:5173` (브라우저 데이터) · `http://localhost:5173/?source=api` (서버 데이터)

- **Node 22.13 이상**이 필요하다(`node:sqlite`를 플래그 없이 쓰고, concurrently · vite가 22 이상을 요구). 저장소에 버전 지정이 없으므로 Node 24 LTS로 맞추는 것을 권장한다 — 결정 필요.
- 지금은 **환경 변수 파일 없이 뜬다.** GitHub 연동을 만든 뒤에는 서버가 필수 값을 검사하게 된다 — 아래 「환경 변수」.
- 화면 작업은 GitHub 없이 한다(브라우저 mock 또는 서버 데이터). 연동 작업과 시범 운영만 실제 GitHub App을 쓴다.

## 명령

모두 데모 저장소 루트에서 실행한다. "그대로 사용"은 지금 코드에서 쓸 수 있는지, "바뀔 점"은 v1 기능 때문에 손봐야 하는 것이다.

| 명령 | 실제로 하는 일 | 선행 조건 · 의존 | 그대로 사용 | v1 때문에 바뀔 점 |
| --- | --- | --- | --- | --- |
| `npm run dev` | `vite` — 화면 개발 서버 `:5173`. `/api` 요청을 `127.0.0.1:${API_PORT ?? 8787}`로 넘긴다 | `npm install`. 서버 데이터를 보려면 API 서버가 따로 떠 있어야 한다 | 가능 | 없음 |
| `npm run server` | `tsx watch server/src/index.ts` — Hono API 서버 `127.0.0.1:${PORT ?? 8787}`, 파일이 바뀌면 다시 시작. 빈 DB면 시드 데이터를 넣는다 | Node 22.13+. DB 파일 `server/data/office.db`가 자동 생성된다 | 가능 | GitHub 연동 뒤에는 환경 변수 필수 검사가 들어간다 |
| `npm run server:once` | 위와 같고 감시만 하지 않는다 | 위와 같음 | 가능 | 위와 같음 |
| `npm run dev:all` | `concurrently`로 `dev` + `server`를 함께 띄운다(하나가 죽으면 둘 다 종료) | 위 둘 | 가능 | 없음 |
| `npm run build` | `tsc -b && vite build` — 모든 TS 프로젝트 타입 검사 후 `dist/` 빌드. 이어서 `postbuild`가 `scripts/check-bundle.mjs`로 첫 화면이 3D 청크를 바로 부르지 않는지 검사(어기면 실패) | `npm install` | 가능 | 없음. 3D 코드를 첫 화면 번들에 넣으면 실패한다(NFR-02) |
| `npx tsc -b` | 타입 검사만(빌드 없음). `tsconfig.json`이 화면 · 서버 · 도구 프로젝트를 참조한다 | `npm install` | 가능 | 없음 |
| `npm run lint` | `oxlint` — `.oxlintrc.json` 규칙(react · typescript · oxc, `rules-of-hooks` error) | `npm install` | 가능 | 없음. 포매터는 없다 |
| `npm run test:contract` | `tsx --test tests/contract/*.test.ts` — Node 내장 테스트 러너. DataSource 계약을 mock과 API(메모리 DB 서버를 테스트 안에서 띄움) 양쪽에 똑같이 돌리고, 순수 규칙(`rules.test.ts`) · 클라이언트(`client.test.ts`)도 검사 | `npm install`. **따로 띄운 서버가 필요 없다** | 가능 | DataSource 계약(Task · Member CRUD)이 바뀌므로 시나리오(`tests/contract/scenarios.ts`)를 함께 바꾼다 |
| `npm run test:e2e` | `playwright test` — 모든 시나리오를 mock(`/`)과 api(`/?source=api`) 두 번씩. 테스트가 직접 Vite `:5180` · API `:8790`(메모리 DB)을 띄우고, 일부는 `:5181` · `:8791`을 더 쓴다 | **Google Chrome이 설치돼 있어야 한다**(`channel: 'chrome'`, 브라우저를 내려받지 않음). 위 포트가 비어 있어야 한다. 이미 떠 있는 서버를 재사용하지 않는다 | 가능 | 작업 CRUD · 드래그로 담당 지정 · 주간 계획 같은 시나리오(`02` · `03` · `04` · `07` · `08`)는 v1 동작과 달라져 고치거나 뺀다 |
| `npm run preview` | `vite preview` — 빌드 결과 미리 보기 | `npm run build` | 가능 | 없음 |
| `npx tsx scripts/data-smoke.ts` | mock 데이터 소스의 불변 조건(시드 · CRUD · 저장 · 데모 · 탭 간 동기화 등) 스모크 검사 | `npm install` | 가능 | mock 모델이 바뀌면 함께 바뀐다 |

## DB

API 서버는 Node 내장 `node:sqlite`로 SQLite 파일 하나를 쓴다. 저장 항목은 [06-data.md](./06-data.md), 저장 방식은 ARCHITECTURE.md 「저장소 (SQLite)」.

### 1. 로컬 DB

따로 띄울 DB 서버가 없다. `npm run server`가 처음 뜰 때 `server/data/office.db`(gitignore됨)를 만들고 시드 데이터를 넣는다. 처음부터 다시 하려면 서버를 끄고 `server/data/`를 지우거나, 화면 메뉴 「데이터 초기화」(`POST /api/reset`)를 쓴다.

### 2. 환경 변수

목록과 결정 필요 항목은 [환경 변수 가이드](./plan/환경%20변수%20가이드.md)가 기준이다. 요약:

- **지금 데모가 읽는 것**: `PORT`(API 포트, 기본 8787), `API_PORT`(Vite 프록시 대상, 기본 8787), `DEMO`(`1`이면 서버 데모 봇). 화면의 데이터 소스는 환경 변수가 아니라 주소의 `?source=api`로 고른다.
- **GitHub 연동 때 새로 도입**: `APP_ORIGIN`, `SESSION_SECRET`, `GITHUB_APP_*` 4종, `MASTER_ADMIN_GITHUB_LOGIN`, `SYNC_INTERVAL_MINUTES`, `LOG_LEVEL`. `DATA_SOURCE` · DB 경로 변수는 결정 필요.
- `.env.example`은 아직 없다. 개발 저장소 루트에 만든다(이름만, 비밀 값 없음). 그 전에 `.gitignore`에 `.env`, `*.pem`, `secrets/`를 먼저 넣는다 — 데모 `.gitignore`에는 `*.local`만 있다.
- **값을 터미널에 찍거나 Issue · PR · 댓글 · 채팅에 붙이지 않는다.** 확인은 "설정됨 / 미설정" 까지만 한다.
- **빈 값(`KEY=`)은 미설정과 같다.**
- 브라우저로 나가는 변수에는 비밀 값을 넣지 않는다.
- 막힘 기준 시간처럼 팀이 화면에서 조정하는 값은 환경 변수가 아니라 팩과 레포 설정에 둔다.

### 3. 스키마 적용

별도 명령이 없다. 서버가 시작할 때 `server/src/db.ts`가 `CREATE TABLE IF NOT EXISTS`로 테이블을 만든다.

### migration 정책

지금은 마이그레이션 도구가 없고, 시작 시 `repo.migrate()`가 옛 데이터를 고친다(예: 옛 작업에 `onAgenda` 칸 추가). v1에서 저장 항목을 더할 때 이 방식을 이어 갈지, 도구를 들일지는 결정 필요다. 생성된 파일은 사람이 수동 편집하지 않는다(`.claude/rules/code-quality.md`).

### production 을 바꾸기 전 — backup / rollback

TODO: 대응 내용 없음 (삭제 여부 확인) — 시범 서버 위치(TBD-09)가 정해진 뒤 쓴다. SQLite 파일 하나라 백업은 파일 복사가 될 가능성이 높지만, WAL 모드라 서버를 멈춘 뒤 복사해야 하는지 확인이 필요하다.

#### 보호 장치

TODO: 대응 내용 없음 (삭제 여부 확인)

#### 적용 전

TODO: 대응 내용 없음 (삭제 여부 확인)

#### 되돌려야 할 때

TODO: 대응 내용 없음 (삭제 여부 확인)

#### 복구가 정말 필요하면

TODO: 대응 내용 없음 (삭제 여부 확인)

#### 하지 않는 것

- 접속 문자열 · 토큰 · 키 파일 내용을 Issue · PR · 댓글 · 채팅에 붙이지 않는다. **「설정됨 / 미설정」까지만** 적는다(환경 변수 가이드).
- 시범 서버의 GitHub App은 개발용과 따로 만든다. 운영용 값은 서버에만 둔다.
- 시범 서버에서 `POST /api/reset` · 데모 봇을 켜 두지 않는다(실제 기록이 지워진다).

## PR 전에 반드시

```bash
npm run lint
npm run build            # tsc -b 타입 검사 + 빌드 + 번들 검사
npm run test:contract
npm run test:e2e         # 화면 · 3D 를 바꿨을 때. Chrome 필요
```

- 실제로 돌린 명령과 결과만 PR 「검증」에 적는다. 못 돌린 것은 못 돌렸다고 적는다.
- **CI**: 데모 저장소에는 `.github/workflows/`가 없다. 이 문서 저장소의 `.github/workflows/ci.yml`은 명령이 정해지기 전에 만든 placeholder(`echo` 한 줄)라 아무것도 검증하지 않는다. 개발 저장소에 CI를 넣을 때 위 명령(e2e 제외 또는 Chrome 설치 포함)으로 채운다 — Git 전략 「9」 GitHub 설정.

> **검증 한계 (CI가 채워지기 전까지)**: CI 초록 표시를 검증으로 세지 않는다. Git 전략 「7. 코드 리뷰 및 머지 규칙」대로 리뷰어가 PR 본문 「검증」에 적힌 실제 명령과 결과를 직접 확인한다.

## 테스트가 덮는 범위

| 테스트 | 덮는 것 | 위치 |
| --- | --- | --- |
| 규칙 단위 | 순수 규칙(작업 패치 적용 · 팀원 제거 계획 · 회의 시작/종료 계획 · 데모 봇 계획) | `tests/contract/rules.test.ts` |
| 계약 | 같은 시나리오 약 25개를 mock과 API에 똑같이 — 작업 생애, 검증 메시지, 팀원 · 책상, 안건, 회의, 초기화 | `tests/contract/scenarios.ts` + `mock.test.ts` · `api.test.ts` |
| API 전용 | `X-Seq`, 에러 본문 모양, SSE 다중 클라이언트 · 재생 · 스냅샷, 봇 409, 데모 저장, 요청 위생(Host · Origin · Content-Type) | `tests/contract/api.test.ts` |
| 클라이언트 | 에러 변환, SSE 재연결 · 백오프, 늦게 온 응답 무시 | `tests/contract/client.test.ts` |
| E2E | 로딩 · KPI, 작업 CRUD, 칸반 드래그, 패널 · 3D 담당 지정, 3D 상자 드래그, 안건 · 회의, 주간 계획, 추천, 오프라인 배너, 실시간 동기화, 반응형(1024×768), 터치 | `tests/e2e/01`~`12` |

v1의 테스트 대상은 **core 의 순수 규칙**이다 — 막힘 계산, 컨벤션 검사, [제안] 투표 판정, [제안] 기술 결정 카드 조립처럼 외부 서비스 없이 값만으로 판정할 수 있는 것(코드 컨벤션 BE 「모듈 구조」). 새 규칙은 `src/data/rules/`에 두고 `rules.test.ts`처럼 테스트한다. 화면 시연과 테스트는 **fake** 로 하고(권장안은 브라우저 mock — ARCHITECTURE.md 「데이터 소스는 두 층이다」의 결정 필요) GitHub API 를 부르지 않는다. GitHub 어댑터의 변환은 GitHub 응답 예시 데이터로 테스트한다(신규 예정).

PR 에 "테스트 완료"라고만 적지 않는다. 실제로 한 것만 적고, 테스트하지 못한 부분은 PR 에 명시한다(`.claude/rules/testing.md`).

## 데모 docs/API.md와 코드의 차이

데모 저장소의 `docs/API.md`(API 계약)는 대부분 코드와 맞지만 아래가 다르다. 코드가 기준이다.

| API.md | 실제 코드 |
| --- | --- |
| 예상하지 못한 오류는 `500 unavailable` | 500을 쓰지 않는다. `DataError`가 아닌 오류는 모두 `503 unavailable` |
| 요청 위생 실패 403이 에러 표에 없음 | `403 invalid`로 응답한다(본문 설명에만 있음) |
| 클라이언트 `src/data/api/ApiDataSource.ts` | 없는 파일. 실제는 `src/data/api/index.ts`(`createApiDataSource`)와 `src/data/api/sse.ts` |
| 시드 `src/data/mock/seed.ts` | 없는 파일. 실제는 `src/data/rules/seed.ts`의 `buildSeed` |
| 조사 함수 `src/data/mock/text.ts` | 없는 파일. 실제는 `src/data/rules/messages.ts` |
| 서버 `http://localhost:8787`, `startServer({ port: 8787 })` | `127.0.0.1`에만 바인딩. `PORT` · `DEMO` 환경 변수를 읽는데 문서에 없다 |

API.md를 고치는 일은 개발 저장소의 작업이다(이번 문서 작업에서는 데모 저장소를 수정하지 않았다).

## 확인할 때 자주 쓰는 것

| 하고 싶은 것 | 방법 |
| --- | --- |
| API가 떠 있는지 | `curl http://127.0.0.1:8787/api/health` → `{"ok":true,"version":1}` |
| 서버 데이터로 화면 보기 | `http://localhost:5173/?source=api` |
| 데모 봇 켜기 | 화면 메뉴 「데모 모드」 또는 `DEMO=1 npm run server` |
| 처음 상태로 | 화면 메뉴 「데이터 초기화」 |

## 검증에 쓰는 것

TODO: 대응 내용 없음 (삭제 여부 확인) — 시범 운영(NFR-01 · TBD-05)과 발표 준비(NFR-08) 단계의 검증 절차가 정해지면 쓴다.

## 주의

- Claude Code 는 `.env` 계열 파일과 `secrets/`, `*.pem` 을 읽지 못하게 막혀 있다(`.claude/settings.json` 의 `deny`). 개발 저장소에도 같은 설정을 둔다.
- GitHub 원본(이슈 · PR · 코멘트 · 라벨)을 바꾸는 GitHub API 요청을 보내는 명령이나 스크립트를 만들지 않는다(CON-02). 로그인 · 설치 토큰 발급 같은 인증 요청은 별개다 — ARCHITECTURE.md 「GitHub 읽기 전용 원칙」.
- E2E는 이미 떠 있는 서버를 재사용하지 않는다. 다른 이유로 5180 · 8790 · 5181 · 8791 포트를 쓰고 있으면 실패한다(개발 서버 5173 · 8787과는 겹치지 않는다).
- API 서버는 `127.0.0.1`에만 뜬다. 같은 네트워크의 다른 기기에서 접속할 수 없다.
- Windows에서 `tsx --test tests/contract/*.test.ts`의 `*`는 셸이 아니라 Node가 펼친다(Node 21+). 실행 확인 전이다.
