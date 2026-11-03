# Project Commands

> **스택은 정했다(2026-11-01 결정)**: 프론트엔드는 데모와 같은 React · TypeScript · Three.js(R3F) · Vite이고, 백엔드는 Spring Boot(Spring MVC · Spring Data JPA · Spring Security)로 새로 만든다. 그래서 이 문서의 **프론트 명령은 데모 저장소 기준으로 그대로 재사용**하고, 데모의 Hono 서버 · SQLite 명령은 **참고용**이다(운영 백엔드가 아니다). Spring 쪽은 Java 21 · Gradle · MySQL 8.x로 정했다(2026-11-01 추가 결정). 명령은 개발 저장소를 만든 뒤 실제로 돌려 확인하고 채운다(ARCHITECTURE.md 「기술 스택」 · 「착수 체크리스트」).

개발에 쓰는 명령 전부다. `.claude/rules/testing.md` 가 이 문서를 가리킨다.

**저장소를 구분한다.** 이 저장소(`docs-for-PJ-review`)는 문서뿐이라 실행할 명령이 없다. 아래 「프론트 명령」은 **기존 3D 데모 저장소(`SamdoRyu/warehouseDemo_fork`) 루트** 기준이다. 실제 개발은 새로 만들 개발 저장소에서 한다(2026-10-28 결정 — ARCHITECTURE.md 「저장소」). 개발 저장소는 모노레포다(2026-11-03 결정) — 만들면 프론트 명령은 `frontend/`, 백엔드 명령은 `backend/`, 로컬 DB(`compose.yaml`)는 저장소 루트에서 실행한다.

> **확인 범위**: 명령과 동작은 2026-10-28에 데모의 `package.json` · 설정 · 소스를 읽어서 정리했다. 데모가 실제로 실행되는 것은 2026-11-01에 팀이 확인했다. 아래 명령별 통과 여부(예: `test:contract` · `test:e2e`)는 따로 기록되지 않았다 — 개발 저장소로 옮길 때 다시 돌려 보고 이 문서를 고친다.

## 처음 받았을 때 (프론트 — 데모 기준)

```bash
git clone https://github.com/SamdoRyu/warehouseDemo_fork.git   # 개발 저장소를 만들면 주소를 바꾼다
cd warehouseDemo_fork
npm install          # package-lock.json 이 있으므로 npm 을 쓴다 (CI 라면 npm ci)
npm run dev          # 화면(Vite :5173) — 브라우저 mock 데이터
```

→ 화면 `http://localhost:5173` (브라우저 mock 데이터). `?source=api`(서버 데이터)는 v1에서는 Spring 서버를 가리키게 된다.

- **Node 24 LTS**로 통일하고 개발 저장소에 `.nvmrc`를 둔다(2026-10-28 결정). 의존성 조건으로 보면 Node 22.12 이상이 필요하다(vite).
- 화면 작업은 GitHub 없이 한다(브라우저 mock 또는 Spring 서버 데이터). 연동 작업과 시범 운영만 실제 GitHub App을 쓴다.

## 프론트 명령 (데모 기준 — 재사용)

모두 데모 저장소 루트에서 실행한다. "v1에서"는 Spring 백엔드로 바꾸면서 달라지는 점이다.

