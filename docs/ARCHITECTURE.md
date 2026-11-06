# Architecture

화면과 서버 코드를 만들기 전에 이 문서를 읽는다. **git 흐름(브랜치·커밋·PR·리뷰·머지)은 여기 없다 — `.claude/rules/git-workflow.md`(개발 컨벤션 > Git 전략)를 본다.**

- 공통 계약(도메인 모델 · Entity 관계 · ID · DTO · 엔드포인트 · 에러 · 권한 · 어댑터 · DataSource · SceneModel · 계약 테스트) — v1 확정(2026-11-03): [CONTRACTS.md](./CONTRACTS.md), 엔드포인트별 상세: [CONTRACTS_API.md](./CONTRACTS_API.md)
- 누가 어느 화면을 맡는지: [SCREEN_ASSIGNMENTS.md](./SCREEN_ASSIGNMENTS.md)
- 누가 어느 서버 기능을 맡는지: [BACKEND_ASSIGNMENTS.md](./BACKEND_ASSIGNMENTS.md)
- 실행·검증 명령과 환경 변수: [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md)

> **근거와 표기**: 기존 3D 데모는 데모 저장소 `SamdoRyu/warehouseDemo_fork`(`FG-Kaki/warehouseDemo`의 fork, `main`)다. 이 문서의 "현재 구현"은 2026-10-28에 그 저장소의 소스를 읽어 확인한 내용이다. 데모의 사용 · 수정 권한과 실제 실행은 2026-11-01에 팀이 확인했다(결정 기록 「기술 스택 · 공간 모델 · 학습 흐름 결정 (2026-11-01)」). 경로는 데모 저장소 기준 상대 경로다.
>
> - **현재 구현**: 데모 코드에 실제로 있는 것
> - **확정 (날짜)**: 팀이 결정 기록으로 정한 것
> - **신규 예정**: v1을 위해 새로 만들 것. 경로는 권장안이고 아직 없다
> - **`[제안]`**: 구현할 때 권장하는 설계. 팀 결정이 아니다
> - **`[구현 기본값]`**: 합의된 기본값으로 시작하고 구현 중 바꿀 수 있는 값(2026-11-01 추가 결정). 보안 · 인증 · 권한 · 데이터 무결성에 닿는 값은 바꿀 때 CONTRACTS.md 「9」 계약 변경 절차를 따른다. 목록은 CONTRACTS.md 「10」
> - **결정 필요**: 팀이 정해야 하는 것. 모아서 PRD.md 「열린 질문」

## 저장소

| 저장소 | 역할 | 비고 |
| --- | --- | --- |
| `SamdoRyu/docs-for-PJ-review` (이 저장소) | 기획 · 설계 · 개발 규칙 문서, Issue · PR 템플릿 초안 | 앱 코드가 없다 |
| `SamdoRyu/warehouseDemo_fork` | 기존 3D 데모 코드. **프론트엔드를 재사용할 원본**이다 — 3D 장면 · 카메라 · 선택 · 드래그 · 애니메이션 · HUD · Zustand · `DataSource` 개념 · 순수 계산 함수 · 프론트 테스트를 적극 재사용하고, 필요하면 대규모로 리팩터링한다(CON-05 — 2026-11-01 결정). Hono 서버 · SQLite는 동작과 API 설계만 참고한다 | 사용 · 수정 권한 확인됨(2026-11-01). **실제 개발은 새로 만들 개발 저장소에서 한다**(2026-10-28 결정). 레포 설정은 Git 전략 「8」 |
| 개발 저장소 (아직 없음) | 프론트엔드(React) + 백엔드(Spring Boot) | **모노레포 — 한 저장소에 `frontend/` + `backend/`**(2026-11-03 결정 — Q6). 공통 계약 변경(Java DTO · TypeScript 타입 · 예시 JSON)을 한 PR에서 검토한다. 이 기획 저장소는 기획 · 설계 기록의 출처로 남는다. 구조는 아래 「개발 저장소」 |

데모 저장소의 `docs/PLAN.md`는 과거 팀 관리 대시보드(관리자 1명 + 팀원 6~10명) 기획이다. 오피스 v1의 요구사항 기준으로 쓰지 않는다.

## 기술 스택

**확정 (2026-11-01 결정)**. 강사에게 React · TypeScript · Three.js 사용이 가능함을 확인했다. HTML · CSS · JavaScript 위주 재구현(이전 ②안)은 후보에서 뺐다.

| 구분 | 확정 | 메모 |
| --- | --- | --- |
| 프론트엔드 | React + TypeScript | 데모 코드 재사용 · 리팩터링 |
| 3D | Three.js + React Three Fiber (데모는 drei도 씀) | 데모 장면 재사용 |
| 상태 관리 | Zustand (데모 구조) | 데이터 사본 · 선택 · 드래그 · 낙관적 갱신 |
| 백엔드 언어 · 빌드 | Java 21 · Gradle | 2026-11-01 추가 결정 |
| 백엔드 | Spring Boot + Spring MVC | **단일 Spring Boot 애플리케이션의 모듈형 모놀리스**를 권장한다. 마이크로서비스 · CQRS · 이벤트 소싱은 전제하지 않는다. Spring Boot 버전은 골격을 만들 때 Java 21과 맞는 것으로 고른다 |
| 데이터 접근 | Spring Data JPA | Entity · 연관관계 · Repository · 페이징 · 정렬 |
| DB | MySQL 8.x | 2026-11-01 추가 결정 — 팀이 부트캠프에서 MySQL Workbench로 실습했고, 요구사항(Entity 관계 · CRUD · 트랜잭션)에 충분하다. migration 도구 · 구체 설정은 구현 때 |
| 인증 · 인가 | Spring Security + 세션 기반 인증 | GitHub OAuth 로그인. 보안 심사용 ID/PW 로그인은 명시적으로 설정한 배포 환경에서만(2026-11-01 추가 결정 — 「인증과 권한」) |
| GitHub 데이터 | GitHub App, 읽기 전용 | CON-02 |
| 배포 | 외부 인터넷에서 접근할 수 있는 웹 애플리케이션 | SP11 (TBD-09 재결정). 배포 플랫폼은 Spring Boot 초기 프로젝트 구축 단계에서 확정한다(2026-11-01 추가 결정) |
| 운영에 쓰지 않는 것 | 데모의 Hono 서버 · `node:sqlite` 저장 | 동작 · API 설계만 참고, 코드 포팅을 전제하지 않음 |

**구현 단계에서 정하는 것**. DB · JDK · 빌드 도구는 2026-11-01 추가 결정으로 위 표에 옮겼다(이전 권장안 "PostgreSQL 우선 검토"는 MySQL 8.x 확정으로 대신했다). 아래는 개발을 막지 않고 정할 시점이 정해진 항목이다. 권장안은 `[제안]`이다.

| 항목 | 권장안 · 기본값 | 정하는 때 |
| --- | --- | --- |
| Spring Boot 버전 | 4.1.2 (GRAVIT `backend/build.gradle`) | 정함 — 골격 |
| DB migration 도구 | Flyway(`backend/src/main/resources/db/migration/`) — JPA `ddl-auto`에 운영 스키마를 맡기지 않는다 | 정함 — 골격 |
| 테스트 DB | MySQL 8.4 — 로컬은 `compose.yaml`, CI는 서비스 컨테이너(GRAVIT README) | 정함 — 골격 |
| 세션 저장 위치 | `[구현 기본값]` 단일 서버의 기본 세션(서버 메모리). 서버를 늘리거나 재시작 때 세션 유지가 필요해지면 바꾼다 | 필요할 때 |
| 로컬 개발 구성 | 2026-11-03 결정(권장 구성): React(Vite)와 Spring Boot는 로컬에서 직접 실행하고, MySQL 8.x만 저장소 루트의 `compose.yaml`(Docker Compose)로 띄운다. Docker는 개발 환경 선택일 뿐 배포 플랫폼과 같다고 가정하지 않는다 | 개발 저장소 만들 때 |
| SPA 서빙 방식 | `[제안]` Spring이 빌드 결과를 정적 파일로 서빙(같은 출처 — CORS 불필요) 또는 리버스 프록시 | 배포 플랫폼과 함께 |
| 배포 플랫폼 | 특정 클라우드를 문서에서 정하지 않는다. HTTPS 인증서 · 도메인 · MySQL 8.x를 쓸 수 있는지 본다 | Spring Boot 초기 프로젝트 구축 단계 |

### 데모가 쓰는 스택 (프론트 재사용 기준 — 데모 `package.json` · `package-lock.json`)

| 구분 | 패키지 | `package.json` 범위 | lock 실제 버전 | v1에서 |
| --- | --- | --- | --- | --- |
| 언어 | TypeScript | `~6.0.2` | 6.0.3 | 프론트 재사용 |
| 화면 | react · react-dom | `^19.2.8` | 19.3.0 | 프론트 재사용 |
| 3D | three | `^0.186.1` | 0.186.1 | 프론트 재사용 |
| 3D | @react-three/fiber | `^9.8.1` | 9.8.1 | 프론트 재사용 |
| 3D | @react-three/drei | `^10.7.9` | 10.7.9 | 프론트 재사용 |
| 상태 관리 | zustand | `^5.0.15` | 5.0.15 | 프론트 재사용 |
| 서버 | hono | `^4.13.13` | 4.13.13 | 참고만 (운영 백엔드 아님) |
| 서버 | @hono/node-server | `^2.1.3` | 2.1.3 | 참고만 |
| DB | `node:sqlite` (Node 내장 `DatabaseSync`) | 의존성 아님 | Node 버전에 따름 | 참고만 |
| 빌드 | vite (Rolldown 기반) | `^8.3.0` | 8.3.2 | 프론트 재사용 |
| 빌드 | @vitejs/plugin-react | `^6.1.1` | 6.1.2 | 프론트 재사용 |
| 실행 | tsx | `^4.23.15` | 4.23.15 | 서버 실행용은 참고만. 프론트 스크립트 · 테스트에 쓰면 유지 |
| 실행 | concurrently | `^10.0.5` | 10.0.5 | 프론트 · Spring은 기본적으로 각자 직접 실행한다(2026-11-03 결정 — 권장 로컬 구성). 한 명령으로 띄우는 스크립트가 필요하면 그때 정한다 |
| 린터 | oxlint | `^1.81.0` | 1.87.0 | 프론트 재사용 |
| 테스트 | @playwright/test (E2E) | `^1.64.0` | 1.64.0 | 프론트 재사용 |
| 테스트 | `node:test` + tsx (계약 테스트) | 의존성 아님 | — | mock 쪽은 재사용. API 쪽은 Spring 서버를 대상으로 다시 구성 — 아래 「테스트 경계」 |
| 타입 | @types/node | `^24.13.3` | 24.19.1 | 프론트 재사용 |

- 앱 코드는 전부 TypeScript(`.ts` · `.tsx`)다. JavaScript는 `scripts/check-bundle.mjs` 같은 스크립트에만 있고, HTML은 `index.html` 하나, CSS는 `src/index.css`와 `src/ui/*.css`다.
- 서버 변경 알림은 SSE(직접 구현, 라이브러리 없음), 포매터는 없다(oxlint만).
- **Node 버전**(프론트 개발 도구용): 데모에는 `engines`와 `.nvmrc`가 없다. 의존성 조건으로 보면 Node 22.12 이상이 필요하다(vite ≥22.12, concurrently ≥22). **Node 24 LTS로 통일한다**(2026-10-28 결정) — 개발 저장소에 `.nvmrc`를 추가한다(Git 전략 「8」).

