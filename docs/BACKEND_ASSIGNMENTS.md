# Backend Assignments

MVP backend 구현 Issue 의 담당·상태 표다. 화면 구현은 [SCREEN_ASSIGNMENTS.md](./SCREEN_ASSIGNMENTS.md) 를 본다.

- **배정 기준: 기획안 「역할 분담」** — A 기획, B 연동 백엔드, C 도메인 백엔드, D 3D·프론트엔드
- git 흐름(브랜치·커밋·PR·머지): `.claude/rules/git-workflow.md`
- 모듈 경계: [ARCHITECTURE.md](./ARCHITECTURE.md) · 실행·검증 명령: [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md) · Must 이슈 목록: [00-backlog.md](./00-backlog.md) 「이슈 백로그 (Must)」
- **기호 구분**: 이 표의 `B0`~`B12`는 백엔드 담당 기호다. 00-backlog.md의 이슈 후보 번호는 `IB-01`~`IB-50`으로 따로 매긴다 — "B2"는 GitHub 연동 담당 묶음이고, IB-02(GitHub 로그인 이슈)와 다르다.
- [제안]이 붙은 줄은 requirements-changes.md에서 "검토 중"인 요구사항이고, [보류]가 붙은 줄은 2026-10-28에 보류된 요구사항이다. 채택되기 전에는 GitHub 이슈를 만들지 않는다(00-backlog.md 「착수 조건」).

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
| B0 | TODO | TBD 9건 팀 결정 (TBD-01~09) | decision-only | — | A 기획 · TODO: 이름 | (없음) | 전체 |
| B1 | TODO | 계정·권한 — FR-AUTH-01~05 (FR-AUTH-02 · 05 원본 확인 필요) | TODO | — | B 연동 백엔드 · TODO: 이름 | TODO | TODO |
| B2 | TODO | GitHub 연동 · 동기화 — FR-GH-01~06 (FR-GH-02 · 04 · 06 원본 확인 필요) | TODO | B3 | B 연동 백엔드 · TODO: 이름 | TODO | TODO |
| B3 | TODO | 데이터 어댑터(github · fake · local) — NFR-08 | TODO | — | B 연동 백엔드 · TODO: 이름 | TODO | TODO |
| B4 | TODO | PR 상태 동기화 — GitHub 원본 상태(열림 · 머지 · 닫힘)를 사무실 위치로 (FR-OFF-02, FR-LRN-07) | TODO | B2 | B 연동 백엔드 · TODO: 이름 | TODO | TODO |
| B5 | TODO | 보안 · 동기화 비기능 — NFR-04, NFR-05, NFR-09, NFR-10 | TODO | B1, B2 | B 연동 백엔드 · TODO: 이름 | TODO | TODO |
| B6 | TODO | 요구사항 추적 — FR-REQ-01~07 | TODO | B2 | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B7 | TODO | 진행·막힘 — FR-FLOW-01~06, [보류] FR-FLOW-07 | TODO | B2 | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B8 | TODO | 협업 기본기 · 컨벤션 피드백 — FR-HAB-01~07, FR-HAB-08~11 (FR-HAB-01 수정안은 철회) | TODO | B2 | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B9 | TODO | PR 투표 · 판정 · 회의실 안건 로직 — FR-LRN-01~14 (2026-10-28 채택) | TODO | B2, B4 | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B10 | TODO | 활동 기록 — FR-ACT-01~04 (2026-10-28 추가 결정으로 채택) | TODO | B1 | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B11 | TODO | 회고 리포트 · 기술 결정 카드 — FR-REP-01~06 (FR-REP-04 원본 확인 필요), FR-REP-07 · 10 | TODO | B2 (카드는 B9) | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B12 | TODO | 회의·스탠드업 · 일정판 서버 — FR-MTG-01~05, FR-PLAN-01~04 (FR-MTG-02 · FR-PLAN-04 원본 확인 필요) | **담당 미정** | B2 | **미정** — 기획안 「역할 분담」에 서버 담당이 없다(화면은 D). 팀이 정한다 | TODO | TODO |
| B13 | TODO | 도움 요청 게시판 — FR-LRN-15~20 (2026-10-28 추가 결정) | TODO | B1, B9(안건 전환) | **미정** — 기획안 「역할 분담」에 없다. 팀원들이 나중에 정한다 | TODO | TODO |