| 명령 | 실제로 하는 일 | 선행 조건 · 의존 | v1에서 |
| --- | --- | --- | --- |
| `npm run dev` | `vite` — 화면 개발 서버 `:5173`. `/api` 요청을 `127.0.0.1:${API_PORT ?? 8787}`로 넘긴다 | `npm install` | 그대로. 프록시 대상을 Spring 서버 포트로 바꾼다(포트는 결정 필요) |
| `npm run build` | `tsc -b && vite build` — 타입 검사 후 `dist/` 빌드. 이어서 `postbuild`가 `scripts/check-bundle.mjs`로 첫 화면이 3D 청크를 바로 부르지 않는지 검사(어기면 실패) | `npm install` | 그대로. 3D 코드를 첫 화면 번들에 넣으면 실패한다(NFR-02). 빌드 결과를 Spring이 서빙할지는 결정 필요 |
| `npx tsc -b` | 타입 검사만(빌드 없음). `tsconfig.json`이 화면 · 서버 · 도구 프로젝트를 참조한다 | `npm install` | 서버(TS) 프로젝트 참조는 빠진다 |
| `npm run lint` | `oxlint` — `.oxlintrc.json` 규칙(react · typescript · oxc, `rules-of-hooks` error) | `npm install` | 그대로. 포매터는 없다 |
| `npm run test:contract` | `tsx --test tests/contract/*.test.ts` — DataSource 계약을 mock과 API(메모리 DB TS 서버) 양쪽에 돌리고, 순수 규칙 · 클라이언트도 검사 | `npm install` | mock · 클라이언트 쪽은 재사용. API 쪽 계약은 Spring 통합 테스트가 맡고, 프론트는 핵심 E2E로 확인한다 — ARCHITECTURE.md 「테스트 경계」(구현 기본값 — 2026-11-01 추가 결정). Task · Member CRUD 시나리오는 v1 계약으로 바꾼다 |
| `npm run test:e2e` | `playwright test` — 시나리오를 mock(`/`)과 api(`/?source=api`) 두 번씩. 테스트가 직접 Vite `:5180` · API `:8790`을 띄운다 | **Google Chrome이 설치돼 있어야 한다**(`channel: 'chrome'`). 포트가 비어 있어야 한다 | mock 쪽은 재사용. api 쪽은 Spring 서버를 띄우는 방식으로 바뀐다 — 띄우는 방법은 개발 저장소를 만든 뒤 정한다. 작업 CRUD · 드래그로 담당 지정 · 주간 계획 시나리오(`02` · `03` · `04` · `07` · `08`)는 v1 동작과 달라 고치거나 뺀다 |
| `npm run preview` | `vite preview` — 빌드 결과 미리 보기 | `npm run build` | 그대로 |
| `npx tsx scripts/data-smoke.ts` | mock 데이터 소스의 불변 조건 스모크 검사 | `npm install` | mock 모델이 바뀌면 함께 바뀐다 |

### 데모 서버 명령 (참고만 — 운영 백엔드 아님)

| 명령 | 하는 일 | v1에서 |
| --- | --- | --- |
| `npm run server` · `server:once` | `tsx watch server/src/index.ts` — Hono API `127.0.0.1:${PORT ?? 8787}`, `server/data/office.db`(SQLite) 자동 생성 · 시드 | 쓰지 않는다. 동작 · API 설계를 볼 때만 띄운다 |
| `npm run dev:all` | `concurrently`로 `dev` + `server` | 쓰지 않는다. v1 로컬은 MySQL만 컨테이너로 띄우고 프론트(`frontend/`)와 Spring(`backend/`)을 각자 직접 실행한다(2026-11-03 결정 — 권장 구성) |

## 백엔드 명령 (Spring Boot — Java 21 · Gradle)

빌드 도구는 Gradle, JDK는 21이다(2026-11-01 추가 결정). 아래 명령은 Gradle Wrapper의 일반 명령으로 적은 **예정**이고, 개발 저장소가 아직 없어 **실행해 확인하지 않았다**. 저장소를 만든 뒤 실제로 돌려 보고 고친다(모노레포의 `backend/`에서 실행).

| 하고 싶은 것 | 명령 |
| --- | --- |
| 서버 실행 (로컬) | `./gradlew bootRun` (예정 — 미확인) |
| 테스트 (단위 · 통합) | `./gradlew test` (예정 — 미확인). JUnit + Spring 통합 테스트. 실제 GitHub를 부르지 않는다 |
| 빌드 (배포용 산출물) | `./gradlew build` (예정 — 미확인) |
| DB migration 적용 | TODO — migration 도구는 골격 만들 때 정한다(`[제안]` Flyway) |
| 정적 분석 · 포매터 | TODO — 새 도구는 팀 합의 후(`.claude/rules/scope-control.md`) |

