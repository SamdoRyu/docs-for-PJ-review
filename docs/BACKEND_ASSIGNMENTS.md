# Backend Assignments

MVP backend 구현 Issue 의 담당·상태 표다. 화면 구현은 [SCREEN_ASSIGNMENTS.md](./SCREEN_ASSIGNMENTS.md) 를 본다.

- **배정 기준: 실제 4명 재배분 (2026-11-03)** — **A** 총괄 · 기획 · 백엔드 일부(도움 요청 B13), **B** 백엔드 전담, **C** 프론트 3D, **D** 프론트 HUD · 데이터. 기획안 「역할 분담」의 이전 배정(B 연동 백엔드 · C 도메인 백엔드 · D 3D · 프론트)은 쓰지 않는다 — 이전 "B · C"(백엔드)는 지금 **B**(도움 요청은 **A**), 이전 "D"(프론트)는 지금 **C · D**다
- **담당의 기준은 개발 저장소 [SamdoRyu/GRAVIT](https://github.com/SamdoRyu/GRAVIT) Issue 본문**이다. 실제 GitHub 계정 지정은 사람 팀원 4명 중에서만 하고 Claude Code · AI 계정은 넣지 않는다
- 공통 계약의 기준은 GRAVIT `docs/CONTRACTS.md` · `docs/CONTRACTS_API.md`다(2026-11-03 기준 위치 전환). 이 저장소의 두 파일은 `796fc25`까지의 이력이다
- **백엔드 스택: Spring Boot(Spring MVC) · Spring Data JPA · Spring Security 세션**(2026-11-01 결정). 데모의 Hono 서버는 운영 백엔드가 아니고 동작 · API 설계만 참고한다
- git 흐름(브랜치·커밋·PR·머지): `.claude/rules/git-workflow.md`
- 모듈 경계: [ARCHITECTURE.md](./ARCHITECTURE.md) · 공통 계약(v1 확정 — 2026-11-03): [CONTRACTS.md](./CONTRACTS.md) · 엔드포인트 · DTO: [CONTRACTS_API.md](./CONTRACTS_API.md) · 실행·검증 명령: [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md) · Must 이슈 목록: [00-backlog.md](./00-backlog.md) 「이슈 백로그 (Must)」
- **기호 구분**: 이 표의 `B0`\~`B14`는 백엔드 담당 기호다(B8은 비어 있다 — 2026-10-30 결정). 00-backlog.md의 이슈 후보 번호는 `IB-01`\~`IB-68`로 따로 매긴다(IB-34\~39는 비어 있다 — 2026-10-30 결정) — "B2"는 GitHub 연동 담당 묶음이고, IB-02(GitHub 로그인 이슈)와 다르다.
- [제안]이 붙은 줄은 requirements-changes.md에서 "검토 중"인 요구사항이다(보류된 요구사항은 지금 없다 — FR-FLOW-07은 2026-10-29 결정으로 채택). 채택되기 전에는 GitHub 이슈를 만들지 않는다(00-backlog.md 「착수 조건」).

## 이 표를 믿는 범위

> **이 문서는 snapshot / cross-check 전용이다.** 착수 가능 여부는 이 표가 아니라 아래 순서로만 판정한다.
>
> 1. 배정 Issue **본문의 상태**와 "요구사항 ID" 칸
> 2. 「선행」의 Issue 마다, 그 PR 이 `origin/main` 에 squash merge 됐는지
> 3. 필요한 TBD 가 05-policy.md 서비스 정책에서 [?] 가 아닌지

- 1~3 을 모두 통과했는데 이 표가 막혀 있으면 작업을 막지 않고 "BACKEND_ASSIGNMENTS 가 GitHub Issue 와 불일치 — 문서 stale" 만 보고한다.
- 반대로 Issue 본문이 막혀 있으면 이 표가 열려 있어도 막힌 것이다.
- 선행은 Issue 가 open/closed 인지로 판단하지 않는다. PR 에 `Closes #N` 을 빠뜨리면 머지된 작업의 이슈가 open 으로 남을 수 있다.
- 브랜치 이름은 번호에서 추론하지 말고 Issue 에 적힌 값을 쓴다(`<type>/<이슈번호>-<slug>`).

## 담당 표

| Symbol | Issue | 제목 | Status | 선행 | 담당 | canonical branch | Downstream |
| --- | --- | --- | --- | --- | --- | --- | --- |
| B0 | TODO | TBD 9건 팀 결정 (TBD-01~09) · 공통 계약 확정(CONTRACTS.md) · 미확정 기술 결정 | decision-only | — | A 총괄 · 기획 | (없음) | 전체 |
| B1 | TODO | 계정·권한 — FR-AUTH-01~05 (FR-AUTH-02는 팀 재정의 — 2026-11-01 추가 결정으로 채택, FR-AUTH-05 원본 확인 필요) · Spring Security 세션 · 인가 서비스 · 강사 허용 목록 판정 · 보안 심사용 ID/PW 로그인(IB-69 — 2026-11-01 추가 결정, 작업량 증가) | TODO | B0 | B 백엔드 전담 | TODO | TODO |
| B2 | TODO | GitHub 연동 · 동기화 — FR-GH-01~06 (FR-GH-02 · 04는 팀 재정의 — 2026-11-01 추가 결정으로 채택, FR-GH-06 원본 확인 필요) | TODO | B3 | B 백엔드 전담 | TODO | TODO |
| B3 | TODO | 데이터 어댑터(GitHub · Seed(시연) · local) — NFR-08 · 사무실(Workspace) 분리 | TODO | B0 | B 백엔드 전담 | TODO | TODO |
| B4 | TODO | PR 상태 동기화 — GitHub 원본 상태(열림 · Draft · 머지 · 닫힘 · `headSha`)를 표시 상태로 (FR-OFF-02, FR-LRN-07) | TODO | B2 | B 백엔드 전담 | TODO | TODO |
| B5 | TODO | 보안 · 동기화 비기능 — NFR-04, NFR-05, NFR-09, NFR-10 · 외부 공개 배포(HTTPS · 쿠키 · CSRF · SSE 권한) | TODO | B1, B2 | B 백엔드 전담 | TODO | TODO |
| B6 | TODO | 요구사항 추적 — FR-REQ-01~07 | TODO | B2 | B 백엔드 전담 (이전 배정: C 도메인 백엔드) | TODO | TODO |
| B7 | TODO | 진행·막힘 — FR-FLOW-01~08 (FR-FLOW-07 · 08은 2026-10-29 결정으로 채택) | TODO | B2 | B 백엔드 전담 (이전 배정: C 도메인 백엔드) · 검증 IB-25는 A | TODO | TODO |
| B9 | TODO | PR 평가 · 평가 회차 · 회의실 안건 로직 — FR-LRN-01~14 (2026-10-28 채택) | TODO | B2, B4 | B 백엔드 전담 (이전 배정: C 도메인 백엔드) · 검증 IB-29는 A | TODO | TODO |
| B10 | TODO | 활동 기록 — FR-ACT-01~04 (2026-10-28 추가 결정으로 채택) | TODO | B1 | B 백엔드 전담 (이전 배정: C 도메인 백엔드) | TODO | TODO |
| B11 | TODO | 회고 리포트 · 기술 결정 카드 — FR-REP-01~06 (FR-REP-04 원본 확인 필요), FR-REP-07 · 10 | TODO | B2 (카드는 B9) | B 백엔드 전담 (이전 배정: C 도메인 백엔드) | TODO | TODO |
| B12 | TODO | 회의·스탠드업 · 일정판 · 작업 마감 서버 — FR-MTG-01\~05, FR-PLAN-01\~04 (FR-MTG-02는 팀 재정의 — 2026-11-01 추가 결정으로 채택, FR-PLAN-04 원본 확인 필요) | TODO | B2 | B 백엔드 전담 (2026-11-03 지정. 스탠드업 IB-40 · 41은 A 이관 후보) | TODO | TODO |
| B13 | TODO | 도움 요청 게시판 — FR-LRN-15~20 (2026-10-28 추가 결정) | TODO | B1, B9(안건 전환) | **A 총괄 · 기획 · 백엔드 일부** (2026-11-03 지정 — IB-53\~58, 강사 답변 IB-62 포함) | TODO | TODO |
| B14 | TODO | 강사 대응 — FR-AUTH-06~08의 서버 쪽(승인된 강사 계정 중 지정 — 2026-11-01 결정) · FR-LRN-21~23 · FR-REP-11 · FR-LIB-01 · 02 (2026-10-30 결정) | TODO | B1, B9 · B11 · B13(요청이 붙는 안건 · 카드 · 도움 요청) | B 백엔드 전담 (2026-11-03 지정. 도움 요청의 강사 답변 IB-62는 A, 컨벤션 라이브러리 IB-66 · 67은 A 이관 후보) | TODO | TODO |

**담당 칸은 역할로 채웠다(2026-11-03 실제 4명 재배분 — B12 · B13 · B14 포함).** 사람 이름 · GitHub 계정은 GRAVIT Issue에서 사람 팀원 4명 중에 지정한다. Issue별 주 담당 · 협업 범위 · 실제 통합 선행은 GRAVIT Issue 본문이 기준이고 이 표는 묶음 단위 요약이다. 선행 칸은 기능 사이의 데이터 의존이고(B2 동기화가 GitHub 원본을 채워야 B6\~B12가 계산할 수 있다), 실제 착수 판정은 위 「이 표를 믿는 범위」를 따른다. 이슈별 구현 순서(P0 · P1 · P2)는 00-backlog.md — 순서일 뿐 Must 범위 축소가 아니다(2026-11-01 결정).

- **도움 요청 생애주기**(게시판 작성 · 코멘트 · 닫기 · 안건 전환, FR-LRN-15~20)는 B9에 합치지 않고 B13으로 뗐다(2026-10-28). B9는 평가 · 안건만 맡는다.
- **B8 협업 기본기 · 컨벤션 피드백**(FR-HAB-01\~11)은 습관 체크 삭제로 없앴다(2026-10-30 결정). 기호 B8은 비워 둔다.
- **강사 대응**(FR-AUTH-06\~08의 서버 쪽 · FR-LRN-21\~23 · FR-REP-11 · FR-LIB-01 · 02)은 B14로 더했다(2026-10-30 결정). 담당은 B(2026-11-03 지정)다.
- **담당 쏠림 (2026-11-03 재배분)**: 백엔드 전담이 한 명(B)뿐이라 백로그 63건 중 B가 주 담당인 것이 36건, A가 10건(도움 요청 IB-53\~58 · 62 + 검증 IB-25 · 29 + 결정 IB-01), 프론트 C 11건 · D 6건이다(나머지 화면 작업은 협업 범위로 C · D가 나눔). A의 도움 요청이 실제 통합까지 끝나면 B에서 A로 옮길 후보는 컨벤션 라이브러리(IB-66 · 67) → 막힘 계산(IB-22) → 스탠드업(IB-40 · 41) 순서다. 옮길 때는 그 테이블 · Entity · Service 소유권을 통째로 넘긴다. Must 범위는 줄이지 않는다.

## 공통 계약 소유와 변경 권한

여러 Claude Code 세션이 병렬로 구현하므로 **공통 계약은 먼저 정하고, 바꿀 때는 계약 문서를 먼저 고쳐 공유한다**(2026-11-01 결정 — 문서 관리 방식은 2026-11-03 결정). 계약 목록 · 변경 절차는 [CONTRACTS.md](./CONTRACTS.md) 「9. 계약 변경 절차」가 기준이다.

| 계약 | 구현 소유 | 바뀌면 함께 알릴 역할 | 확정한 곳 (2026-11-03 결정) |
| --- | --- | --- | --- |
| 도메인 모델 · JPA Entity 관계 · ID · DB 스키마(migration) | B (도움 요청 테이블 · Entity는 A) | C · D | CONTRACTS.md 「2」 · 「3」 — 사무실 소속, `Long` 기본키 · 문자열 ID, 평가 회차, 안건 원본 참조, 마감, 요구사항 재동기화, 무결성 제약 |
| 요청 · 응답 DTO · API 경로 | B (도움 요청은 A) | C · D | CONTRACTS.md 「4」 · 「5」 · CONTRACTS_API.md |
| 공통 에러 응답 · Spring Security 설정 · 인가 서비스 | B | A | CONTRACTS.md 「6」 — 상황별 code, 검사 순서, 세션 · CSRF · SSE |
| GitHub 어댑터 인터페이스 · Seed 어댑터 | B | A | CONTRACTS.md 「7」 — 읽기 메서드 목록 |
| 브라우저 `DataSource` · 화면 타입 | D | A · B · C | CONTRACTS.md 「8」 · CONTRACTS_API.md 「0」 — API와 1:1 |
| SceneModel · ThemeLayout | C | D | CONTRACTS.md 「8」 — 공간 역할 · 드롭 결과 · 화면 키 |
| 계약 예시 JSON · 계약 테스트 | B · D 공동 (A · C 참여) | — | CONTRACTS.md 「9」 — `contract/examples/` |

- 계약 문서(CONTRACTS.md · CONTRACTS_API.md)는 **문서 담당자 1명**이 고치고 GitHub에 반영한다. 팀원 · Claude Code 세션은 공지된 계약 커밋을 기준으로 개발하고, 계약을 바꿀 일이 생기면 문서 담당자에게 요청한다. 문서 변경에 PR · 전원 리뷰는 의무가 아니다(2026-11-03 결정 — CONTRACTS.md 「9」).
- 계약이 바뀌면 문서 담당자가 위 표의 구현 소유 · 함께 알릴 역할에 새 커밋과 바뀐 절을 공유한다. 코드는 문서가 바뀐 뒤에 맞춘다 — 계약 문서에 없는 계약을 기능 코드에서 먼저 만들지 않는다.
- 코드(계약 파일 · 공통 파일 — Spring 설정, 공통 `common/` 패키지, 빌드 설정, `package.json` 포함)는 Git 전략대로 PR · 승인 1명으로 합친다. 공통 파일을 바꾸는 PR은 먼저 관련 역할에 알린다.
- 다른 패키지의 Repository를 직접 부르지 않고 그 패키지의 Service를 부른다 `[제안]` — 패키지 경계에서 충돌이 줄어든다.

## 기존 코드와 Spring 패키지의 대응

데모의 서버 코드(`server/src/`)는 **참고만** 한다(2026-11-01 결정 — 포팅을 전제하지 않음). 아래 "Spring 패키지"는 ARCHITECTURE.md 「개발 저장소」의 `[제안]` 경로라 아직 없다.

**데모 서버에 있는 것**(참고): Hono 라우트(작업 · 팀원 CRUD, 회의 시작 · 종료, 데모, 초기화, SSE), SQLite JSON 저장과 `mutate()` 트랜잭션, SSE 허브, 데모 봇, 요청 위생 미들웨어. **없는 것**: 로그인 · 세션 · 권한 검사, GitHub 연동, PR · 요구사항 · 평가 · 스탠드업 · 회고 리포트.

### A와 B의 경계 (2026-11-03 재배분 — 이전 「B와 C의 경계」를 대신함)

| 영역 | B 백엔드 전담 | A 총괄 · 기획 · 백엔드 일부 |
| --- | --- | --- |
| 패키지 | `help`를 뺀 서버 패키지 전부(`common` · `auth` · `workspace` · `github` · `sync` · `requirement` · `evaluation` · `meeting` · `plan` · `flow` · `report` · `instructor` · `realtime`) | `help`만 |
| 테이블 · Entity | `help_request` · `help_comment`를 뺀 전부. `agenda_item` · `meeting` · `standup`은 도움 요청이 FK로 가리키므로 먼저 만든다 | `help_request` · `help_comment` |
| 인증 · 인가 | Spring Security 설정, 로그인 · 세션 · 역할 판정, 인가 서비스, 테스트용 로그인 헬퍼 | 자기 Controller에서 인가 서비스로 권한을 검사한다 |
| 서로 부르는 메서드 | 안건 생성(도움 요청 → 안건 전환), 스탠드업 소유 확인을 A에게 제공. 안건 결정 트랜잭션 안에서 A의 도움 요청 종료 메서드를 부른다. Snapshot 조립은 B | Snapshot `helpRequests` 조각 · 강사 요청함용 도움 요청 조회 메서드를 B에게 제공 |

- 한 테이블 · Entity · Service는 한 사람만 고친다. 서로의 Repository를 직접 쓰지 않고 Service로만 부른다.
- migration 번호는 머지 직전 `main` 최신 + 1, 머지된 파일은 고치지 않는다. 남의 테이블 `ALTER`는 도움 요청 ↔ 안건 순환 FK 한 건(A의 help migration이 붙이고 B가 리뷰)뿐이다.
- 계약 인터페이스를 바꿀 일이 생기면 문서 담당자(A)에게 먼저 요청하고 별도 PR로 낸다.

### 기호별 수정 지점

| 기호 | 데모에서 참고할 곳 | Spring 패키지 (신규 · `[제안]`) | 메모 |
| --- | --- | --- | --- |
| B1 계정·권한 | `server/src/routes.ts` 요청 위생 미들웨어(목적만) | `auth` · `common`(보안 설정) — GitHub 로그인 · 콜백 · 세션 · 현재 사용자 · 역할 판정 · 인가 서비스. GitHub 호출(코드 교환 · 권한 조회)은 `github` 패키지를 거친다 | 데모에는 인증이 없다. `X-Actor` 헤더는 인증이 아니다. GitHub 권한은 로그인할 때만 확인하고 세션은 30분~1시간 뒤 만료된다(2026-10-28 결정). 강사 지정 여부는 요청마다 앱 DB에서 확인한다(2026-10-30 결정 — 강사 지정 저장은 B14). 권한을 조회할 자격 증명은 여기서 정한다. 강사 판정: GitHub 로그인 → 서버 설정 허용 목록(GitHub 사용자 ID) → 사무실별 지정(요청마다 DB). 허용 목록 계정은 ROLE-01 · 02로 판정하지 않고 지정 밖 사무실은 `not_found`(2026-11-01 추가 결정). 보안 심사용 ID/PW 로그인(IB-69)도 여기다 — Spring Security 폼 로그인 `[제안]`, 비밀번호 해시, 설정한 환경에서만 켜짐, 시연 사무실만, 로그인 뒤 같은 세션 · 인가 검사(P15) |
| B2 GitHub 연동 · 동기화 | `server/src/events.ts`(SSE로 결과 알림 — 목적만) | `github` — 설치 토큰, 이슈 · PR(`headSha` · Draft 포함) · 리뷰 · 커밋 · 라벨 · 콜라보레이터 · PR–이슈 연결 읽기, 앱 모델 변환, 변경분 조회 / `sync` — 폴링 · 수동 동기화 · 실패 기록 | 폴링(GitHub → 서버)과 SSE(서버 → 브라우저)는 다른 일이다. GitHub 원본을 바꾸는 호출은 만들지 않는다(CON-02). PR–이슈 연결은 GitHub가 공식으로 인식한 연결만 읽는다 — 본문 `#N` 파싱 없음(FR-GH-04 · 2026-11-01 추가 결정). 이슈 담당자는 전원을 `TaskAssignee`(Task–Member 다대다 중간 Entity)로 저장한다 |
| B3 데이터 어댑터 · 사무실 | 브라우저 `src/data/DataSource.ts` · `src/App.tsx`(`?source=` 선택) · `src/data/mock/` · `src/data/api/` | `workspace` — 사무실(종류: GitHub · 시연), `github`의 Seed 어댑터 | 화면 개발 · 테스트는 브라우저 mock(2026-10-28 결정 유지), 발표 시연의 다른 레포는 서버 Seed 사무실(2026-11-01 결정 — "시연 데이터" 표시, 접근 검사 유지). Task · Member CRUD 메서드는 없앤다. 시연 사무실에는 명시적으로 등록한 대상(우리 조 계정 · 지정된 강사 · 심사용 계정)만 들어간다(2026-11-01 추가 결정 — P2). `local` 어댑터의 뜻은 결정 필요 |
| B4 PR 상태 동기화 | `src/data/types.ts`(PR 모델 추가 — D와 합의), `src/scene/slots.ts`(위치 규칙은 C와 함께 SceneModel로) | `github`의 PR 변환 · 표시 상태 계산 | PR 모델 · 검토 공간 위치가 데모에 없다. 닫힌 PR은 머지 여부와 상관없이 완료 공간(FR-LRN-07). `[구현 기본값]` 머지 안 되고 닫힌 PR은 미병합 종료 표시, Draft PR은 검토 공간 + Draft 배지 |
| B5 보안 · 동기화 비기능 | `server/src/routes.ts` 요청 위생(목적만) | `common` — 출처 검사(`APP_ORIGIN`), CSRF, 쿠키 속성, 로그 마스킹, SSE 권한, API 호출량 관리(NFR-10) | 외부 공개 배포다(SP11 · 2026-11-01 결정). 개발 저장소 `.gitignore`에 `.env` · `*.pem` · `secrets/`를 가장 먼저 넣는다 |
| B6 요구사항 추적 | — | `requirement` — 명세 문서 읽기 · 요구사항 · `RequirementLink` 저장 · 진행률 계산 | 형식은 ## 제목만(TBD-01). 요구사항은 저장하고 수동 동기화 때 갱신한다 — 원본 ID 우선 매칭 · 모호하면 보존 · 연결을 자동으로 지우지 않음(2026-11-03 결정 — CONTRACTS.md 「3」 요구사항 재동기화). 연결은 이슈 위치를 바꾸지 않는다. 묶음 표시는 화면 계산이라 서버 저장이 없다(2026-11-01 결정) |
| B7 진행 · 막힘 | `src/data/selectors.ts` · `src/data/insights.ts` — 업무량 · 포인트 계산은 사람별 비교라 쓰지 않는다 | `flow` — 막힘(이유 문구 없음) · 담당 편중(FR-FLOW-03 · 07) · 팀 지표 · 최근 변화 | 기준값(막힘 48시간 · 48시간)은 팀 설정(`TeamSettings`)에서 읽는다 — 코드 컨벤션 (BE) 「기준값」 |
| B9 PR 평가 · 안건 | `src/data/rules/meetings.ts`(회의 결과가 GitHub 원본을 바꾸는 부분은 쓰지 않음) | `evaluation` — `EvaluationRound`(회차 ID ≠ `headSha`) · `Evaluation` · 현재 회차 집계 / `meeting`의 `AgendaItem` | 평가는 PR 상태 · 위치를 바꾸지 않는다(2026-10-29 결정). 새 회차는 새 PR · 새 커밋 · 회의의 "수정 후 재평가"에서 열리고, 같은 `headSha`에 회차가 여럿일 수 있다(2026-11-01 결정). 안건은 원본을 참조만 한다. 안건 기본 구조(FR-MTG-03 · 04)는 B12와 경계를 정한다 |
| B13 도움 요청 게시판 | — | `help` — 도움 요청 · 코멘트, 닫기 · 도움 된 코멘트 · 배운 점, 48시간 미해결 판정, 안건 전환 · 종료, 스탠드업 "막힌 것"에서 넘어온 요청 | 담당 A(2026-11-03 지정). 데모에 대응 코드가 없다. 안건 전환은 B9의 `AgendaItem`을 쓰므로 B가 제공하는 안건 생성 메서드로만 만든다(위 「A와 B의 경계」). 별도 학습 일지는 없다(2026-11-01 결정) |
| B10 활동 기록 | — | `activity` — 활동 기록 · 동료 피드백 | 데모의 `Activity`(변경 로그)와 이름이 겹친다 — 다른 이름을 쓴다 |
| B11 회고 리포트 | — | `report` — 학습 타임라인(계산) · 리포트 생성 · 마크다운 내보내기 · 카드 조립(9칸 · 규칙 기반 · AI 없음) | 데모에 대응 코드가 없다. 과거 평가 회차도 재료다 |
| B12 회의 · 스탠드업 · 일정판 | `server/src/routes.ts` `/api/meetings`(목적만) — 회의 결과(담당 지정 · 상태 변경 · 백로그)는 GitHub 원본을 바꾸므로 결정 · 배운 점 기록으로 바뀐다 | `meeting` — 회의 · 스탠드업 / `plan` — 일정판 · 작업 마감(`TaskDueDate`) | 담당 B(2026-11-03 지정). 마감은 앱 DB에 저장하고(2026-11-01 결정), 그 사무실의 ROLE-02와 그 이슈의 담당자 전원(`TaskAssignee`)만 고친다 — P14(2026-11-01 추가 결정). 스탠드업 날짜는 한국 시간 기준(구현 기본값) |
| B14 강사 대응 | — | `workspace`의 강사 지정 · `auth`의 승인된 강사 계정, `instructor` — 강사 요청함(계산) · 컨벤션 라이브러리, 각 패키지의 강사 답변 · 피드백 · 꼬리질문 저장 | 데모에 대응 코드가 없다. 담당 B(2026-11-03 지정 — 도움 요청의 강사 답변은 A). 일반 학생은 지정할 수 없고 승인된 강사 계정만 지정한다(2026-11-01 결정). 승인된 강사 계정은 서버 설정 허용 목록(GitHub 사용자 ID)이고 관리 화면이 없다(2026-11-01 추가 결정). 강사의 허용되지 않은 쓰기는 기존 code `forbidden_role`. 역할 판정은 B1, 도움 요청 · 안건 · 카드 본체는 B13 · B9 · B11과 경계를 정한다 |

## 지금 상태 (2026-11-03 snapshot)

- **GitHub Issue 가 아직 없다.** Must 이슈 후보는 [00-backlog.md](./00-backlog.md) 「이슈 백로그 (Must)」에 있다. 개발 저장소(GRAVIT)와 계약 기준 위치 전환(2026-11-03)은 끝났고, 다음은 GRAVIT에 기반 Task 5건 + 백로그 63건 Issue를 만드는 일이다(담당 역할 지정, Assignee는 사람 계정만).
- **역할을 실제 4명으로 다시 나눴다(2026-11-03)** — 위 「배정 기준」 · 「A와 B의 경계」. 일차(5 · 7 · 10일차)는 점검 기준일이고 착수 대기 조건이 아니다. 실제 기술 선행만 기다리고, 그 전에도 DTO · 순수 규칙 · mock 기반 작업은 병렬로 시작한다.
- 구현 스택은 정했다(2026-11-01 결정 — 프론트 React · TypeScript · R3F, 백엔드 Spring Boot. 2026-11-01 추가 결정 — Java 21 · Gradle · MySQL 8.x). Spring Boot 버전 · migration 도구 · 배포 플랫폼은 Spring Boot 골격 때 정한다(ARCHITECTURE.md 「기술 스택」).
- **공통 계약은 v1으로 확정했다**(2026-11-03 결정 — CONTRACTS.md · CONTRACTS_API.md). ID 형식(`Long` 기본키 · 문자열 ID)과 저장소 구성(모노레포)도 함께 닫았다. 바꿀 때는 CONTRACTS.md 「9」 — 문서 담당자가 고치고 새 커밋을 공유한다.
- 데모의 사용 · 수정 권한과 실제 실행은 팀이 확인했다(2026-11-01). 다음은 개발 저장소(모노레포) 생성 → 초기 구조 · Git 설정 → 로컬 MySQL(`compose.yaml`) → Spring 골격 → 공통 Entity · DTO · 인증 · 인가 기초 → GitHub App 연동 → 이슈 생성 · 담당 배분 → 병렬 개발 순서다(ARCHITECTURE.md 「착수 체크리스트」). 그 전에는 가짜 어댑터(브라우저 mock)로 먼저 화면을 띄운다(기획안 「리스크 및 대응 방안」).
- 개발 저장소는 새로 만들기로 했다(2026-10-28 · 모노레포는 2026-11-03). 만든 뒤 Git 전략 「8」 설정을 하고 이슈를 연다.
- 5일차까지 우리 조 레포의 이슈가 사무실에 상자로 뜨는 것이 첫 목표다(B1 · B2 · B3 · B4). 7일차 1차 데모에서는 요구사항 구역, 진행률(B6), PR 추천·비추천 평가(B9)까지 보여야 한다(기획안 「2주 개발 일정」). 10일차에 기능을 동결한다. Must는 줄이지 않는다(2026-11-01 결정).
- 이 절은 snapshot 이라 금방 낡는다. **현재 값은 각 Issue 본문에서 확인한다.**

## B0는 구현 Issue 가 아니다

- decision-only Task 다. 코드 · PR 이 없다. TBD 9건은 2일차까지 팀 결정으로 닫고, 결정은 정의서 변경 이력에 남긴다(기획안 「역할 분담」 협업 규칙).
- 2026-11-01부터 B0에 공통 계약 확정, 미확정 기술 결정, 팀 재정의 초안 4건(FR-AUTH-02 · FR-GH-02 · FR-GH-04 · FR-MTG-02) 승인도 든다. 4건 승인과 Java 21 · Gradle · MySQL 8.x는 2026-11-01 추가 결정으로, 공통 계약 확정 · 저장소 구성(모노레포)은 2026-11-03 결정으로 끝났다. 남은 B0 일은 이슈 생성과 담당 배분이다(ARCHITECTURE.md 「착수 체크리스트」).
- 결정이 나오면 05-policy.md 서비스 정책의 [?] 를 지우고, requirements-changes.md 와 영향받는 문서를 뒤따라 맞춘다.

## 화면 이슈와의 관계

- 화면은 C(프론트 3D)와 D(프론트 HUD · 데이터)가 나눠 맡는다(2026-11-03 재배분 — SCREEN_ASSIGNMENTS.md). 강사 요청함 · 컨벤션 라이브러리 화면은 D다. 화면 이슈의 상태는 [SCREEN_ASSIGNMENTS.md](./SCREEN_ASSIGNMENTS.md) 에 있다.
- 이어받는 관계는 아래와 같다(화면 이름은 07-screens.md 「화면 목록」).

| 화면 | 이어받는 backend |
| --- | --- |
| 로그인 · 조직·레포 선택 | B1 · B2 |
| 사무실 열기 (팀장) · 수집 동의 (팀원) | B2 · B6 · B12 · B10 |
| 3D 사무실 (메인) · 상세 패널 | B2 · B4 · B6 · B7 · B9 · B12(마감) · B13 · B14 |
| [제안] 2D 팀 보드 | B9 · B13 (요구사항 ID 없음) |
| 상단 바 | B2 · B6 · B7 · B12 · B14(팀 설정의 강사 지정) |
| 요구사항 패널 | B6 |
| 활동 기록 | B10 |
| 스탠드업·회의 | B12 · B9 · B13 · B14 |
| 일정판 | B12 |
| 내 학습 타임라인 | B9 · B11 |
| 회고 리포트 | B11 · B14 |
| 강사 요청함 | B14 · B9 · B11 · B13 |
| 컨벤션 라이브러리 | B14 |