**담당 칸의 이름은 비어 있다. 이슈가 만들어지면 담당자를 배정한다.** 역할(A~D)은 기획안 「역할 분담」 표 그대로다. 선행 칸은 기능 사이의 데이터 의존이고(B2 동기화가 GitHub 원본을 채워야 B6~B12가 계산할 수 있다), 실제 착수 판정은 위 「이 표를 믿는 범위」를 따른다.

- **도움 요청 생애주기**(게시판 작성 · 코멘트 · 닫기 · 안건 전환, FR-LRN-15~20)는 B9에 합치지 않고 B13으로 뗐다(2026-10-28). B9는 투표 · 판정 · 안건만 맡는다.
- **담당 쏠림**: C 도메인 백엔드가 B6~B11 여섯 묶음을 맡는다. 00-backlog.md 「일정 · 의존성 점검」 기준 이슈 후보 58개 중 33개다. 도움 요청(IB-53~58)은 B13으로 떼어 C에 넣지 않았다. 역할 조정은 팀이 정한다.

## 기존 코드에서 손댈 곳

기존 3D 데모(`SamdoRyu/warehouseDemo_fork`)의 서버 코드를 읽어 정리했다(2026-10-28, 실행은 안 함). 경로는 데모 저장소 기준이고, "(신규)" 경로는 ARCHITECTURE.md 「모듈 이름과 실제 경로」의 권장안이라 아직 없다. 구조 설명은 ARCHITECTURE.md를 본다.

**지금 서버에 있는 것**: Hono 라우트(`server/src/routes.ts` — 작업 · 팀원 CRUD, 회의 시작 · 종료, 데모, 초기화, SSE), 저장(`repo.ts` · `db.ts` — SQLite JSON blob, 트랜잭션), SSE 허브(`events.ts`), 데모 봇(`bot/index.ts`), 요청 위생 미들웨어. **없는 것**: 로그인 · 세션 · 권한 검사, GitHub 연동, PR · 요구사항 · 투표 · 스탠드업 · 회고 리포트.

### B와 C의 경계

| 영역 | B 연동 백엔드 | C 도메인 백엔드 |
| --- | --- | --- |
| GitHub App 인증 · 권한 검증 | 로그인 · 세션 · 역할 판정 · 요청별 검사 미들웨어 | 역할 검사를 자기 라우트에 붙인다 |
| GitHub 폴링 · 변환 | GitHub 원본을 읽어 앱 모델(`Task` · `Submission`)로 바꿔 저장, 동기화 상태 | 변환된 앱 모델만 쓴다 — GitHub 원본 모양을 보지 않는다 |
| DataSource 연동 | 브라우저 `DataSource`(`src/data/DataSource.ts`)의 읽기 경로와 서버 원천 선택 | 앱 자체 기록의 쓰기 메서드(요구사항 연결 · 회의 · 안건 …)를 인터페이스에 더한다 |
| Hono API 확장 | 인증 · 동기화 · 레포 등록 라우트 | 요구사항 · 막힘 · 습관 · 회의 · 리포트 라우트 |
| SQLite 저장 항목 | 팀 설정 · 동기화 상태 · GitHub에서 온 앱 모델 | 요구사항 연결 · 회의 안건 · 스탠드업 · 회고 리포트 · 투표 |
| 업무 흐름 · 막힘 계산 | — | `core`(`src/data/rules/`)의 순수 규칙 |

인터페이스(`src/data/types.ts` · `DataSource.ts`)와 저장 방식(`repo.ts`의 테이블 구성)은 B와 C가 함께 쓰는 계약이다. 바꿀 때는 둘이 먼저 합의하고 별도 PR로 낸다.

### 기호별 수정 지점