## DB

저장 항목은 [06-data.md](./06-data.md), Entity · 관계 · ID · 무결성 제약은 [CONTRACTS.md](./CONTRACTS.md)(v1 확정 — 2026-11-03), 저장 방식은 ARCHITECTURE.md 「저장소 (JPA)」.

- **DB는 MySQL 8.x다**(2026-11-01 추가 결정). 데모의 SQLite 파일 · `repo.migrate()` 방식은 쓰지 않는다(2026-11-01 결정 — 2026-10-28의 "JSON 방식 이어 쓰기" 결정을 대신함).

### 1. 로컬 DB

권장 구성(2026-11-03 결정): 로컬 MySQL 8.x는 저장소 루트의 `compose.yaml`(Docker Compose)로 띄우고, 프론트와 Spring은 컨테이너 밖에서 직접 실행한다. Docker는 개발 환경 선택일 뿐 배포 플랫폼과 같다고 가정하지 않는다.

| 하고 싶은 것 | 명령 |
| --- | --- |
| 로컬 MySQL 띄우기 · 내리기 | `docker compose up -d` · `docker compose down` (예정 — 미확인. 저장소 루트) |

TODO — 개발 저장소를 만든 뒤 쓴다: `compose.yaml`의 DB 이름 · 포트, 초기 데이터 · 시연 Seed 데이터 넣는 법. DB 접속 정보는 `.env`에서 읽고 `compose.yaml`에 비밀 값을 적지 않는다.

### 2. 환경 변수

목록과 결정 필요 항목은 [환경 변수 가이드](./plan/환경%20변수%20가이드.md)가 기준이다. 요약:

- **데모가 읽는 것(참고)**: `PORT` · `API_PORT` · `DEMO`. 화면의 데이터 소스는 환경 변수가 아니라 주소의 `?source=api`로 고른다.
- **GitHub 연동 때 새로 도입**: `APP_ORIGIN`, `SESSION_SECRET`(Spring 세션에 필요한지는 확인 필요), `GITHUB_APP_*` 4종, `MASTER_ADMIN_GITHUB_LOGIN`, `SYNC_INTERVAL_MINUTES`, `LOG_LEVEL`. DB 접속 변수 이름은 결정 필요.
- `.env.example`은 아직 없다. 개발 저장소 루트에 만든다(이름만, 비밀 값 없음). 그 전에 `.gitignore`에 `.env`, `*.pem`, `secrets/`를 먼저 넣는다 — 데모 `.gitignore`에는 `*.local`만 있다.
- **값을 터미널에 찍거나 Issue · PR · 댓글 · 채팅에 붙이지 않는다.** 확인은 "설정됨 / 미설정" 까지만 한다.
- **빈 값(`KEY=`)은 미설정과 같다.**
- 브라우저로 나가는 변수에는 비밀 값을 넣지 않는다.
- 막힘 기준 시간처럼 팀이 화면에서 조정하는 값은 환경 변수가 아니라 팀 설정에 둔다(코드 컨벤션 (BE) 「기준값」).

### 3. 스키마 적용

TODO — migration 도구가 정해지면 쓴다. 운영 스키마를 JPA 자동 생성(`ddl-auto`)에 맡기지 않는다 `[제안]`.

### migration 정책

스키마 변경은 migration 도구로 관리하고, 저장 구조를 바꾸는 PR에는 위험 범위를 적는다(`.claude/rules/scope-control.md` — DB migration/schema 변경). 생성된 파일은 사람이 수동 편집하지 않는다(`.claude/rules/code-quality.md`).

### production 을 바꾸기 전 — backup / rollback