### 신규 예정 (선정 필요)

| 구분 | 상태 |
| --- | --- |
| GitHub API 호출 방식(Spring의 HTTP 클라이언트로 직접 호출 또는 GitHub 클라이언트 라이브러리) | 선정 필요 |
| GitHub App JWT 서명 · 설치 토큰 발급 | 선정 필요(라이브러리 또는 직접 구현) |
| GitHub 로그인 | Spring Security OAuth2 Login으로 GitHub App의 사용자 인증(client id · secret)을 쓰는 것이 `[제안]` — 동작은 구현 전 확인 필요 |
| 명세 문서(마크다운) 파싱 | 선정 필요(형식은 ## 제목만 — TBD-01, 2026-10-28 결정) |
| 서버 → 브라우저 변경 알림 | `[제안]` Spring MVC의 SSE(`SseEmitter`). 데모 클라이언트(`src/data/api/sse.ts`)의 재연결 동작을 유지할 수 있는지 확인 필요 |

새 dependency는 필요성과 유지보수 상태를 확인하고 팀에 먼저 말한 뒤 추가한다(`.claude/rules/security.md`). 확정 스택 밖으로 프레임워크를 바꾸지 않는다.

## 폴더 구조

### 현재 구현 (데모)

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
├─ server/src/                    (참고만 — 운영 백엔드는 Spring Boot)
│  ├─ index.ts · app.ts           서버 시작(127.0.0.1, PORT ?? 8787) · 조립
│  ├─ routes.ts                   Hono 라우트 · 요청 위생 미들웨어 · 에러 응답
│  ├─ repo.ts · db.ts             저장 로직 · SQLite(node:sqlite)
│  ├─ events.ts                   SSE 허브
│  └─ bot/index.ts                로컬 데모 봇(가상 팀원)
├─ tests/contract/ · tests/e2e/   계약 테스트(node:test) · E2E(Playwright)
└─ scripts/                       check-bundle.mjs(빌드 후 번들 검사) · data-smoke.ts
```

### 개발 저장소 (신규 예정 — 모노레포는 2026-11-03 결정, 그 안의 패키지 경로는 `[제안]`)

```text
(개발 저장소 — 이름 예: gitvillage)
├─ compose.yaml                   로컬 개발용 MySQL 8.x (권장 구성 — 배포 설정 아님)
├─ .env.example                   변수 이름만, 비밀 값 없음 (환경 변수 가이드)
├─ .gitignore                     .env · *.pem · secrets/ 를 가장 먼저
├─ README.md
├─ contract/examples/             계약 예시 JSON — 백엔드 · 프론트 계약 테스트가 함께 읽는다 (CONTRACTS.md 「9」)
├─ frontend/                      데모의 index.html · src/ · tests/ · scripts/ 를 옮겨 리팩터링
│  └─ src/
│     ├─ app/ · scene/ · ui/      데모 구조 유지
│     ├─ scene/model/             SceneModel 계산 — 도메인 데이터 → 공간 역할 · 강조 (순수 함수)
│     ├─ theme/office/            ThemeLayout 기본 테마(사무실) — layout.ts 좌표 · 드롭 영역을 옮겨 옴
│     ├─ data/                    DataSource · types · mock · api (Spring API 클라이언트) · rules(화면 계산 · mock 규칙)
│     └─ store.ts
└─ backend/                       Spring Boot 단일 애플리케이션 (모듈형 모놀리스)
   └─ src/main/java/<base>/       패키지마다 controller · service · repository · domain(Entity) · dto
      ├─ common/                  에러 응답 · 보안 설정 · UTC 시각 · 로그 마스킹
      ├─ auth/                    로그인(GitHub · 심사용) · 세션 · 역할 판정 · 승인된 강사 계정(서버 설정)
      ├─ workspace/               사무실(레포 등록 · 시연 사무실) · 팀 설정 · 강사 지정
      ├─ github/                  GitHub 어댑터 — GitHub API를 부르는 유일한 패키지 (+ 시연용 Seed 어댑터)
      ├─ sync/                    폴링 · 수동 동기화 · 동기화 상태
      ├─ requirement/             명세 문서 · 요구사항 · RequirementLink · 진행률
      ├─ flow/                    막힘 · 팀 지표 · 담당 · 최근 변화
      ├─ evaluation/              PR 평가 · 평가 회차
      ├─ meeting/                 스탠드업 · 회의 · 회의 안건
      ├─ help/                    도움 요청 · 코멘트
      ├─ report/                  학습 타임라인 · 회고 리포트 · 기술 결정 카드
      ├─ activity/                활동 기록 · 동료 피드백
      ├─ plan/                    일정판 · 작업 마감
      ├─ instructor/              강사 요청함 · 컨벤션 라이브러리
      └─ realtime/                SSE 변경 알림 (사무실별 구독)
```

- 계층은 **Controller → Service → Repository**다. Controller는 요청 · 응답 DTO만 다루고, 규칙과 트랜잭션은 Service에, 저장은 Repository(Spring Data JPA)에 둔다. Entity를 응답으로 바로 내보내지 않는다 — 본인 · 강사 여부로 필드를 빼야 하는 응답이 있어서다(NFR-06).
- 패키지 사이는 Service의 공개 메서드로만 부른다. 다른 패키지의 Repository를 직접 쓰지 않는다(`[제안]` — 병렬 개발에서 충돌을 줄이려는 규칙).

### 모듈 이름과 실제 경로

모듈 이름과 의존 방향은 코드 컨벤션(BE) 「모듈」 · NFR-08 그대로다.

| 모듈 | 데모에서 해당하는 곳 | v1 위치 (신규 예정 · `[제안]`) |
| --- | --- | --- |
| `core` (순수 규칙) | `src/data/rules/` — mock과 서버가 함께 쓰는 순수 규칙 | **도메인 규칙의 기준은 백엔드**다(막힘 · 진행률 · 평가 처리 · 카드 조립 · 강사 요청함 · 개인 지표 접근). 각 패키지 Service 안에서 외부 호출 없이 값만으로 판정하는 클래스로 둔다. 화면용 계산(SceneModel · 공간 역할 · 강조)과 mock용 규칙은 `frontend/src/` 에 둔다 |
| `api` | `server/src/routes.ts` | 백엔드 각 패키지의 Controller |
| `store` | `server/src/repo.ts` · `db.ts` | 백엔드 각 패키지의 Repository + Entity (Spring Data JPA). 컨벤션 라이브러리 항목(`ConventionLibraryItem`)은 사무실 단위가 아니라 서버 전체에 하나다. 승인된 강사 계정은 DB가 아니라 서버 설정이다(2026-11-01 추가 결정) |
| `adapters/github` | 없음 | 백엔드 `github` 패키지 — GitHub API를 부르는 유일한 곳 |
| `adapters/fake` | `src/data/mock/` (브라우저 쪽) | ① 화면 개발 · 테스트: 브라우저 mock을 새 모델로 확장(2026-10-28 결정 유지) ② 발표 시연의 다른 레포: 서버 Seed 사무실(2026-11-01 결정) — 「데이터 소스는 두 층이다」 |
| `adapters/local` | 없음 | 무엇을 뜻하는지 기준 문서에 정의가 없다 — 결정 필요 |
| `packs` | 없음(기준값은 `src/data/rules/constants.ts` · `src/data/meta.ts`에 상수로 있음) | 기준값 기본값 · 용어. 팀 설정 밖의 숫자는 백엔드 상수 한 곳 + 화면용 상수 한 곳 |
| 인증 | 없음 | 백엔드 `auth` 패키지 — Spring Security 설정 · 세션 · 역할 판정(강사 지정은 요청마다 앱 DB에서 확인 — 2026-10-30 결정). OAuth 코드 교환 · 사용자 정보 · 레포 권한 조회처럼 GitHub를 부르는 일은 `github` 패키지를 거친다("GitHub API는 GitHub 어댑터에서만" 규칙 유지) |

데모의 `CLAUDE.md`는 `src/data/types.ts` · `store.ts` · `layout.ts` · `selectors.ts` · `insights.ts`를 계약 파일로 보고 메인 세션에서만 바꾸게 한다. 팀 작업에서도 이 파일들과 [CONTRACTS.md](./CONTRACTS.md)가 가리키는 백엔드 공통 파일(에러 · 보안 설정 · 공통 DTO · Entity)은 「건드리기 전에 말해야 하는 곳」이다(SCREEN_ASSIGNMENTS.md · BACKEND_ASSIGNMENTS.md).

### 테스트 경계 (`[제안]`)

- 데모의 계약 테스트는 같은 시나리오를 mock과 API(TS 서버)에 함께 돌린다. API 쪽 서버가 Spring으로 바뀌므로, **같은 시나리오를 두 언어에서 따로 유지하면 어긋난다** — 구현 위험이다.
- `[구현 기본값]` 계약 테스트는 **Spring 통합 테스트 + 프론트 핵심 E2E**로 한다(2026-11-01 추가 결정). 백엔드 계약(엔드포인트 · DTO · 권한 · 에러)은 Spring 통합 테스트가 맡고, 프론트는 mock DataSource로 화면 동작을, Spring 서버를 띄운 핵심 흐름은 E2E로 확인한다.
- **Java DTO ↔ TypeScript 타입**(2026-11-03 결정 — Q5): 논리 계약은 CONTRACTS.md · CONTRACTS_API.md가 기준이고, 자동 생성(OpenAPI → TS)은 쓰지 않는다. 모노레포 루트 `contract/examples/`의 대표 요청 · 응답 JSON이 기준이다. 백엔드는 JUnit + Jackson으로 DTO와 왕복 비교하고 Spring 통합 테스트로 실제 응답 모양을 비교한다. 프론트는 `satisfies DTO`로 둔 TS 사본을 `tsc`로 검사하고, 사본과 공통 JSON을 값 비교 테스트로 묶는다(JSON을 바로 import하면 리터럴이 `string`으로 넓어져 유니언 타입 검사가 깨진다). 범위와 통과 기준은 CONTRACTS.md 「9」 계약 테스트.
- 백엔드 규칙 테스트는 JUnit으로 Service 안의 순수 규칙을 검사한다. GitHub 어댑터 변환은 GitHub 응답 예시 데이터로 테스트하고 실제 GitHub를 부르지 않는다.

## 실행 구조

### 현재 구현된 구조 (데모)

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

- 3D는 lazy 로딩이고 HUD가 먼저 뜬다. `npm run build` 뒤 `scripts/check-bundle.mjs`가 첫 HTML이 three · r3f 청크를 바로 부르면 빌드를 실패시킨다 — 코드상 NFR-02("상단 바와 패널이 3D보다 먼저")를 지키는 구조다.
- 1024px 반응형 레이아웃이 있다(`src/ui/responsive.css`, E2E `11-responsive`) — 코드상 NFR-07과 맞는다.

### v1 구조 (신규 예정)

```text
GitHub ──(설치 토큰으로 읽기만 · 폴링 · 웹훅 없음)──▶ Spring github 어댑터
                                                        │ GitHub 원본(Github*) → 앱 모델(Task · Submission) 변환
                                                        ▼
                                          sync ─▶ JPA Repository (공통 DB) ── syncedAt · 동기화 상태
                                                        │  (모든 사무실 단위 데이터는 Workspace에 속함)
                                                        ▼
브라우저 ◀── SSE 변경 알림(사무실별) ── Spring MVC Controller ◀── Spring Security(세션 · 역할 · 사무실 접근 · CSRF)
   │                                                        ▲
   └─ DataSource(api) ─── REST /api/** ─────────────────────┘
   └─ DataSource(mock) ── 화면 개발 · 테스트 (서버 없음)

시연 사무실 ── Seed 어댑터(가짜 데이터) ── 같은 저장 · 같은 API · 같은 접근 검사 · "시연 데이터" 표시
```

- **GitHub 폴링과 SSE는 다른 일이다.** 폴링은 서버가 GitHub의 변경을 가져오는 방법이고(CON-03, FR-GH-03), SSE는 앱 서버가 브라우저에 "바뀌었다"를 알리는 방법이다. 동기화 결과도 SSE로 알린다.
- **SSE도 권한 검사를 거친다**(2026-11-01 결정). 구독은 사무실 단위이고, 연결할 때 세션과 그 사무실의 접근 권한을 검사한다. 사용자가 볼 수 없는 사무실의 변경은 보내지 않는다. `[제안]` 세션이 끝나면 연결을 닫고, 이벤트 본문에는 "무엇이 바뀌었나"만 담아 화면이 권한 검사를 거친 REST로 다시 읽게 한다(개인 지표가 이벤트로 새지 않게).
- GitHub 웹훅은 쓰지 않는다(CON-03, 기획안 「이번 범위에서 제외」).
- 화면은 GitHub를 직접 부르지 않는다. GitHub 토큰은 서버에만 있다(NFR-04).
- 여러 조가 서버 1개 · 공통 DB를 같이 쓴다(2026-11-01 결정 — SP11). 사무실(Workspace)마다 데이터를 나누고, 사용자는 접근할 수 있는 사무실만 받는다(P2).

## 데이터 소스는 두 층이다

실제 코드에서 `DataSource`는 **브라우저 쪽 인터페이스**다. GitHub 토큰은 브라우저로 나갈 수 없으므로(NFR-04) GitHub 어댑터는 **서버 안**에 있다.

| 층 | 현재 구현 | v1 |
| --- | --- | --- |
| 브라우저 `DataSource` (`src/data/DataSource.ts`) | `mock`(localStorage) · `api`(Hono 클라이언트). `?source=api` 쿼리로 고른다 | 이름과 교체 구조를 유지한다(CON-05). `api`는 Spring API 클라이언트로 바뀐다. 화면은 계속 이 인터페이스로만 데이터를 받는다 |
| 서버 데이터 원천 | 없음 — 서버는 자기 DB만 안다 | 사무실 종류에 따라 고른다 — 실제 사무실은 GitHub 어댑터, 시연 사무실은 Seed 어댑터(2026-11-01 결정). `[제안]` 서버 전체 환경 변수(`DATA_SOURCE`)가 아니라 사무실의 종류 값으로 고른다 |

**정함**:

- (2026-10-28 결정) 화면 작업의 가짜 데이터는 기존 브라우저 mock을 새 모델로 확장해서 쓴다. → **화면 개발 · 테스트에는 그대로 유지한다.**
- (2026-11-01 결정) 실제 개발 · 발표의 데이터 원천은 우리 조 GitHub 레포다. 다른 레포로 전환하는 시연은 서버의 Fake/Seed 데이터 사무실로 한다.
  - 같은 프론트 데이터 계약을 쓴다.
  - "시연 데이터"로 표시해 실제 GitHub 활동과 구분하고, 다른 조의 실제 데이터처럼 보이지 않게 한다.
  - 인증 · 접근 제어를 똑같이 적용한다.
  - 이 결정으로 10-28 결정의 "발표 시연도 브라우저 mock" 부분은 바뀐다.

### DataSource 인터페이스에서 바꿀 것

현재 메서드(`src/data/DataSource.ts`): 읽기 `load` · `subscribe`, 쓰기 `createTask` · `updateTask` · `deleteTask` · `createMember` · `updateMember` · `deleteMember` · `startMeeting` · `endMeeting`, 선택 `setDemoMode` · `getDemoMode` · `reset`.

| 책임 | 현재 | v1 방향 |
| --- | --- | --- |
| GitHub 원본 읽기 | 없음 | 서버 GitHub 어댑터가 폴링으로 읽는다(B2) |
| GitHub 원본 → 앱 모델 변환 | 없음 | 서버 GitHub 어댑터. GitHub 모양은 어댑터 밖으로 내보내지 않는다 |
| 앱 모델 저장 · 동기화 상태 | `repo.ts`(Task · Member · Meeting CRUD) | JPA Entity에 `syncedAt` · 마지막 성공 시각 · 실패 사유와 함께 저장(FR-GH-05) |
| 이슈 · 팀원 만들기 · 고치기 · 지우기 | `createTask` · `updateTask` · `deleteTask` · `createMember` … | **없앤다** — GitHub에서 오는 값은 앱에서 바꾸지 않는다(CON-02) |
| 앱 자체 기록 쓰기 | `startMeeting` · `endMeeting` | 요구사항–이슈 연결, 회의 · 안건, 스탠드업, 평가, 도움 요청, 작업 마감, 강사 답변 등 쓰기 메서드 신규 — API와 1:1, 메서드 이름은 CONTRACTS_API.md 「0」 |
| 개인 학습 데이터 읽기 | 없음 | `load`(Snapshot)에 넣지 않고 따로 읽는다 — 학습 타임라인 · 회고 리포트 · 기술 결정 카드(2026-11-03 결정 — CONTRACTS.md 「5」) |
| 화면 표시 | `load` → 스토어 → 장면 · HUD, `subscribe`로 변경 반영 | 유지. 사무실 단위로 읽는다 |
| 시연 | mock + 데모 봇 | 화면 개발은 mock. 발표 시연은 서버 Seed 사무실(2026-11-01 결정). 데모 봇은 `[제안]` Spring 서버로 옮기지 않는다 |

이 인터페이스에 기대는 곳이 많아 변경 영향이 크다. 실제 TypeScript 인터페이스는 이번 문서 작업에서 바꾸지 않았다.

| 파일 | 지금 기대는 것 | 바꿔야 하는 이유 |
| --- | --- | --- |
| `src/store.ts` | Task · Member CRUD 낙관적 갱신, `endDrag`의 담당 지정 · 상태 변경 · 마감일 변경 · 안건 올리기 | 담당 · 상태는 GitHub 원본이라 드래그로 바꿀 수 없다. 드래그 결과는 요구사항 연결(상자는 제자리로) · 안건 만들기(별도 `AgendaItem`)로 바뀐다(2026-11-01 결정). 드래그로 마감 바꾸기도 뺀다(2026-10-28 추가 결정) |
| `src/data/selectors.ts` · `insights.ts` | `estimate`(포인트) · `capacity` · `dueDate`로 업무량 · KPI · 추천 계산 | 포인트 · 용량은 GitHub에 없고, 사람별 비교는 금지다(FR-FLOW-06) |
| `src/scene/slots.ts` | `status` · `assigneeId` · `onAgenda`로 위치 계산 | SceneModel(공간 역할)과 ThemeLayout(좌표)로 나눈다 — 「공간 역할과 테마」 |
| `src/ui/MeetingDialogs.tsx` · `src/data/rules/meetings.ts` | 회의 종료 결과로 담당 지정 · 상태 변경 · 백로그로 | GitHub 원본을 바꾸는 결정이라 CON-02와 충돌한다. 결정 기록 · 배운 점 저장으로 바뀐다 |
| `server/src/bot/index.ts` | API로 작업을 만들고 옮긴다 | 운영 백엔드가 아니므로 옮기지 않는다 `[제안]` |
| `tests/contract/*` | 위 CRUD 계약 | 계약이 바뀌면 함께 바뀐다 — 「테스트 경계」 |

## GitHub 원본 상태와 앱 상태

두 종류의 상태를 섞지 않는다. 값 목록은 05-policy.md 「상태값」이 기준이다.

| 종류 | 예 | 누가 바꾸나 | 어디에 |
| --- | --- | --- | --- |
| GitHub 원본 상태 | 이슈 담당자 · 열림 · 닫힘 · 라벨(`priority:*` 포함), PR 열림 · Draft · 리뷰 요청 · 머지 · 닫힘 · 최신 커밋(`headSha`), 콜라보레이터, PR–이슈 연결 | GitHub(사람이 GitHub에서). 앱은 다음 동기화에 반영만 한다 | GitHub 어댑터가 읽어 앱 모델(`Task` · `Submission`)로 바꾸고 `syncedAt`과 함께 저장 |
| 계산한 값 | 표시 상태 · 공간 역할(05 「상태값」 표), 대표 담당자, 막힘, 진행률, 팀 지표, 현재 평가 회차의 추천·비추천 수(위치는 바꾸지 않음 — 2026-10-29 결정), 강사 요청함 목록, 우선순위 색, 요구사항 묶음 | 원본 상태와 앱 상태에서 계산한다 | 도메인 계산은 백엔드, 화면 표현(공간 역할 · 묶음 · 색)은 프론트. 대표 담당자는 서버가 담당자 순서를 정렬해 보내고 화면은 첫 번째를 쓴다(GRAVIT CONTRACTS.md 「3」 · 「10」) |
| 앱 상태 | 요구사항–이슈 연결, 동기화 상태, 회의 안건 상태, PR 평가 · 평가 회차, 도움 요청 상태, 작업 마감, 강사 지정(`InstructorAssignment`), 강사 요청과 답변, 컨벤션 라이브러리 항목(서버 전체에 하나), 사무실(Workspace), 시연 사무실 접근 대상, 심사용 계정 | 앱 사용자 · 앱 규칙 | 앱 DB에만 (CON-02) |
| 서버 설정 | 승인된 강사 계정(허용 목록 — GitHub 사용자 ID), 전체 관리자, 심사용 로그인 켜기 | 운영(서버 설정) | 서버 설정 — 2026-11-01 추가 결정 |

- **지금 데모의 `Task.status`(`todo` · `in_progress` · `review` · `done`)는 앱이 직접 바꾸는 값이다.** v1에서는 GitHub 원본(열림 · 닫힘, 담당자)에서 계산한다. 열린 이슈에 담당자가 있으면 "작업 중"이다(2026-11-01 결정). 이슈와 PR은 다른 항목이고, "닫힌 이슈 = 머지된 PR"로 가정하지 않는다.
- **다중 담당자**(2026-11-01 추가 결정): 팀은 이슈마다 담당자 1명을 권장하지만 여러 명을 허용하고 서버가 막지 않는다. 동기화는 담당자 전원을 `TaskAssignee`(Task–Member 다대다 중간 Entity)로 저장한다. 3D에는 대표 담당자(결정적인 규칙 — CONTRACTS.md 「3」) 작업 공간에 상자 하나만 놓고, 상세 패널에는 담당자 전원을 보인다.
- **위치는 원본에서만 계산한다**(2026-11-01 결정). 요구사항 연결은 `RequirementLink`만 만들고, 회의 안건은 별도 `AgendaItem`을 만든다 — 둘 다 원본 이슈 · PR의 위치를 바꾸지 않는다.
- PR이 회의 안건이 되어도 PR 평가는 그대로다. 안건은 따로 저장한다(06-data.md 「PR 평가」 · 「회의 안건」).
- **PR 평가 회차**(2026-11-01 결정): 회차 식별자와 `headSha`는 별개다.
  - 새 회차는 새 PR · 새 커밋(동기화에서 `headSha`가 바뀜) · 회의의 "수정 후 재평가" 결정 때 연다.
  - 각 회차에 시작 시점의 `headSha`를 남긴다. 같은 `headSha`에 회차가 여러 개일 수 있다.
  - 이전 회차는 보존한다.
- **PR–이슈 연결**(FR-GH-04 — 2026-11-01 추가 결정): **GitHub가 공식으로 인식하는 연결 관계만** 읽어 앱 모델에 반영한다. 앱이 추측해 만들지 않는다.
  - PR 본문 · 제목에 `#12`처럼 언급만 된 것으로는 연결하지 않는다. 앱이 PR 본문을 직접 파싱하지 않는다.
  - 팀 PR 본문의 `Closes #N`(Git 전략)은 GitHub가 그것을 연결로 인식했을 때만 그 연결로 들어온다.
  - 예: GitHub가 PR에 "이 PR이 닫을 이슈"로 기록한 연결(GraphQL의 closing issue references). 어떤 API로 읽을지는 개발 단계에서 고른다.
- **우선순위**(2026-11-01 결정): `priority:high` · `priority:medium` · `priority:low` 라벨을 쓰고, 라벨이 없으면 중립이다.
  - 이 방식은 바뀔 수 있다. 라벨 이름 → 우선순위 대응은 상수 한 곳에 둔다.
  - `[제안]` 라벨이 여러 개면 가장 높은 값을 쓴다.
- 원본 상태를 앱이 바꾸는 경로는 없다. 머지 · 이슈 만들기는 GitHub 화면 링크로 넘긴다(P3).

## 서버 API

### 데모 API와 v1 API

데모 API는 **참고**다. v1 API는 Spring에서 새로 만들고, 공통 규칙은 [CONTRACTS.md](./CONTRACTS.md) 「4」 · 「5」 · 「6」, 엔드포인트 · DTO는 [CONTRACTS_API.md](./CONTRACTS_API.md)에 있다(v1 확정 — 2026-11-03).

| 구분 | 데모 경로 | v1에서 |
| --- | --- | --- |
| 상태 확인 | `GET /api/health` | 같은 목적의 엔드포인트를 둔다 |
| 전체 데이터 | `GET /api/workspace` (`X-Seq` 헤더) | 사무실 단위 `GET /api/workspaces/{workspaceId}/snapshot`(`WorkspaceSnapshot`). 응답이 새 모델(이슈 · PR · 요구사항 · 안건 …)이고, 개인 지표는 넣지 않고 개인 API로 나눈다(2026-11-03 결정) |
| 작업 · 팀원 CRUD | `POST` · `PATCH` · `DELETE /api/tasks` · `/api/members` | 없앤다 — 이슈와 팀원은 GitHub에서 온다 |
| 회의 | `POST /api/meetings` · `POST /api/meetings/:id/end` | 안건 · 결정 · 배운 점 기록으로 다시 만든다(FR-MTG-03 · 04) |
| 데모 · 초기화 | `GET` · `PUT /api/demo`, `POST /api/reset` | 운영 서버에는 두지 않는다 `[제안]`. 시연은 Seed 사무실로 한다 |
| 변경 알림 | `GET /api/events` (SSE) | 사무실별 SSE로 다시 만든다 — 권한 검사 포함 |
| 신규 | — | 로그인 · 세션, 조직 · 레포 · 사무실 열기, 수동 동기화 · 동기화 상태, 요구사항 · 연결, 스탠드업 · 일정판 · 작업 마감 · 회고 리포트, 평가 · 평가 회차 · 안건 · 도움 요청, 강사 지정 · 강사 요청함 · 컨벤션 라이브러리 |

데모의 `docs/API.md`는 실제 코드와 다른 곳이 있다 — PROJECT_COMMANDS.md 「데모 docs/API.md와 코드의 차이」(참고용).

### GitHub 읽기 전용 원칙 (CON-02)

CON-02는 **앱이 GitHub의 원본(이슈 · PR · 코멘트 · 라벨 등)을 바꾸지 않는다**는 뜻이다. HTTP 메서드 이름으로 판단하지 않는다.

| 요청 | 허용 | 이유 |
| --- | --- | --- |
| GitHub API로 이슈 · PR · 커밋 · 리뷰 · 라벨 · 참여자 읽기 | 허용 | 읽기 |
| GitHub 로그인의 OAuth 코드 교환(`POST https://github.com/login/oauth/access_token`) | 허용 | 인증 절차. 원본을 바꾸지 않는다 |
| GitHub App 설치 토큰 발급(`POST /app/installations/{id}/access_tokens`) | 허용 | 인증 절차. 원본을 바꾸지 않는다 |
| GraphQL의 조회(query) | 허용 | 읽기. mutation은 금지 |
| 이슈 만들기 · 코멘트 · 라벨 붙이기 · 머지 · 상태 변경 등 GitHub 원본을 바꾸는 호출 | **금지** | CON-02. 할 일은 GitHub 화면 링크로 넘긴다(P3) |
| 사용자의 원본 변경 동작(이슈 생성 · 닫기, PR 생성 · 병합 · 닫기, 라벨 · 담당자 · 상태 변경) | 서버 호출 없이 **GitHub 웹페이지로 이동** | 사용자가 GitHub에서 직접 하고, 앱은 다음 동기화에서 읽는다(P3 — 2026-11-01 추가 결정). 예: 앱의 "PR 병합" 버튼 → GitHub의 그 PR 페이지 |
| 브라우저 → 앱 서버 API의 `POST` · `PATCH` · `PUT` · `DELETE` | 허용 | 앱 자체 기록을 앱 서버에 저장한다 |

GitHub App 권한을 Metadata · Contents · Issues · Pull requests 모두 **읽기 전용**으로 두면, 실수로 쓰기 호출을 만들어도 GitHub가 거부한다.

### 에러 응답 형식

데모 모양 `{ "error": { "code", "message" } }`를 그대로 쓰고, code는 소문자 snake다(2026-10-28 결정 — 스택이 바뀌어도 유지). Spring에서는 공통 예외 처리(`[제안]` `@RestControllerAdvice` 한 곳)가 이 모양으로 바꾼다. 클라이언트 `src/data/api/index.ts`의 `toError`(`not_found`면 로컬에서 지우고 `unavailable`이면 SSE 재연결)와 `DataError` 타입이 이 모양에 기댄다.

- 기존: `invalid`(400 · 요청 출처 검사 실패는 403) · `not_found`(404) · `conflict`(409) · `unavailable`(503)
- 신규: `unauthorized` · `forbidden_role` · `github_unauthorized` · `github_rate_limited` — 표는 코드 컨벤션(BE) 「에러 응답」 · CONTRACTS.md

## 인증과 권한

### 현재 구현 (데모)

- **로그인 · 세션 · 권한 검사가 없다.** 데모 `docs/API.md`도 "Local-only: no auth"라고 적는다.
- 서버는 요청 위생 미들웨어(`server/src/routes.ts`)만 가진다 — `Host` 검사(DNS 리바인딩 방지), `Origin` 검사, 쓰기 요청의 `Content-Type: application/json` 필수. 기획안 「시스템 구조」의 "기존 서버의 요청 검사 규칙 유지"(NFR-05)는 이 미들웨어를 가리킨다.
- `X-Actor: <memberId>` 헤더는 데모 봇이 "어느 팀원이 한 일인지"를 표시하는 값이다. **인증이 아니다.**

### v1 (신규 예정 — Spring Security + 세션, 2026-11-01 결정)

GitHub 로그인과 레포 읽기는 **GitHub App 하나**로 처리한다. OAuth App은 비공개 레포를 읽으려면 쓰기까지 포함된 `repo` 권한이 필요해서 읽기 전용 최소 권한(NFR-04)을 지킬 수 없기 때문이다 — 개발 컨벤션 > 환경 변수 가이드.

**외부 인터넷에 공개하는 서버다.** GitHub 로그인이 있다는 이유로 아래 검사를 빼지 않는다(2026-11-01 결정).

| 단계 | 내용 | 상태 |
| --- | --- | --- |
| 로그인 | GitHub App의 사용자 인증(`GITHUB_APP_CLIENT_ID` · `GITHUB_APP_CLIENT_SECRET`)으로 "누가 들어왔는지"를 정한다. `[제안]` Spring Security OAuth2 Login. 팀원과 강사 모두 GitHub로 로그인한다. 보안 심사용 ID/PW 로그인은 아래 「보안 심사용 로그인」 | 신규 예정 |
| 세션 | 서버 세션(HttpSession)과 세션 쿠키. 세션은 30분~1시간 뒤 만료된다(2026-10-28 결정). `[구현 기본값]` 단일 서버의 기본 세션으로 시작한다. 로그인할 때 세션 ID를 새로 발급하고, 로그아웃하면 세션을 끝낸다 | 신규 예정 |
| 쿠키 · HTTPS | 외부 배포는 HTTPS로만 받는다. 세션 쿠키는 `HttpOnly` · `Secure` · `SameSite` | 신규 예정 |
| CSRF | Spring Security의 CSRF 방어를 켠다. `[제안]` SPA는 쿠키로 받은 CSRF 토큰을 요청 헤더에 담아 보낸다. SSE · 조회 요청은 대상이 아니다 | 신규 예정 |
| 출처 검사 (NFR-05) | 데모 미들웨어의 목적(다른 출처의 요청 차단)을 Spring에서 다시 지킨다 — 허용 출처는 `APP_ORIGIN`. SPA를 같은 출처로 서빙하면 CORS를 열지 않는다 | 신규 예정 |
| 역할 판정 | 팀 레포의 GitHub 권한과 앱의 강사 지정에서 정한다 — admin = 레포 관리자(ROLE-02), 서버 설정 허용 목록에 있고 레포 관리자가 앱 팀 설정에서 그 사무실에 지정한 사람 = 강사(ROLE-04), 그 밖의 레포 콜라보레이터 전원(read 포함) = 참여자(ROLE-01), `MASTER_ADMIN_GITHUB_LOGIN` = 전체 관리자(ROLE-03) · P1. 허용 목록의 계정은 ROLE-01 · 02로 판정하지 않는다(아래 「강사 계정」). GitHub 권한은 로그인할 때만 확인해 세션에 두고(레포마다), 강사 지정 여부는 요청마다 앱 DB에서 확인한다(2026-10-30 결정). 어느 자격 증명으로 권한을 조회하는지는 B1 구현 때 정한다 | 신규 예정 |
| 요청마다 권한 검사 | API에서 요청마다 ① 로그인 ② 그 사무실에 접근할 수 있는지(현재 GitHub 자격, 강사 지정, 시연 사무실 접근 대상 중 하나 — 없으면 `not_found`로 존재를 숨긴다 · `[구현 기본값]`) ③ 그 동작을 할 수 있는 역할인지 확인한다(FR-AUTH-04). 버튼을 숨긴 것은 권한 확인이 아니다. `[제안]` 사무실 접근과 역할 판정은 인가 서비스 한 곳에 두고, 컨트롤러 메서드마다 그 서비스로 검사한다(Spring Security 메서드 보안 등). 강사의 허용되지 않은 쓰기는 `forbidden_role`(FR-AUTH-08 · P13) | 신규 예정 |
| 실시간 갱신 권한 | SSE 연결도 세션 · 사무실 접근을 검사한다 — 「v1 구조」 | 신규 예정 |
| 설치 토큰 | `GITHUB_APP_ID`와 키 파일(`GITHUB_APP_PRIVATE_KEY_PATH`)로 설치 토큰을 받아 레포를 동기화한다. 토큰은 서버 메모리에서만 쓰고 만료 전에 다시 발급한다. DB 저장 여부는 결정 필요(권장: 저장하지 않음). GitHub 어댑터 안에서만 쓰고 브라우저로 내보내지 않는다(NFR-04) | 신규 예정 |

**강사 계정** (2026-11-01 결정 · 2026-11-01 추가 결정):

- **확정**: 일반 학생은 강사로 지정할 수 없다. 레포 관리자는 승인된 강사 계정 중에서만 지정한다. 목록에 없는 계정을 지정하는 요청은 서버가 거부한다.
- **확정 — 판정 순서**: 강사는 본인 GitHub 계정으로 로그인한다. ① GitHub 로그인으로 식별 ② 서버 설정 허용 목록에 있는지 ③ 그 사무실의 강사로 지정되어 있는지(요청마다 DB) ④ ROLE-04. 강사가 그 레포의 콜라보레이터가 아니어도 된다. GitHub 레포의 직접 수정 권한을 주는 일은 앱 역할과 별개다.
- **확정 — 승인 · 등록 방법**: 아래 대안 표의 **A(서버 설정 허용 목록)**를 쓴다. 키는 로그인 이름이 아니라 바뀌지 않는 **GitHub 사용자 ID**다. 관리 화면은 만들지 않는다. 허용 목록(강사 후보 등록)과 사무실별 강사 지정은 다른 개념이다. 설정 이름은 환경 변수 가이드.
- **확정 — 지정 밖**: 허용 목록의 계정은 어느 사무실에서도 ROLE-01 · 02로 판정하지 않는다. 지정되지 않았거나 지정이 해제된 사무실에는 콜라보레이터여도 접근할 수 없고, 팀원 목록 · 책상 · 평가 인원에서 빠진다. 해제하면 강사 권한이 즉시 끝난다(사용자 확인 — 2026-11-01 추가 결정).

아래는 검토했던 대안이다(A 채택).

| 대안 | 방법 | 장점 | 단점 |
| --- | --- | --- | --- |
| A 환경 변수 (**채택**) | 서버 설정에 강사 GitHub 계정 목록 — GitHub 사용자 ID 기준 | 구현이 가장 작고 화면이 필요 없다 | 바꾸려면 서버 설정을 고치고 재시작해야 한다 |
| B DB + 전체 관리자 기능 | 전체 관리자(ROLE-03)가 앱에서 강사 계정을 등록 · 해제 | 재시작 없이 바로 반영, 등록 이력을 남길 수 있다 | 전체 관리자용 화면 · API가 새로 필요하다(범위 증가) |
| C DB 시드 · 관리 명령 | 배포할 때 시드 데이터나 관리용 명령으로 DB에 넣는다 | 화면 없이 DB 한 곳에서 관리 | 운영 절차가 필요하고 실수 위험이 있다 |

- **지정 전후 기록**(2026-11-01 결정): 지정 전에 남긴 평가 · 안건 · 코멘트는 지우지 않는다. 지정 뒤에는 강사 정책을 적용한다. 해제 뒤에도 과거 강사 답변 · 피드백은 보존한다. 해제할 때 학생 자격(그 레포 콜라보레이터)이 없는 계정에 학생 권한을 자동으로 주지 않는다.
- **권한 상승 방지**: 레포 관리자와 강사가 같은 사람인 경우는 운영상 없다고 전제한다(2026-11-01 결정). 그래도 서버가 막는다.
  - **확정**(2026-11-01 추가 결정): 한 사무실에서 한 사람의 역할은 하나로만 정한다. 강사로 지정되면 그 사무실에서는 강사 정책만 적용하고, 레포 관리자 · 참여자의 쓰기 권한을 함께 주지 않는다.
  - 레포 관리자가 자기 자신을 강사로 지정하는 요청은 거부한다(허용 목록 계정은 레포 관리자로 판정되지 않으므로 같은 결과다).
- GitHub App 권한은 Metadata, Contents, Issues, Pull requests를 모두 **읽기 전용**으로 둔다.
- 앱은 조마다 조직에 설치한다. 레포 등록은 학생 기준 자기 조 조직 안의 레포만 받는다(P2). 강사는 자신이 강사로 지정된 모든 조의 사무실을 본다(FR-AUTH-07).
- 토큰 · 쿠키 · 이메일을 로그에 남기지 않는다. 일부를 가린 값도 남기지 않는다(NFR-04 · GRAVIT CONTRACTS.md 「6」).
- 키 파일(`*.pem`)은 `./secrets/` 에만 둔다. **데모 저장소의 `.gitignore`에는 `.env` · `*.pem` · `secrets/`가 아직 없다** — 개발 저장소에서 먼저 추가한다(환경 변수 가이드).

**보안 심사용 로그인** (2026-11-01 추가 결정 — FR-AUTH-01에 연결, 새 ID 아님, P15):

- 목적: 강사님이 본인 GitHub 계정 없이 인증 · 인가 · 권한 상승 · IDOR 등을 점검한다.
- 계정: 실제 GitHub 계정이 아닌 앱 내부 계정(`ReviewAccount`). 권장 구성은 ROLE-01 2개 · ROLE-02 1개 · ROLE-04 1개.
- 범위: **시연 사무실(Demo Workspace)에만** 접근한다. 실제 레포의 데이터에 접근하지 않고 GitHub 권한을 가진 것으로 취급하지 않는다. 시연 사무실은 Seed 데이터라 GitHub를 부르지 않는다.
- 처리: Spring Security 안에서 처리한다 — `[제안]` 폼 로그인 + `PasswordEncoder`. 별도 인증 서버 · JWT는 만들지 않는다. 로그인 뒤에는 GitHub 로그인과 **같은 세션 · CSRF · 인가 · 리소스 접근 검사**를 받는다. 테스트 계정이라는 이유로 검사를 건너뛰지 않는다.
- 비밀 값: 비밀번호는 안전한 해시로만 저장하고, 비밀번호 · 토큰을 저장소에 커밋하지 않는다. 계정을 넣는 방식(서버 설정 · 배포 때 시드)은 구현 때 정한다.
- 켜기 · 끄기: 명시적으로 설정한 배포 환경에서만 켜진다(기본은 꺼짐 — 환경 변수 가이드). 꺼지면 로그인 경로도 없다. 심사가 끝나면 끄거나 계정을 쓸 수 없게 한다.
- `[제안]` 반복 실패 제한은 구현 때 검토한다.

**시연 사무실 접근** (2026-11-01 추가 결정 · P2): 명시적으로 등록한 대상만 들어간다 — 우리 조의 실제 GitHub 계정, 그 시연 사무실에 지정된 강사 계정, 발급한 심사용 계정. GitHub로 로그인만 한 일반 사용자는 들어갈 수 없다(`not_found`). 우리 조 계정의 역할은 `[구현 기본값]` 우리 조 실제 사무실의 역할을 따른다.

**운영 범위** (2026-11-01 추가 결정): v1의 실제 운영은 우리 조 사무실과 시연 사무실이다. 여러 사무실을 받는 구조(데이터 분리 · 레포 전환 · 강사의 여러 사무실 조회 · 공용 라이브러리 · 서버 측 접근 제어)는 유지하고, 외부 조직 자동 가입 · 온보딩 · 운영자 대시보드 · 승인 워크플로 같은 범용 운영 기능은 만들지 않는다.

## 저장소 (JPA)

- **현재 구현 (데모, 참고만)**: `server/src/db.ts`가 `node:sqlite`로 `server/data/office.db`를 열고, 테이블마다 `(id TEXT PRIMARY KEY, data TEXT)`로 엔티티 전체를 JSON 문자열로 저장한다. 쓰기는 `repo.ts`의 `mutate()` 트랜잭션, 커밋 뒤 SSE 알림. 2026-10-28에는 이 방식을 이어 쓰기로 했지만, **2026-11-01 결정으로 Spring Data JPA로 바꿔 이 방식은 쓰지 않는다.**
- **v1 (신규 예정)**: 06-data.md 「저장 항목」을 JPA Entity로 만든다. Entity · 연관관계 · ID 정책 · 무결성 제약은 CONTRACTS.md 「2」 · 「3」(v1 확정 — 2026-11-03).
  - 기본키는 `Long`(MySQL `BIGINT`)이고 API에서는 문자열로 낸다. GitHub 이슈 · PR 번호와 사용자 · 레포 ID는 다른 칸에 둔다(2026-11-03 결정 — Q1).
  - DB는 MySQL 8.x다(2026-11-01 추가 결정).
  - 사무실 단위 항목은 모두 사무실(Workspace)에 속한다. 조회는 항상 사무실 조건을 포함한다 — 다른 조 데이터가 섞이지 않게 하는 핵심이다.
  - 컨벤션 라이브러리 항목 · 심사용 계정은 서버 전체에 하나다. 승인된 강사 계정은 서버 설정 허용 목록이다(2026-11-01 추가 결정).
  - 쓰기의 트랜잭션은 Service 메서드 단위다. SSE 알림은 커밋이 끝난 뒤에 보낸다(데모 원칙 유지 — `[제안]` 트랜잭션 커밋 뒤 이벤트).
  - 스키마 변경은 migration 도구로 관리한다(Flyway — GRAVIT 골격에서 정함). 운영에서 JPA가 스키마를 자동으로 바꾸게 두지 않는다 `[제안]`.
  - JPA 주의: 지연 로딩과 N+1 조회, 양방향 연관관계는 꼭 필요한 곳만, 목록 API의 페이징은 계약이 정한 곳(학습 타임라인 · 활동 기록)만 쓰고, 정렬은 계약에 적힌 고정 순서를 따른다(GRAVIT CONTRACTS_API.md) — 코드 컨벤션 (BE) 「저장」.
  - 시각은 UTC로 저장 · 응답한다. GitHub에서 온 값은 `syncedAt`을 같이 둔다.
  - 저장 구조를 바꾸면 DB schema 변경이므로 PR에 위험 범위로 적는다.

## 공간 역할과 테마

**확정 (2026-11-01 결정)**: 비즈니스 로직과 시각적 테마를 분리한다. 사무실(책상 · 선반 · 회의실)은 기능을 표현하는 기본 테마일 뿐이고, 앞으로 아기자기한 3D 마을 · 캐주얼 로우폴리 공간 등으로 바뀔 수 있다. 기존 게임의 에셋 · 캐릭터는 복제하지 않는다. 테마가 바뀌어도 팀원 표시, 이슈 · PR 표시, 상태별 공간 배치, 요구사항 연결, 회의 안건, 도움 요청, 진행 상태, 학습 타임라인, 기술 결정 카드, 강사 피드백은 그대로다.

### 공간 역할 ↔ 기본 테마 표현

| 공간 역할 | 들어가는 것 | v1 기본 테마(사무실) 표현 | 다른 테마 예 (참고) |
| --- | --- | --- | --- |
| 작업 공간 (팀원마다) | 팀원 아바타, 담당자 있는 열린 이슈 | 책상 + 아바타 | 집 · 작업대 |
| 대기 | 담당자 없는 열린 이슈 | 백로그 선반 | 창고 |
| 검토 | 열린 PR | 검토 영역 | 우체통 · 검사대 |
| 완료 | 닫힌 이슈 · 머지된 PR | 완료 선반 | 진열장 |
| 회의 | 회의 안건(`AgendaItem`)의 표현 | 회의실 테이블 | 광장 · 모닥불 |
| 게시 | 도움 요청 · 회의 결정 기록 · 강사 피드백 · 배운 점 공유 | 게시판 오브젝트 (내용은 HUD) | 마을 게시판 |
| 요구사항 영역 | 요구사항(판 또는 대기 칸) | 바닥 구역 | 밭 · 구획 |

도형 종류(이슈 상자 · PR 삼각기둥 · 도움 요청 구 · 배운 점 공유 팔면체 · 기타 메모지)도 기본 테마의 표현이다. 요구는 "종류를 구분해 보인다"이다.

### 계층 (`[제안]` — 구조 방향은 2026-11-01 결정, 세부는 제안)

```text
도메인 상태 (API 응답 · store)       이슈 · PR · 평가 · 안건 · 도움 요청 … — 테마를 모른다
      │  순수 함수
      ▼
SceneModel                            객체마다 { id, kind, placeRole, badges, highlight, groupKey }
      │                               placeRole 예: backlog · desk:<memberId> · review · done · meeting · board · requirement:<id>
      ▼
ThemeLayout (테마 하나 = 구현 하나)    공간 역할 → 기준 좌표(anchor) · 드롭 영역(dropZone) · 이동 경로
      │
      ▼
R3F 렌더러 (테마 컴포넌트)            실제 3D 모델 · 재질 · 애니메이션
```

- **처음에는 테마 하나(사무실)만 구현한다.** 테마 엔진 · 테마 선택 화면 · 런타임 테마 교체는 만들지 않는다(2026-11-01 결정). 목표는 "테마를 바꿀 때 도메인 로직을 고치지 않는다"까지다.
- 데모와의 차이: 데모는 `src/scene/slots.ts`가 위치 계산 로직을 가지면서도 `Furniture.tsx`(`BACKLOG_CENTER_X` · `BACKLOG_LEVELS` · `DONE_TOP` · `TABLE_TOP_Y`)와 `Desks.tsx`(`DESK_TOP_Y` · `deskLocalToWorld`)에서 상수를 가져온다. 그래서 위치 로직이 가구 컴포넌트에 묶여 있다.
  - `dropTarget.ts`도 책상 · 선반 · 회의 테이블 좌표를 직접 갖고 있다.
  - `[제안]` 공간 역할 계산은 SceneModel로, 좌표 · 드롭 영역은 ThemeLayout으로 옮긴다.
  - 드롭 결과는 "어느 공간 역할에 놓였나"로만 해석한다 — 요구사항 영역이면 연결, 회의면 안건 만들기, 그 밖은 제자리로.
- 테마를 바꿀 때 드는 일:
  - 색 · 조명 · 재질만 바꾸기: `theme.ts` 팔레트 · 재질 캐시
  - 가구 · 아바타 모양 바꾸기: 테마 컴포넌트를 교체한다. 외부 3D 모델을 쓰면 에셋 준비 · 라이선스 · 용량 확인이 새로 필요하다(데모는 코드로 만든 기본 도형뿐이다)
  - 공간 배치 바꾸기: ThemeLayout(`layout.ts` · `paths.ts` 이동 경로 · 드롭 영역)
  - 상호작용 방식 바꾸기: 카메라 · 드래그 방식. 이건 기획 결정이 필요하다

## 요구사항 묶음 표시 — 최소 설계 (`[제안]`)

**확정 (2026-11-01 결정)**:

- 요구사항이 많아지면 비슷한 조건의 항목을 대표 오브젝트 하나로 묶어 보인다. 묶인 항목 수를 표시하고, 선택하면 내부 목록과 상세를 보인다.
- 명세 문서의 `##` 제목 하나 = 요구사항 하나는 그대로다(TBD-01).
- 묶음은 **시각화 방식일 뿐**이다. 요구사항 · 이슈의 ID와 상태, `RequirementLink`, 진행률은 원본(요구사항) 단위로 계산한다.

**바꿈 (2026-11-06 결정 — C 요청)**: 요구사항 종류로 묶지 않는다(2026-11-01 추가 결정을 바꿨다). 판 수를 active 요구사항 수로 정하고, 판에 다 못 올린 요구사항은 **대기 칸** 하나에 모은다. 요구사항 종류는 이름표 · 대기 목록 분류 · 정렬에만 쓴다. 우리 규모(요구사항 10개 안팎)에서는 종류별로 묶으면 개별 요구사항과 빈 구역이 오히려 안 보이기 때문이다. 근거는 결정 기록 「요구사항 구역 표시 변경 (2026-11-06)」.

아래 표의 나머지는 `[제안]` · `[구현 기본값]`이다. 판 수 · 시각 효과는 구현 중 조정한다.

| 항목 | 최소 설계 |
| --- | --- |
| 계산 위치 | SceneModel 계산(프론트)의 표시용 값. 서버에 저장하지 않는다. 입력은 Snapshot과 대기 칸 교환 결과(아래) |
| 판 수 | `[구현 기본값]` active 요구사항 0\~5개 → 판 5장(남는 판은 빈 판), 6\~10개 → 요구사항 수만큼, 11개 이상 → 판 10장 = 표기용 9장 + 대기 칸 1장. 숫자는 SceneModel 상수 한 곳 |
| 판 순서 | 기본은 명세 문서 순서(`position`). 교환 결과가 있으면 그 요구사항들을 먼저 판에 놓고 남는 판을 `position` 순서로 채운다. 새 요구사항은 남는 판이 없으면 대기 칸으로 간다 |
| 교환 결과 | 판에 올린 요구사항 ID 목록(`string[] \| null`). 사무실별로 보는 사람의 브라우저에 저장한다 — 서버 저장 · 새 API 없음. 스토어가 내주고 SceneModel의 판 배치가 읽는다 |
| 교환 결과를 버리는 때 | 교환한 요구사항이 `missing` · `needs_review`가 됨, active가 10개 이하로 줄어듦, 브라우저 저장소를 읽지 못함 → 버리고 `position` 순서. 보는 사람마다 배치가 다를 수 있다 — 대기 목록의 "기본 순서로"로 맞춘다 |
| 요구사항 종류 | `##` 제목 앞의 요구사항 ID 접두사(예: `FR-AUTH-…` → "인증 · 권한", `FR-GH-…` → "GitHub 연동", `FR-LRN-…` → "학습 · 평가"). 함수 하나 `groupKeyOf(requirement)`로만 정하고, 접두사 → 이름 대응은 상수 한 곳. 접두사가 없으면 "미분류". 이름표 · 대기 목록 분류 · 정렬에만 쓴다 — 묶음 기준이 아니다 |
| 영역 라벨(`area:*`) | 보조 정보 — 대기 목록의 표시 · 검색 · 필터에 쓴다 |
| 대기 칸 | 화면 키 `requirement-group:waiting` · `kind: 'requirementGroup'` · `placeRole: 'requirement:waiting'` · `refId: null`. 배지 `waiting:N`(판에 못 올린 수) · `empty:k`(그중 연결 이슈 0, k > 0일 때만) |
| 선택 | 대기 칸을 고르면 HUD에 대기 목록(각 ID · 진행률 · 빈 구역 표시)이 열리고, 고른 요구사항을 판 하나와 자리 교환한다 |
| 드래그 연결 | 판에 놓으면 그 요구사항과 연결. 대기 칸에 놓으면 대기 목록에서 요구사항을 골라야 연결된다(연결은 항상 요구사항 하나와). 빈 판은 드롭 구역이 아니라 아무 일도 없다 — 드롭 빛은 요구사항이 있는 판과 대기 칸에만. 상자는 제자리로 돌아간다 |
| 진행률 | 요구사항마다 기존 규칙(FR-REQ-05) — 판마다 `doneTaskCount/linkedTaskCount`(예: 1/3)와 빈 구역 표시. 묶음 단위 지표는 없다 |
| 담당 | C — SceneModel의 대기 칸 엔티티 · 판 배치, 3D 판 · 대기 칸 · 드롭 판정 · 드롭 빛 · 교환 연출, 대기 칸 클릭 → 대기 목록을 여는 스토어 액션 호출. D — 대기 목록 HUD · 교환 UI · "기본 순서로", 교환 결과 저장(스토어 · 사무실별 브라우저 저장)과 버리는 규칙, `linkRequirement` · `unlinkRequirement`, 요구사항 패널 |

## 기존 데모와의 대응

> 프론트엔드는 데모 코드를 재사용 · 리팩터링한다(2026-11-01 결정). 아래 경로는 데모 저장소 기준이다. 서버 파일은 참고만 한다.

### 3D 장면

| 기능 | 기존 파일 | 판정 | 필요한 수정 | 관련 요구사항 |
| --- | --- | --- | --- | --- |
| 사무실 공간 · 벽 · 바닥 · 조명 | `src/scene/Room.tsx` · `Lighting.tsx` · `Furniture.tsx` · `src/office/layout.ts` | 재사용 | 기본 테마(사무실) 컴포넌트로 둔다. `layout.ts`의 좌표는 ThemeLayout의 공간 역할 기준 좌표로 옮기고, 요구사항 영역(구역 · 묶음)을 놓을 자리를 더한다 | FR-OFF-01 |
| 책상 · 아바타 | `Desks.tsx` · `Avatar.tsx` · `Avatars.tsx` | 수정 · 확장 | 책상은 10개 고정(`MAX_MEMBERS = 10`)이라 4인 팀에 충분하다. 팀원 → 책상 배정은 GitHub 콜라보레이터 기준으로 자동 배정. 아바타는 기본 작업 공간, 회의 같은 앱 이벤트에 따라 이동(2026-11-01 결정 — `paths.ts` 이동 경로 재사용). 직접 조작은 Must 아님. 강사는 책상 · 아바타가 없다. GitHub 프로필 사진 표시(FR-OFF-06) 신규. 머리 위 신호 다이아 · "업무량 140%" 태그는 사람별 비교라 제거 대상 | FR-OFF-01 · 06, FR-FLOW-06 |
| 작업 상자 | `TaskBox.tsx` · `TaskBoxes.tsx` · `TaskTooltip.tsx` | 수정 · 확장 | 둥근 상자 한 종류뿐이다. 높이 = 포인트(`estimate`)는 뺀다. 색은 `priority:*` 라벨에서, 라벨이 없으면 중립(2026-11-01 결정 · P6) | FR-OFF-02 · 04 |
| PR 도형(삼각기둥) | 없음 | 신규 | `shared.tsx`의 기본 도형 패턴으로 만든다. 새 3D 모델링은 하지 않는다 | FR-OFF-02, FR-LRN-03 |
| 안건 도형 5종(상자 · 삼각기둥 · 구 · 팔면체 · 납작한 메모) | 없음 — 상자 1종만 있다 | 신규 | 이슈 상자를 빼면 4종이 새 도형이다(기본 테마 표현). 도형 5종이 이미 있다고 가정하지 않는다 | FR-OFF-02, FR-LRN-10 |
| 위치 배치 | `slots.ts` (`home`: `desk` · `backlog` · `done` · `agenda`) | 구조 변경 | SceneModel(공간 역할)과 ThemeLayout(좌표)로 나눈다 — 「공간 역할과 테마」. 검토 공간(지금 없음)을 더한다. **`agenda`는 원본 상자를 옮기는 위치가 아니라 `AgendaItem` 표현의 위치로 바꾼다**(데모는 `onAgenda`면 상자 자체를 회의 테이블로 옮긴다 — 2026-11-01 결정과 다름). 완료 선반은 최근 16개만 그린다. 과부하 책상 기울기(`wobble`)는 제거 대상. 담당자가 여럿인 이슈는 대표 담당자 책상에 하나만 놓는다(2026-11-01 추가 결정) | FR-OFF-02, FR-MTG-03, FR-FLOW-06 |
| 백로그 · 완료 선반 | `Furniture.tsx`(`BacklogShelf` · `DoneShelf`) · `slots.ts` | 재사용 | 대기 = 담당자 없는 열린 이슈, 완료 = 닫힌 이슈 · 머지되거나 닫힌 PR(05 「상태값」 표 — FR-LRN-07). `[구현 기본값]` 머지 안 되고 닫힌 PR은 미병합 종료 표시, Draft PR은 검토 영역에 Draft 배지, 닫힌 이슈는 닫힘 사유로 완료 · 종료 구분 | FR-OFF-02 |
| 회의실 | `MeetingRoomState.tsx` · `Room.tsx` | 수정 · 확장 | 회의 중 표시 · 안건 수 라벨 · 드롭 강조는 재사용. 안건은 지금 `Task.onAgenda` 하나뿐이라 종류가 없다 → `AgendaItem`의 표현을 5종 도형으로 놓는다. 원본 이슈 · PR은 회의실로 옮기지 않는다(2026-11-01 결정). 안건 노출은 21개까지 | FR-MTG-03 · 04, FR-LRN-04 · 10 |
| 요구사항 구역 · 연결 | 없음 | 신규 | 판 · 대기 칸 · 판별 진행률 · 상자를 판으로 드래그(ROLE-02). 연결 뒤 상자는 제자리로(2026-11-01 결정) | FR-REQ-02 · 04 · 05 |
| 막힘 · 리뷰 대기 표시 | 비슷한 것: `TaskBox`의 지연 깜빡임, `Overlays.tsx`의 업무량 원판 | 수정 · 확장 | 막힘은 강조하고 "막힘" 표시만 붙인다 — 이유 문구 없음(FR-FLOW-02). 업무량 원판(사람별)은 제거 대상 | FR-FLOW-01 · 02 |
| PR 평가 표시 | 없음 | 신규 | 삼각기둥 위 현재 회차의 추천 · 비추천 수와 이유. PR 위치는 바꾸지 않는다 | FR-LRN-01~03 · 06 |
| 공용 게시 공간 | 없음 | 신규 | 3D에는 게시판 오브젝트(개수 배지)만, 내용(회의 결정 기록 · 강사 피드백 · 도움 요청 · 배운 점)은 HUD 패널(2026-11-01 결정). 긴 글을 3D 텍스트로 그리지 않는다. 배지는 열린 · 논의 중 도움 요청 수(`help:N`)와 그중 48시간 안건 후보 수(`candidate:k`)만 센다 — 결정 기록 · 강사 피드백 · 배운 점은 세지 않는다. 고르면 HUD의 도움 요청 목록을 열고, 결정 기록 · 강사 피드백을 보는 HUD가 생기면 그것도 연다(2026-11-06 결정) | FR-MTG-04, FR-LRN-15~22 |
| 정보 뷰(최근 변화 · 담당 · 막힘) | 비슷한 것: `TopBar`의 `OverlaySwitch`와 `Overlays.tsx` | 수정 · 확장 | 오버레이 전환 구조를 재사용하고 정보 뷰 내용은 새로 구현한다. 업무량 오버레이는 제거 대상 | FR-FLOW-03 · 07 · 08 |
| 카메라 회전 · 줌 · 이동 | `CameraRig.tsx` | 재사용 | — | FR-OFF-01 |
| 도형 드래그 | `DragLayer.tsx` · `dropTarget.ts` · `store.ts` `endDrag` | 구조 변경 | 드롭 판정 박스를 ThemeLayout의 드롭 영역으로 옮긴다. 드롭 결과는 요구사항 영역(연결 — 상자는 제자리) · 회의(안건 만들기)만 남긴다. 담당 · 상태 · 마감은 드래그로 바꾸지 않는다(2026-10-28 추가 결정). 완료 상자는 드래그가 막혀 있다 | FR-REQ-04, FR-LRN-04 · 10 |
| 상세 패널 연동 | `src/ui/Detail.tsx` · `RightPanel.tsx` | 수정 · 확장 | 선택 → 패널 구조는 재사용한다. 편집 입력란(상태 · 우선순위 · 포인트 · 담당)은 읽기 전용 + GitHub 링크로 바꾼다. 담당자는 전원을 보인다. 마감은 앱 저장 값이고, 상세 패널 또는 일정판에서 레포 관리자와 그 이슈의 담당자만 입력한다(P14 — 2026-11-01 추가 결정) | FR-OFF-05, FR-PLAN-03 |
| 위치 이동 애니메이션 | `TaskBox.tsx`(호 그리며 이동 · 등장 · 흔들림) | 재사용 | 흔들림(과부하)만 제거 | FR-OFF-02 |

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

기획안 「2주 개발 일정」의 순서와 우선순위를 바꾸지 않는다. Must 범위도 줄이지 않는다(2026-11-01 결정). 이슈별 구현 순서(P0 · P1 · P2)는 00-backlog.md 「이슈 백로그 (Must)」에 있다 — 순서일 뿐 범위 축소가 아니다.

| 단계 | 목표 | 주로 바뀌는 곳 | 선행 | 규모 메모 |
| --- | --- | --- | --- | --- |
| 1차 공통 계약 · 골격 (1~2일차) | 데모 실행은 확인됨(2026-11-01). 공통 계약 확정(2026-11-03 — CONTRACTS.md v1), 개발 저장소(모노레포) 생성, Spring Boot 골격(Java 21 · Gradle · MySQL 8.x — 공통 에러 · 보안 설정 · ID 변환 · 사무실 Entity), 배포 플랫폼 확정, 프론트 데모 이전 | 문서 · 공통 파일. 4명이 함께 | 없음 — 스택 · DB(2026-11-01 추가 결정), 계약 · 저장소 구성(2026-11-03 결정) 확정 | 여러 Claude Code 세션이 서로 다른 계약을 만들지 않도록 이 단계를 먼저 끝낸다 — 아래 「착수 체크리스트」 |
| 2차 GitHub 이슈를 사무실에 (5일차 첫 목표) | GitHub 로그인 · 세션, 설치 토큰으로 이슈 · PR 읽기, 앱 모델로 변환 · 저장, `DataSource(api)`를 Spring에 연결, 상태에 따른 위치(SceneModel · ThemeLayout) | 백엔드 `auth` · `github` · `sync` · `workspace`, 프론트 `types.ts` · `slots.ts` → SceneModel · `store.ts` | 1차, GitHub App 생성 (FR-GH-02 · 04는 2026-11-01 추가 결정으로 채택) | 가장 큰 단계. 로그인 없이 설치 토큰만으로 먼저 이슈를 띄우고 로그인을 나중에 붙이는 순서도 가능하다 — 결정 필요 |
| 3차 기획 핵심 확장 | 요구사항 구역 · 묶음과 진행률, PR 시각화 · 평가 · 회차, 회의실 안건 · 결정 기록, 도움 요청, 강사 대응, 회고 리포트, 시연 사무실 · 심사용 로그인(IB-69) | 백엔드 도메인 패키지, 프론트 장면 도형 · 회의 화면 · 새 패널 | 2차, 명세 문서 형식(TBD-01 — ## 제목만) | 7일차 데모의 PR 평가(FR-LRN-01~03)는 일정에 든다 |

### 착수 체크리스트 (1차 단계 — 2026-11-01 추가 결정)

기획 전면 검토는 끝났다. 아래 순서로 개발에 들어간다.

1. ~~**공통 계약 확정**~~ — 완료(2026-11-03 결정 — CONTRACTS.md v1 · CONTRACTS_API.md). ID 형식 · 저장소 구성도 이때 닫았다. 문서 담당자가 GitHub에 반영하고 기준 커밋을 팀에 공유한다 — 팀원 전원 리뷰는 착수 조건이 아니다(CONTRACTS.md 「9」).
2. **개발 저장소 생성(모노레포)** — 새 저장소를 만들고 `.gitignore`에 `.env` · `*.pem` · `secrets/`를 가장 먼저 넣는다. Git 전략 「8」 설정(보호 규칙 · squash · 템플릿 · 라벨 · `.nvmrc` · JDK 21)을 한다.
3. **모노레포 초기 구조** — 위 「개발 저장소」 트리대로 `frontend/`(데모 프론트 이전) · `backend/` · `contract/examples/`(CONTRACTS_API.md 예시 JSON) · `compose.yaml`(로컬 MySQL 8.x) · `.env.example`을 만든다.
4. **Spring Boot 골격** — Java 21 · Gradle · MySQL 8.x로 만들고, Spring Boot 버전 · migration 도구 · 테스트 DB · 배포 플랫폼 · SPA 서빙 방식을 이때 정한다. 공통 에러 응답 · 보안 설정(세션 · CSRF · 401 JSON) · ID 문자열 변환 · 인가 서비스 틀 · 사무실 Entity를 먼저 넣는다(CONTRACTS.md 「6」 · 「2」).
5. **공통 Entity · DTO · 인증 · 인가 기초** — CONTRACTS.md 「2」 Entity와 CONTRACTS_API.md 공통 타입 · Snapshot DTO 뼈대, 로그인 · `GET /api/me` · 사무실 접근 검사, 계약 테스트 틀(CONTRACTS.md 「9」)을 먼저 만든다. 코드는 Git 전략대로 PR로 합치고, PR 본문에 기준 계약 커밋을 적는다.
6. **GitHub App 생성 · 연동** — 읽기 전용 권한(Metadata · Contents · Issues · Pull requests)으로 만들고 우리 조 조직에 설치한다. 설치 토큰 · 동기화(B2)를 붙인다.
7. **GitHub 이슈 생성과 담당 배분** — 00-backlog.md 「이슈 백로그 (Must)」로 이슈를 만들며 담당자를 정한다. B12 · B13 · B14처럼 지금 미정인 묶음도 **병렬 기능 개발 전에는 담당자와 작업 경계를 지정**한다(BACKEND_ASSIGNMENTS.md).
8. **병렬 개발 시작** — 모두 같은 계약 커밋을 기준으로 한다. 계약을 바꿀 일이 생기면 문서 담당자에게 요청하고, 담당자가 고친 뒤 새 커밋과 바뀐 절을 관련 담당자 · Claude Code 세션에 공유한다(CONTRACTS.md 「9」).

디자인 테마는 팀원들과 따로 정한다. 지금의 사무실 테마는 기본 테마일 뿐 최종 디자인이 아니고, 테마 결정은 위 순서를 막지 않는다 — 테마가 바뀌어도 SceneModel · 도메인 로직은 그대로이고 ThemeLayout · 테마 컴포넌트만 바뀐다(「공간 역할과 테마」).

## 공용 컴포넌트

여러 화면이 쓰는 화면 조각이다. 3D 표현은 기존 데모의 표현을 재사용하고 새 모델링은 하지 않는다(기획안 「기능 동결 규칙」). 기존 경로는 데모 기준이다.

### 상단 바

기존 `src/ui/TopBar.tsx`. 레포 드롭다운, D-day, 요구사항 진행률, 열린 PR 수, 평균 리뷰 대기, 동기화 상태, "기록되지 않은 기여는 반영되지 않습니다" 안내, 팀 설정 버튼(레포 관리자만 수정 — 2026-11-01 결정), 시연 사무실이면 "시연 데이터" 표시 — 07-screens.md 「상단 바」. 지금은 KPI 칩 · 균형 칩 · 회의 버튼 · 오버레이 전환이 있고, 레포 선택 · D-day · 동기화 상태 · 설정 버튼은 없다(신규). 3D보다 먼저 뜬다(NFR-02 — 이미 lazy 로딩).

### 상세 패널

기존 `src/ui/Detail.tsx`(`RightPanel.tsx`의 서랍). 도형 · 아바타를 선택하면 제목, 담당자, 연결 PR, 리뷰 상태, 우선순위, 마감, 현재 회차의 추천·비추천과 이유, 평가 버튼, GitHub PR 화면 링크를 보여 준다 — 07-screens.md 「상세 패널」 · FR-OFF-05.

### 도형 5종 (기본 테마 표현)

이슈는 정육면체 상자, PR은 삼각기둥, 도움 요청은 구, 배운 점 공유는 팔면체(보석 모양), 기타는 납작한 메모지다. 2D 팀 보드 아이콘은 ■ ▲ ● ◆ 모서리 접힌 메모로 1:1 대응한다. 도형은 종류만 나타내고, 상태는 도형을 바꾸지 않고 배지 · 테두리 · 크기로 표시한다(기획안 「안건 도형과 상태 표시」). **지금 있는 것은 상자(`src/scene/TaskBox.tsx`) 하나뿐이다.** 공유 도형 · 재질 캐시는 `src/scene/shared.tsx`에 있다. 테마가 바뀌면 모양은 바뀔 수 있고, "종류를 구분해 보인다"는 요구만 남는다.

### 정보 뷰

최근 변화 · 담당 · 막힘 뷰 세 가지를 같은 사무실 위에 겹쳐 보여 준다(2026-10-29 결정). 최근 변화 뷰(FR-FLOW-08)는 가장 최근 스탠드업 날 이후 상태가 바뀐 도형을 밝게, 그대로인 도형을 흐리게 보인다. 담당 뷰는 몰린 사람 책상과 담당자 없는 도형을, 막힘 뷰는 막힌 도형 · 아바타를 강조한다. 담당 · 막힘 표시에는 이유 문구와 숫자 비교가 없다. 요구사항 진행률 · 빈 구역은 상단 바와 요구사항 패널, 추천 리뷰어는 상세 패널, 마감 · 뒤처짐은 일정판에서 본다. 일정상 밀리면 패널 목록으로 대신한다(기획안 「정보 뷰」 · 「기능 동결 규칙」). 기존 오버레이 전환(`TopBar.tsx` `OverlaySwitch`, `store.ts`의 `overlay`, `src/scene/Overlays.tsx`)을 틀로 쓴다.

## 디자인 토큰

기존 값은 세 곳에 있다. v1에서 쓸 토큰은 이 파일들을 기준으로 정리한다. 테마는 확정이 아니므로(2026-11-01 결정) 3D 색 · 재질은 테마 쪽에, HUD 토큰은 HUD 쪽에 둔다.

- `src/theme.ts` `PALETTE` — 3D 색(배경 · 바닥 · 벽 · 가구 · 강조색 `accent` · 선택 · 드롭 가능 · 지연 강조). 공간은 흰색 계열이고 색은 데이터에만 쓴다는 원칙이 주석에 있다.
- `src/data/meta.ts` — 상태 · 우선순위 · 접속 상태 라벨과 색, 팀원 색 목록 `MEMBER_COLORS`, 업무량 등급 `WORKLOAD_LEVELS`(사람별 비교라 제거 대상).
- `src/index.css`의 CSS 변수와 `src/ui/*.css` — HUD 색 · 레이아웃.

### 색

색은 사람이 아니라 **우선순위**에 부여한다 — 화면이 사람 비교처럼 보이지 않게 하기 위해서다(P6). 우선순위는 GitHub `priority:*` 라벨에서 읽고, 라벨이 없으면 중립 색이다(2026-11-01 결정). 요구사항 구역별 색은 두지 않는다(2026-10-28 추가 결정 — FR-OFF-03은 2026-11-01에 삭제). 안건 종류는 색으로 구분하지 않는다(도형으로 구분). 지금 데모는 상자 색을 우선순위 · 프로젝트에, 아바타 색을 팀원(`Member.color`)에 준다. `[구현 기본값]` 아바타 색은 팀원 구분 용도로 자유롭게 정한다 — 사람끼리 비교하는 표시가 아니므로 P6과 충돌하지 않는다(2026-11-01 추가 결정).

### 글자 크기

TODO: 대응 내용 없음 (삭제 여부 확인)

### 모서리 · 그림자

TODO: 대응 내용 없음 (삭제 여부 확인)

### 간격

TODO: 대응 내용 없음 (삭제 여부 확인)

### 한 번만 쓰는 값

TODO: 대응 내용 없음 (삭제 여부 확인)

## 렌더링 방식

브라우저에서 모두 그리는 SPA다(Vite + React, 서버 렌더링 없음). Spring Boot 서버는 `/api/**`(REST · SSE)를 응답한다. 빌드 결과를 Spring이 정적 파일로 같이 서빙할지, 리버스 프록시 뒤에 따로 둘지는 결정 필요다. `[제안]` 같은 출처로 서빙하면 세션 쿠키 · CSRF · CORS가 단순해진다. 외부 인터넷 배포라 HTTPS로만 받는다(SP11). GitHub 토큰과 비밀 값은 서버에만 두고 브라우저로 내보내지 않는다(NFR-04). 상단 바와 패널이 3D보다 먼저 뜨고 3D는 이어서 로딩한다(NFR-02).

## 이미지

TODO: 대응 내용 없음 (삭제 여부 확인)

## 새 화면 만들기

기존 데모의 흐름을 따른다. 화면 목록과 보이는 것은 07-screens.md, 담당은 [SCREEN_ASSIGNMENTS.md](./SCREEN_ASSIGNMENTS.md)를 따른다.

1. 필요한 데이터가 `src/data/types.ts`와 [CONTRACTS.md](./CONTRACTS.md)에 있는지 본다. 없으면 타입 · DataSource · API DTO 변경을 먼저 합의한다(계약 파일).
2. 화면 계산(공간 역할 · 강조 · 묶음)은 `src/` 의 순수 함수(SceneModel · selectors)에 둔다. 도메인 판정(막힘 · 권한 · 집계)은 서버 응답을 쓴다. 화면에서 직접 계산하지 않는다.
3. 화면 상태는 `src/store.ts`(데이터 · 선택 · 드래그)와 `src/ui/uiState.ts`(탭 · 패널 같은 UI 전용 값) 중 맞는 곳에 둔다.
4. HUD는 `src/ui/`, 3D는 `src/scene/`에 만든다. 테마에 묶인 좌표 · 모양은 ThemeLayout · 테마 컴포넌트에 둔다. 3D 쪽은 첫 화면 번들에 들어가지 않게 lazy 경로 안에 둔다(`npm run build`의 번들 검사).
5. mock(`?source=` 없이)과 api(`?source=api` — Spring 서버) 두 경로에서 확인하고, 필요하면 E2E에 시나리오를 더한다.

## 기획 문서와 다르게 구현했다면

TODO: 대응 내용 없음 (삭제 여부 확인) — 구현이 기획 문서와 달라졌을 때 어디에 어떻게 기록할지 기준 문서에 정한 바가 없다. 정해지면 이 절에 쓴다.

## 플랫폼 제약

v1은 **외부 인터넷에서 접속하는 웹 서비스**이고(SP11 · 2026-11-01 결정) **1024px 이상 노트북 · 태블릿**에서 쓴다(NFR-07). 모바일 반응형은 범위 밖이다.

- GitHub 변경은 웹훅 없이 **폴링**으로 받는다(CON-03). 그래서 서버가 GitHub에서 들어오는 요청을 받을 필요가 없다.
- 연결이 끊겨도 마지막 데이터로 화면을 유지한다(NFR-09). 데모에는 이미 오프라인 배너와 재연결(`src/ui/Toasts.tsx` `ConnectionBanner`, SSE 재연결 1~10초 backoff)이 있다.

## 함수 리전

TODO: 대응 내용 없음 (삭제 여부 확인) — 서버 위치는 외부 인터넷 배포로 정했다(SP11 · 2026-11-01 결정 — TBD-09 "팀원 컴퓨터 한 대"를 대신함). 배포 플랫폼은 Spring Boot 초기 프로젝트 구축 단계에서 정하고(2026-11-01 추가 결정), 정해지면 쓴다.

## 아직 없는 것

- **개발 저장소** — 모노레포로 새로 만들기로 했고(2026-11-03 결정) 아직 만들지 않았다. 만든 뒤 Git 전략 「8」 설정을 한다.
- **구현 때 정하는 기술** — Spring Boot 버전 · migration 도구 · 테스트 DB · 세션 저장소 세부 · SPA 서빙 방식 · 배포 플랫폼(위 「기술 스택」). Java 21 · Gradle · MySQL 8.x는 2026-11-01 추가 결정으로 확정했다.
- **정의서 v0.1 원문** — 없을 가능성이 높다. Must 4건(FR-AUTH-02 · FR-GH-02 · FR-GH-04 · FR-MTG-02)은 팀 재정의로 대신했고 2026-11-01 추가 결정으로 채택했다(requirements-changes.md).
- **미결 TBD** — 서비스 정책(05-policy.md SP)에 남은 [?]는 없다. TBD-02(공개 범위)는 2026-11-03에 확정된 본인 · 강사 정책으로 정리했고(원문은 원본 확인 필요), TBD-09는 2026-11-01에 다시 정했다. TBD-03 · 07 · 08은 원본 확인 필요. 그 밖의 결정 필요 항목은 PRD.md 「열린 질문」.
- **제안 채택 여부** — 2026-10-28 · 2026-10-29 · 2026-10-30에 채택한 제안은 requirements-changes.md. 2026-11-01 결정은 새 ID 없이 기존 ID를 고쳤다.