| 기호 | 기존 코드에서 고칠 곳 | 새로 만들 곳 (신규) | 메모 |
| --- | --- | --- | --- |
| B1 계정·권한 | `server/src/routes.ts` 미들웨어(인증 · 역할 검사 추가), `server/src/app.ts`(조립) | `server/src/auth/` — GitHub 로그인 · 콜백 · 세션 · 현재 사용자 · 역할 판정. GitHub 호출(코드 교환 · 권한 조회)은 B2의 `adapters/github` 함수를 거친다 | 지금 인증이 전혀 없다. `X-Actor` 헤더는 데모 봇 표시용이라 인증으로 쓰지 않는다. 역할은 로그인할 때만 확인하고 세션은 30분~1시간 뒤 만료된다(2026-10-28 결정 — ARCHITECTURE.md 「인증과 권한」). 권한을 조회할 자격 증명은 여기서 정한다 |
| B2 GitHub 연동 · 동기화 | `server/src/app.ts`(폴링 시작 · 종료), `server/src/events.ts`(동기화 결과를 SSE로 알림) | `server/src/adapters/github/` — 설치 토큰, 이슈 · PR · 리뷰 · 커밋 · 라벨 · 참여자 읽기, 앱 모델 변환, 변경분 조회, 실패 기록 | 폴링(GitHub → 서버)과 SSE(서버 → 브라우저)는 다른 일이다. GitHub 원본을 바꾸는 호출은 만들지 않는다(CON-02) |
| B3 데이터 어댑터 | `src/data/DataSource.ts` · `src/App.tsx`(`?source=` 선택) · `src/data/mock/` · `src/data/api/` | 서버 쪽 원천 선택(`DATA_SOURCE` 도입 시) | 데이터 소스가 브라우저 · 서버 두 층이고, 가짜 데이터는 브라우저 mock을 새 모델로 확장한다(2026-10-28 결정 — ARCHITECTURE.md 「데이터 소스는 두 층이다」). Task · Member CRUD 메서드는 변경 대상 |
| B4 PR 상태 동기화 | `src/data/types.ts`(PR 모델 추가 — C · D와 합의), `src/scene/slots.ts` 위치 규칙은 D와 함께 | `adapters/github`의 PR 변환 | 지금 PR 모델이 없다. 검토 영역 위치도 없다 |
| B5 보안 · 동기화 비기능 | `server/src/routes.ts` 요청 위생(Host · Origin 검사를 `APP_ORIGIN` 기준으로), `server/src/app.ts` 바인딩 주소(지금 `127.0.0.1` 고정) | 로그 마스킹, API 호출량 관리(NFR-10) | 데모 `.gitignore`에 `.env` · `*.pem` · `secrets/`가 없다 — 가장 먼저 추가 |
| B6 요구사항 추적 | `src/data/types.ts` · `server/src/repo.ts` · `db.ts`(새 저장 항목) | 명세 문서 읽기 · 요구사항 · 요구사항–이슈 연결 저장 · 진행률 계산(`src/data/rules/`) | 형식은 ## 제목만(TBD-01, 2026-10-28 결정). 요구사항은 저장하고 수동 동기화 때 갱신한다 |
| B7 진행 · 막힘 | `src/data/selectors.ts` · `src/data/insights.ts` — 업무량 · 포인트 기반 계산(`workload`, `computeKpis`의 쏠림 · 가장 바쁜 팀원, `suggestActions`)은 사람별 비교라 제거 대상 | 막힘 계산(이유 문구 포함) · 팀 지표를 `src/data/rules/`에 | 기준값(막힘 48시간 · 48시간)은 팀 설정(`TeamSettings`)에서 읽는다 — 코드 컨벤션 (BE) 「기준값」 |
| B8 협업 기본기 | — | 컨벤션 검사 규칙(`src/data/rules/`), 규칙 위반 계산 · 본인 응답 | 데모에 대응 코드가 없다 |
| B9 PR 투표 · 안건 | `src/data/rules/meetings.ts` · `server/src/repo.ts`의 회의 · 안건(`Task.onAgenda` → `AgendaItem`) | 투표 저장 · 판정 | 2026-10-28 채택. 안건 기본 구조(FR-MTG-03 · 04)는 B12와 경계를 정한다 |
| B13 도움 요청 게시판 | — | 도움 요청 · 코멘트 저장(`HelpRequest`), 닫기 · 도움 된 코멘트 · 배운 점, 48시간 미해결 판정(`src/data/rules/`), 안건 전환 · 종료 | 데모에 대응 코드가 없다. 안건 전환은 B9의 `AgendaItem`을 쓰므로 B9와 경계를 정한다 |
| B10 활동 기록 | — | 활동 기록 · 동료 피드백 저장 | 데모의 `Activity`(변경 로그)와 이름이 겹친다 — 다른 이름을 쓴다 |
| B11 회고 리포트 | — | 리포트 생성 · 마크다운 내보내기 · 카드 조립 | 데모에 대응 코드가 없다. 회의록 화면(`MeetingHistoryDialog`)이 가장 가깝다 |
| B12 회의 · 스탠드업 · 일정판 | `server/src/routes.ts` `/api/meetings` · `src/data/rules/meetings.ts` — 회의 결과(담당 지정 · 상태 변경 · 백로그)는 GitHub 원본을 바꾸므로 결정 · 배운 점 기록으로 바뀐다 | 스탠드업 저장, 일정판 데이터 | **담당 미정** 그대로다. 마감은 작업 필드 새로 기획 예정(2026-10-28 추가 결정) |