TODO: 대응 내용 없음 (삭제 여부 확인) — 서버는 외부 인터넷에서 접근할 수 있게 배포한다(SP11 · 2026-11-01 결정 — TBD-09 "팀원 컴퓨터 한 대"를 대신함). 배포 대상과 DB가 정해지면 절차를 쓴다.

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
- 시범 서버에 데모의 초기화(`POST /api/reset`) · 데모 봇 같은 기능을 두지 않는다. 시연은 "시연 데이터" 사무실로 한다(2026-11-01 결정).

## PR 전에 반드시

```bash
# 프론트 (데모 기준 명령)
npm run lint
npm run build            # tsc -b 타입 검사 + 빌드 + 번들 검사
npm run test:contract    # mock · 클라이언트 쪽
npm run test:e2e         # 화면 · 3D 를 바꿨을 때. Chrome 필요
# 백엔드 (backend/ — 예정, 미확인)
./gradlew test           # 단위 · Spring 통합 · 계약 테스트
./gradlew build
```

- 실제로 돌린 명령과 결과만 PR 「검증」에 적는다. 못 돌린 것은 못 돌렸다고 적는다.
- **CI**: 데모 저장소에는 `.github/workflows/`가 없다. 이 문서 저장소의 `.github/workflows/ci.yml`은 명령이 정해지기 전에 만든 placeholder(`echo` 한 줄)라 아무것도 검증하지 않는다. 개발 저장소에 CI를 넣을 때 프론트 명령(e2e 제외 또는 Chrome 설치 포함)과 Spring 테스트 · 빌드로 채운다 — Git 전략 「8. GitHub 설정」.

> **검증 한계 (CI가 채워지기 전까지)**: CI 초록 표시를 검증으로 세지 않는다. Git 전략 「6. 리뷰와 머지」대로 리뷰어가 PR 본문 「검증」에 적힌 실제 명령과 결과를 직접 확인한다.

## 테스트가 덮는 범위

| 테스트 | 덮는 것 | 위치 |
| --- | --- | --- |
| 규칙 단위 (데모) | 순수 규칙(작업 패치 적용 · 팀원 제거 계획 · 회의 시작/종료 계획 · 데모 봇 계획) | `tests/contract/rules.test.ts` |
| 계약 (데모) | 같은 시나리오 약 25개를 mock과 API에 똑같이 | `tests/contract/scenarios.ts` + `mock.test.ts` · `api.test.ts` |
| API 전용 (데모 — 참고) | `X-Seq`, 에러 본문 모양, SSE 다중 클라이언트 · 재생 · 스냅샷, 요청 위생 | `tests/contract/api.test.ts` |
| 클라이언트 (데모) | 에러 변환, SSE 재연결 · 백오프, 늦게 온 응답 무시 | `tests/contract/client.test.ts` |
| E2E (데모) | 로딩 · KPI, 작업 CRUD, 칸반 드래그, 패널 · 3D 담당 지정, 3D 상자 드래그, 안건 · 회의, 주간 계획, 추천, 오프라인 배너, 실시간 동기화, 반응형(1024×768), 터치 | `tests/e2e/01`~`12` |

v1의 테스트 대상:

- **백엔드 도메인 규칙**: 막힘 계산, 평가 처리(이유 필수 검사 · 현재 회차 집계 · 회차 열기), 기술 결정 카드 조립, 강사 쓰기 허용 · 거부, 사무실 접근 검사. 외부 서비스 없이 값만으로 판정하는 것을 JUnit으로 테스트한다(코드 컨벤션 BE 「모듈」).
- **프론트 화면 계산**: SceneModel(공간 역할 · 요구사항 연결 · 안건 뒤에도 원본 위치 유지), 묶음 표시.
- **계약 테스트**(2026-11-03 결정 — Q5): 모노레포 루트 `contract/examples/`의 예시 JSON을 기준으로 백엔드(JUnit + Jackson — Java DTO 왕복, Spring 통합 테스트 — 실제 응답 모양)와 프론트(`satisfies`로 둔 TS 사본을 `tsc`로 검사 + 사본과 공통 JSON 값 비교)가 어긋남을 잡고, 권한 · IDOR · 익명성 · 평가 회차 · 요구사항 재동기화 시나리오는 Spring 통합 테스트로 확인한다. 범위와 통과 기준은 CONTRACTS.md 「9」 계약 테스트. 테스트 코드는 개발 저장소를 만든 뒤 쓴다 — 아직 없다.
- 화면 시연과 화면 테스트는 **브라우저 mock** 으로 하고(2026-10-28 결정) GitHub API 를 부르지 않는다. GitHub 어댑터의 변환은 GitHub 응답 예시 데이터로 테스트한다(신규 예정).