## 지금 상태 (2026-10-28 snapshot)

- **GitHub Issue 가 아직 없다.** Must 이슈 후보는 [00-backlog.md](./00-backlog.md) 「이슈 백로그 (Must)」에 있다.
- 기존 3D 팀 오피스 코드와 DataSource 구조는 1~2일차에 4명이 함께 파악한다(기획안 「역할 분담」). 2026-10-28에 소스를 읽어 위 「기존 코드에서 손댈 곳」과 ARCHITECTURE.md를 채웠지만 **실행 확인은 아직이다** — 1~2일차에 실제로 띄워 보고 참고할 범위와 구현 스택(ARCHITECTURE.md 「기술 스택」 ① · ②)을 확정한다. 그 전에는 가짜 어댑터(브라우저 mock)로 먼저 화면을 띄운다(기획안 「리스크 및 대응 방안」).
- 개발 저장소는 새로 만들기로 했다(2026-10-28). 만든 뒤 Git 전략 「8」 설정을 하고 이슈를 연다.
- 5일차까지 우리 조 레포의 이슈가 기존 사무실에 상자로 뜨는 것이 첫 목표다(B1 · B2 · B3 · B4). 7일차 1차 데모에서는 요구사항 구역, 진행률(B6), PR 찬성·반대 투표(B9)까지 보여야 한다(기획안 「2주 개발 일정」). B9가 기대는 FR-LRN은 2026-10-28 결정 회의에서 채택했다. 10일차에 기능을 동결한다.
- 이 절은 snapshot 이라 금방 낡는다. **현재 값은 각 Issue 본문에서 확인한다.**

## B0는 구현 Issue 가 아니다

- decision-only Task 다. 코드 · PR 이 없다. TBD 9건은 2일차까지 팀 결정으로 닫고, 결정은 정의서 변경 이력에 남긴다(기획안 「역할 분담」 협업 규칙).
- 결정이 나오면 05-policy.md 서비스 정책의 [?] 를 지우고, requirements-changes.md 와 영향받는 문서를 뒤따라 맞춘다.

## 화면 이슈와의 관계

- 화면은 D(3D·프론트엔드)가 맡는다. 화면 이슈의 상태는 [SCREEN_ASSIGNMENTS.md](./SCREEN_ASSIGNMENTS.md) 에 있다.
- 이어받는 관계는 아래와 같다(화면 이름은 07-screens.md 「화면 목록」).

| 화면 | 이어받는 backend |
| --- | --- |
| 로그인 · 조직·레포 선택 | B1 · B2 |
| 사무실 열기 (팀장) · 수집 동의 (팀원) | B2 · B6 · B12 · B10 |
| 3D 사무실 (메인) · 상세 패널 | B2 · B4 · B6 · B7 · B9 · B13 |
| [제안] 2D 팀 보드 | B9 · B13 (요구사항 ID 없음) |
| 상단 바 | B2 · B6 · B7 · B12 |
| 요구사항 패널 | B6 |
| 활동 기록 | B10 |
| 스탠드업·회의 | B12 · B9 |
| 일정판 | B12 |
| 내 습관 체크 | B8 |
| 내 학습 타임라인 | B9 |
| 회고 리포트 | B11 |