PR 에 "테스트 완료"라고만 적지 않는다. 실제로 한 것만 적고, 테스트하지 못한 부분은 PR 에 명시한다(`.claude/rules/testing.md`).

## 데모 docs/API.md와 코드의 차이 (참고)

데모 저장소의 `docs/API.md`(API 계약)는 대부분 코드와 맞지만 아래가 다르다. 코드가 기준이다. v1 API는 Spring에서 새로 만들므로 이 표는 데모를 읽을 때만 쓴다.

| API.md | 실제 코드 |
| --- | --- |
| 예상하지 못한 오류는 `500 unavailable` | 500을 쓰지 않는다. `DataError`가 아닌 오류는 모두 `503 unavailable` |
| 요청 위생 실패 403이 에러 표에 없음 | `403 invalid`로 응답한다(본문 설명에만 있음) |
| 클라이언트 `src/data/api/ApiDataSource.ts` | 없는 파일. 실제는 `src/data/api/index.ts`(`createApiDataSource`)와 `src/data/api/sse.ts` |
| 시드 `src/data/mock/seed.ts` | 없는 파일. 실제는 `src/data/rules/seed.ts`의 `buildSeed` |
| 조사 함수 `src/data/mock/text.ts` | 없는 파일. 실제는 `src/data/rules/messages.ts` |
| 서버 `http://localhost:8787`, `startServer({ port: 8787 })` | `127.0.0.1`에만 바인딩. `PORT` · `DEMO` 환경 변수를 읽는데 문서에 없다 |

## 확인할 때 자주 쓰는 것 (데모)

| 하고 싶은 것 | 방법 |
| --- | --- |
| 데모 API가 떠 있는지 | `curl http://127.0.0.1:8787/api/health` → `{"ok":true,"version":1}` |
| 서버 데이터로 화면 보기 | `http://localhost:5173/?source=api` |
| 처음 상태로 (mock) | 화면 메뉴 「데이터 초기화」 |

## 검증에 쓰는 것

TODO: 대응 내용 없음 (삭제 여부 확인) — 시범 운영(NFR-01 · TBD-05)과 발표 준비(NFR-08) 단계의 검증 절차가 정해지면 쓴다.

## 주의

- Claude Code 는 `.env` 계열 파일과 `secrets/`, `*.pem` 을 읽지 못하게 막혀 있다(`.claude/settings.json` 의 `deny`). 개발 저장소에도 같은 설정을 둔다.
- GitHub 원본(이슈 · PR · 코멘트 · 라벨)을 바꾸는 GitHub API 요청을 보내는 명령이나 스크립트를 만들지 않는다(CON-02). 로그인 · 설치 토큰 발급 같은 인증 요청은 별개다 — ARCHITECTURE.md 「GitHub 읽기 전용 원칙」.
- E2E는 이미 떠 있는 서버를 재사용하지 않는다. 다른 이유로 5180 · 8790 · 5181 · 8791 포트를 쓰고 있으면 실패한다(개발 서버 5173 · 8787과는 겹치지 않는다).
- Windows에서 `tsx --test tests/contract/*.test.ts`의 `*`는 셸이 아니라 Node가 펼친다(Node 21+). 실행 확인 전이다.
