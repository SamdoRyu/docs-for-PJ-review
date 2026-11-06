# Screen Assignments

누가 어느 화면을 맡는지, 그 화면의 디자인 원본이 어디인지 적는 표다.

- 만드는 방법: [ARCHITECTURE.md](./ARCHITECTURE.md) · 공통 계약(DataSource · 화면 타입 · SceneModel · ThemeLayout — v1 확정 2026-11-03): [CONTRACTS.md](./CONTRACTS.md) · 화면이 부르는 API와 DTO · DataSource 메서드: [CONTRACTS_API.md](./CONTRACTS_API.md)
- 실행·검증 명령: [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md)
- git 흐름(브랜치·커밋·PR·머지): `.claude/rules/git-workflow.md`
- **backend 구현 담당·상태: [BACKEND_ASSIGNMENTS.md](./BACKEND_ASSIGNMENTS.md)** ← 로그인 · GitHub 동기화 · 평가 처리 · 막힘 계산 · 저장은 이 문서가 아니라 저기다

이 문서는 **화면 구현 담당 문서**다. 화면은 실제 4명 재배분(2026-11-03)에 따라 프론트 두 명이 나눠 맡는다(기획안 「역할 분담」의 "D 한 명"은 이전 배정). 실제 화면 Task 담당은 GRAVIT Issue Assignee가 기준이고, 2026-11-07 현재 값은 아래 「담당 표」에 옮겼다 — 위 역할 나눔과 다른 화면이 있다(A · B도 화면 Task를 맡았다).

- **C 프론트 3D** — 3D 사무실(장면 · SceneModel · ThemeLayout · 도형 · 드래그 · 회의실), 회의 · 안건 대화상자, 일정판, 내 학습 타임라인 · 회고 리포트 · 기술 결정 카드. 파일: `frontend/src/scene/**` · `src/scene/model/`(SceneModel) · `src/theme/station/`(ThemeLayout — 우주정거장. 데모의 `src/office/`는 GRAVIT에서 지웠다) · `src/ui/MeetingDialogs.tsx` · `src/ui/WeekView.tsx` · 리포트 화면 신규 파일
- **D 프론트 HUD · 데이터** — `DataSource` · 화면 타입 · api/mock · `store`, 앱 셸 · 라우터, 로그인 · 조직·레포 선택 · 사무실 열기, 상단 바 · 상세 패널 · 요구사항 패널, 도움 요청 게시판, 스탠드업, 강사 화면(팀 설정 · 강사 요청함 · 컨벤션 라이브러리). 파일: `frontend/src/data/**` · `src/store.ts` · `src/App.tsx` · `src/ui/`(위 C 파일 제외)
- C는 데이터를 store 셀렉터와 DataSource 메서드로만 받고, 드래그 결과는 D가 소유한 store 액션을 부른다. 같은 기능의 3D 표현(C)과 HUD 패널(D)은 GRAVIT Issue의 협업 칸으로 나눈다. 사람 계정 지정은 GRAVIT Issue에서 사람 팀원 4명 중에서만 한다.

화면 목록과 각 화면에 보이는 것은 [07-screens.md](./07-screens.md) 「화면 목록」이 기준이다. 디자인 원본은 확정 시안 GRAVIT [`frontend/public/station/`](https://github.com/SamdoRyu/GRAVIT/blob/main/frontend/public/station/README.md)이다(우주정거장 테마 확정 2026-11-04 — GRAVIT #76 · #77 · #81, 단일 테마). `npm run dev` 뒤 `http://localhost:5173/station/index.html`에서 시안 화면 4개(`#/` · `#/blocked` · `#/meeting` · `#/pr/21`)를 본다. 보이는 모양은 시안, 데이터 · 동작 · 권한은 계약을 따른다(아래 「알려진 문서 ↔ 디자인 차이」).

## 시작하기 전에

```bash
git fetch origin
git switch main
git pull --ff-only origin main
```

```bash
cd frontend
npm ci
npm run dev          # 화면 :5173 — 브라우저 mock 데이터(?source= 없음)
```

명령의 뜻과 선행 조건은 [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md), 확인된 명령은 GRAVIT [README](https://github.com/SamdoRyu/GRAVIT/blob/main/README.md) 「처음 실행하기」 · 「빌드 · 테스트 명령」이다. 화면 작업은 GitHub 없이 한다 — 브라우저 mock(`?source=` 없음) 또는 Spring 서버 데이터(`?source=api`, 환경 변수 가이드 「환경별 설정」). 개발 저장소는 모노레포라(2026-11-03 결정) 화면 명령은 `frontend/`에서 돌리고, Spring 서버 데이터가 필요하면 루트에서 `docker compose up -d --wait`(로컬 MySQL) → `backend/`에서 `.\gradlew.bat bootRun`(`:8080`) → 브라우저 `/?source=api`. 데모의 `npm run dev:all`(Hono 서버)은 GRAVIT에 없다.

## 담당 표

**담당 칸은 역할로 채웠다(2026-11-03).** 사람 이름 · GitHub 계정과 이슈 번호는 GRAVIT Issue에서 정하고, 이 표와 Issue 본문이 어긋나면 Issue 본문이 최신이다. **2026-11-07에 디자인 줄 · 묶음 · 이슈 · 담당 · 상태 칸을 GRAVIT Issue 번호 · Assignee · 상태로 다시 채웠다** — Assignee 계정과 역할 문자의 대응은 GRAVIT [`docs/README.md`](https://github.com/SamdoRyu/GRAVIT/blob/main/docs/README.md) 「역할」. 상태는 main 구현 / PR 진행 중 / 부분 구현 / 미구현 / v1 보류로 적고, 열린 이슈는 머지 PR이 있어도 실제 통합 확인 전이다. 2026-11-03 배정과 다른 담당은 괄호에 이전 배정을 남겼다.

| 화면 | 디자인 줄 | route | 묶음 | 이슈 | 담당 | 상태 |
| --- | --- | --- | --- | --- | --- | --- |
| 로그인 — FR-AUTH-01 (심사용 ID/PW 로그인 폼 포함 — IB-69, 2026-11-01 추가 결정) | 시안 없음 — #166 디자인(`src/ui/entry/LoginScreen.tsx`) | 라우터 추가 — 주소 미정 (2026-10-28 결정) | — | #64 · #63 · #165 · #166 닫힘, Bug #236 열림 | D (#166) | main 구현 · #236 PR #241 진행 중 |
| 조직·레포 선택 — FR-GH-01 | 시안 없음 — #166 디자인(`src/ui/entry/EntryScreen.tsx` · `OpenWorkspace.tsx`) | 라우터 추가 — 주소 미정 (2026-10-28 결정) | — | #68 · #166 닫힘, Bug #238 열림 | D (#166) | main 구현 · #238 PR #244 진행 중 |
| 사무실 열기 (팀장) — FR-REQ-01, FR-PLAN-01 | 시안 없음 — `src/ui/entry/OpenWorkspace.tsx` | 라우터 추가 — 주소 미정 (2026-10-28 결정) | — | #68 · #13 · #38 닫힘 | D (#13) | main 구현 |
| 수집 동의 (팀원) — FR-ACT | 시안 없음 | 라우터 추가 — 주소 미정 (2026-10-28 결정) | — | #173 열림 | A (2026-11-06 — 활동 기록 대화상자의 동의 화면, GRAVIT #173) | PR #247 진행 중 (서버는 main 구현) |
| 3D 사무실 (메인) — FR-OFF-01 · 02 · 04~06, FR-REQ-02 · 04(구역 · 묶음 · 연결), FR-FLOW-02, FR-LRN-03 · 04 | 시안 `#/` · `#/blocked` — `src/scene/` · `src/theme/station/` | 라우터 없음 — 결정 필요 | — | #10 · #15 \~ #17 · #21 · #23 · #26 · #30 · #31 · #205 · #102 · #183 닫힘, #11 열림 | C (v1 전환 #102는 D, 게시판 도움 요청 HUD #183은 A) | main 구현 · #11 실제 통합 확인 대기 |
| [제안] 2D 팀 보드 — 대응 요구사항 ID 없음 (기획안 "(제안)" · requirements-changes.md 추가 제안 후보 10) | — | 라우터 없음 — 결정 필요 | — | 없음 ([제안] — 채택 전) | D (채택 전 착수 안 함) | 미구현 |
| 상단 바 — FR-PLAN-01, FR-FLOW-05, FR-GH-05, FR-REQ-05(진행률), FR-ACT 안내, FR-AUTH-07(강사 레포 드롭다운), 팀 설정 버튼 · 시연 데이터 표시(2026-11-01 결정) | 시안 `#/` 상단바 — `src/ui/TopBar.tsx` · `SyncStatus.tsx` | 라우터 없음 — 결정 필요 | — | #45 · #38 · #223 닫힘, #24 · #234 · #248 열림 | D (D-day #38은 A · D, 팀 설정의 강사 지정 #223은 B, 수동 동기화 #234는 A) | 부분 구현 — #24 서버 `averageReviewWaitHours` 항상 null · #234 PR #239 진행 중 · #248 PR 없음 |
| 상세 패널 — FR-OFF-05, FR-LRN-01 | 시안 `#/pr/21`(클린/스멜 대신 추천/비추천) — `src/ui/OfficeDetail.tsx` · `Evaluation.tsx` | 라우터 없음 — 결정 필요 | — | #12 · #28 닫힘, Bug #235 열림 | D (#12 · #28) | main 구현 · #235 PR #240 진행 중 |
| 요구사항 패널 — FR-REQ-03~06 | 시안 없음 — `src/ui/RequirementBoard.tsx` · `RequirementWaiting.tsx` | 라우터 없음 — 결정 필요 | — | #197 닫힘 | D (#197은 Assignee 없음) | main 구현 |
| 활동 기록 — FR-ACT-01~04 | 시안 없음 | 라우터 없음 — 결정 필요 | — | #173 열림 | A(HUD · 데이터) · C(사무실 3D 메모지) (2026-11-06 — GRAVIT #173) | PR #247 진행 중(HUD · 수집 동의) · 3D 메모지 미구현 · 동료 피드백 FR-ACT-04 v1 보류 |
| 스탠드업·회의 — FR-MTG-01~05, FR-LRN-05 · 12 | 시안 `#/meeting` — `src/ui/MeetingDialogs.tsx` · `StandupBoard.tsx` · `DecisionBoard.tsx` | 라우터 없음 — 결정 필요 | — | #190 · #212 닫힘, #34 열림 | C(회의 · 안건 · 회의실 3D) · A(스탠드업 화면 #190) · B(결정 기록 화면 #212) — 2026-11-03 배정은 C · D(스탠드업) | main 구현 · #34 스탠드업 `commit` 미리 채움 부분 구현 |
| 일정판 — FR-PLAN-02~04 | 시안 없음 — `src/ui/WeekView.tsx` | 라우터 없음 — 결정 필요 | — | #39 닫힘 | A · C (#39 — 2026-11-03 배정은 C) | main 구현 |
| 내 학습 타임라인 — FR-LRN-08 | 시안 없음 — `src/ui/RetroBoard.tsx` | 라우터 없음 — 결정 필요 | — | #172 · #209 닫힘 | B (화면 #209, 서버 #172는 A — 2026-11-03 배정은 C) | main 구현 (`commit_after_meeting` 항목만 부분 구현) |
| 회고 리포트 — FR-REP-01~06, FR-REP-07, FR-ACT-03 | 시안 없음 — `src/ui/RetroBoard.tsx` · `DecisionCards.tsx` | 라우터 없음 — 결정 필요 | — | #40 \~ #43 · #209 · #218 닫힘 | B (리포트 화면 #209) · A (기술 결정 카드 화면 #218) — 2026-11-03 배정은 C | main 구현 |
| 강사 요청함 — FR-LRN-21~23, FR-REP-11 | 시안 없음 — `src/ui/InstructorInbox.tsx` | 라우터 없음 — 별도 화면이 아니라 강사 권한으로 연 화면의 사이드바 또는 헤더 요소(어느 쪽인지는 디자인 때 정함 · 2026-10-30 결정). 2026-11-07 현재: 사무실 고르기 화면과 상단 바 더보기 두 곳(#224) | — | #58 · #224 닫힘 | B (#224 — 2026-11-03 배정은 D) | main 구현 (mock에는 강사 계정이 없어 `?source=api`에서만 동작) |
| 컨벤션 라이브러리 — FR-LIB-01 · 02 | 시안 없음 — `src/ui/ConventionLibraryDialog.tsx` | 라우터 없음 — 사무실 안에서 여는 패널 (2026-10-30 결정). 2026-11-07 현재: 상단 바 더보기의 대화상자(#225) | — | #60 · #61 · #225 닫힘 | A (#225 — 2026-11-03 배정은 D) | main 구현 (강사 쓰기는 `?source=api`에서만) |

**묶음**: 정한 묶음이 없다 — 화면 작업은 GRAVIT Issue 단위로 진행했다(2026-11-07 현재). 묶음 칸은 `—`로 둔다.

**route**: 기존 데모에는 URL 라우터가 없다. 한 페이지 위에 3D 장면과 HUD(상단 바 · 오른쪽 패널 탭 · 대화상자)를 겹쳐 쓰고, 주소에는 `?source=api`만 있다. 로그인 · 조직·레포 선택처럼 사무실 밖 화면은 주소별 화면으로 나누고 라우터를 추가한다(2026-10-28 결정). 주소 이름과 디자인은 더 토론한다(07-screens.md는 URL을 정하지 않는다).

### 기존 데모 코드와의 대응

기존 3D 데모(`SamdoRyu/warehouseDemo_fork`)의 화면 코드를 읽어 정리했다(2026-10-28). 데모의 사용 · 수정 권한과 실제 실행은 2026-11-01에 팀이 확인했다. 경로는 데모 저장소 기준이다. 화면 쪽 상세는 07-screens.md 「기존 데모 화면과의 대응」, 3D 부품별 판정은 ARCHITECTURE.md 「기존 데모와의 대응」.

> **프론트엔드 스택은 데모와 같다(React · TypeScript · Three.js + R3F · Zustand — 2026-11-01 결정).** 아래 경로 · 파일이 그대로 재사용 · 리팩터링할 작업 위치다. 백엔드는 Hono가 아니라 Spring Boot로 새로 만든다 — 선행 데이터(backend) 열의 B 기호는 Spring 쪽 담당이다.

| 화면 | 기존 컴포넌트 · 경로 | 판정 | 수정 핵심 | 선행 데이터 (backend) |
| --- | --- | --- | --- | --- |
| 로그인 | 없음 | 신규 | GitHub 로그인 버튼 · 실패 상태. 심사용 ID/PW 로그인 폼은 설정한 환경에서만 보인다(P15 — 2026-11-01 추가 결정). 대화상자 틀은 `src/ui/Dialogs.tsx` `Modal`을 참고할 수 있다 | B1 |
| 조직·레포 선택 | 없음 | 신규 | 조직 · 레포 목록, 승인 요청 안내 | B1 · B2 |
| 사무실 열기 (팀장) | 없음 | 신규 | 레포 · 기간 · 명세 문서 경로 입력 | B2 · B6 · B12 |
| 수집 동의 (팀원) | 없음 | 신규 (2026-10-28 추가 결정으로 채택) | — | B10 |
| **3D 사무실 (메인)** — 수정 규모 큼 | `src/scene/OfficeScene.tsx` · `TaskBox.tsx` · `TaskBoxes.tsx` · `slots.ts` · `MeetingRoomState.tsx` · `DragLayer.tsx` · `dropTarget.ts` · `Avatar.tsx` · `Overlays.tsx` · `src/office/layout.ts` | 수정 · 확장 | 공간 · 책상 · 아바타 · 카메라 · 이동 애니메이션은 데모를 재사용. **위치 계산을 SceneModel(공간 역할)과 ThemeLayout(기본 테마 좌표 · 드롭 영역)으로 나눈다**(2026-11-01 결정 — ARCHITECTURE.md 「공간 역할과 테마」). PR 삼각기둥 등 새 도형, 요구사항 구역 · 묶음 오브젝트 · 진행률, 검토 공간 위치, 드래그 결과 변경(구역 연결 — 상자는 제자리 / 회의실 — 별도 안건 표현), 게시판 오브젝트(내용은 HUD), 아바타 이벤트 이동, 시연 데이터 표시, 사람별 업무량 표현(신호 다이아 · 업무량 원판 · 기울기) 제거 | B2 · B4 · B6 · B7 · B9 · B13 · B14 |
| [제안] 2D 팀 보드 | 비슷한 것: `src/ui/KanbanView.tsx` · `ListView.tsx`(작업 목록) | 신규 · 채택 전 착수 안 함 | 안건 아이콘 · 게시판 영역 | [제안] B9 · B13 |
| 상단 바 | `src/ui/TopBar.tsx` | 수정 | KPI · 균형 칩(가장 바쁜 팀원) 대신 요구사항 진행률 · 열린 PR 수 · 평균 리뷰 대기 · 동기화 상태. 레포 드롭다운 · D-day · 팀 설정 버튼(패널 — 레포 관리자만 수정) · 시연 데이터 표시 신규(2026-11-01 결정). 오버레이 전환은 정보 뷰(최근 변화 · 담당 · 막힘) 전환으로 | B2 · B6 · B7 · B12 · B14 |
| 상세 패널 | `src/ui/Detail.tsx` · `src/ui/RightPanel.tsx` | 수정 | 편집 입력(상태 · 우선순위 · 포인트 · 담당)을 읽기 전용과 GitHub 링크로. 우선순위는 `priority:*` 라벨, 마감은 앱 저장 값이고 여기 또는 일정판에서 레포 관리자와 그 이슈의 담당자 전원이 입력한다(P14 — 2026-11-01 추가 결정). 담당자는 전원 표시. 연결 PR(GitHub 공식 연결만) · 리뷰 상태 · Draft 배지 · 미병합 종료 표시 추가. 팀원 상세의 업무량 게이지(`Vitals`) 제거. 평가 버튼 · 현재 회차의 추천·비추천과 이유 | B2 · B4 · B9 · B12 |
| 요구사항 패널 | 없음 — `RightPanel.tsx`의 탭 구조를 참고 | 신규 | 요구사항 목록 · 진행률 · 빈틈 · 연결 후보 | B6 |
| 활동 기록 | 없음 — `src/ui/ActivityFeed.tsx`는 데이터 변경 로그라 다른 기능 | 신규 (2026-10-28 추가 결정으로 채택) | — | B10 |
| 스탠드업·회의 | `src/ui/MeetingDialogs.tsx`(회의 시작 · 종료 · 회의록) · `src/scene/MeetingRoomState.tsx` | 수정 · 확장 | 회의 종료 결과(담당 지정 · 상태 변경 · 백로그)를 결정 · 배운 점 기록으로. 스탠드업 화면은 신규 — "막힌 것"에서 도움 요청으로 넘기기(FR-LRN-15 · FR-MTG-02) | B12 · B9 · B13 |
| 일정판 | `src/ui/WeekView.tsx`(팀원 × 날짜 그리드) | 수정 | 화면 모양(팀원 × 날짜 그리드 · 탭)은 유지하고, 칸 · 행의 포인트 합계 · 팀원별 업무량 막대 · 드래그로 마감일 바꾸기는 뺀다(2026-10-28 추가 결정). 마감은 앱 DB에 저장하고, 레포 관리자와 그 이슈의 담당자 전원이 입력한다 — 입력 컴포넌트는 화면 작업 때 정한다(2026-11-01 결정 · P14 추가 결정) | B12 |
| 내 학습 타임라인 | 없음 | 신규 | — | B9 |
| 회고 리포트 | 없음 — 회의록(`MeetingHistoryDialog`)이 가장 가깝다 | 신규 | 리포트 · 마크다운 내보내기 | B11 |
| 강사 요청함 | 없음 | 신규 (2026-10-30 결정) | — | B14 · B9 · B11 · B13 |
| 컨벤션 라이브러리 | 없음 | 신규 (2026-10-30 결정) | — | B14 |

v1 화면 목록에 없는 기존 화면 — `src/ui/TeamView.tsx`(팀원별 업무량 카드), `src/ui/Suggestions.tsx`(업무 재배정 추천), `src/ui/KanbanView.tsx` · `ListView.tsx` · `FilterBar.tsx`(작업 편집 · 목록), 작업 · 팀원 만들기 대화상자(`src/ui/Dialogs.tsx`) — 는 사람별 비교이거나 GitHub 원본을 바꾸는 기능이라 빼거나 다른 용도로 바꾼다. 칸반 · 목록 탭은 화면 모양만 유지하고, 상태 · 담당 편집(GitHub 원본)은 뺀다(2026-10-28 추가 결정).

화면에 붙은 요구사항 ID는 기획안 「화면 구성」 표의 "관련 요구사항" 열을 바탕으로 07-screens.md 「화면 목록」과 맞췄다. [제안]이 붙은 화면 · 요소(2D 팀 보드)는 requirements-changes.md에서 아직 후보인 것에 기대므로 채택 전에는 착수하지 않는다. 수집 동의 · 활동 기록 · 상단 바 안내 · 활동 유형 분포(FR-ACT)와 3D 사무실의 평가 · 회의실 · 게시판 도움 요청(FR-LRN-15~20 포함)은 2026-10-28에 채택했다(07-screens.md).

**담당 쏠림 (2026-11-03 배정 기준 — 실제 Assignee는 위 담당 표)**: 화면은 16개다 — 상단 바 · 상세 패널처럼 별도 화면이 아닌 요소 · 패널도 한 줄로 센다. 내 습관 체크를 지우고 강사 요청함(사이드바 · 헤더 요소) · 컨벤션 라이브러리(사무실 안 패널)를 더했다(2026-10-30 결정). 2026-11-03 재배분으로 C가 5개(3D 사무실 · 회의 쪽 · 일정판 · 내 학습 타임라인 · 회고 리포트), D가 11개(스탠드업 포함, 2D 팀 보드는 [제안]이라 착수 안 함)를 맡는다. C는 3D 사무실 하나의 범위가 가장 크고, D는 백엔드 Issue마다 붙는 화면 협업이 많다. 아래 문장들은 이전 배정(D 한 명 — 기획안 「역할 분담」) 기준의 분석이다. 데모를 참고해도 쏠림은 크게 줄지 않는다 — D의 14개 중 데모에 비슷한 화면이 있는 것은 5개(3D 사무실 · 상단 바 · 상세 패널 · 스탠드업·회의 · 일정판)이고 나머지 9개는 신규다(그중 2D 팀 보드 1개는 [제안]이라 지금 착수하지 않음 → 착수할 신규 8개). 게다가 가장 큰 3D 사무실은 데모에 비슷한 화면이 있지만 구현 범위가 넓다. 로그인 · 조직·레포 선택처럼 B1 · B2와 붙어 있는 화면, 스탠드업 · 일정판처럼 B12와 붙어 있는 화면을 어떻게 나눌지 포함해 역할 조정은 팀이 정한다.

### 시작 순서

- 기획안 「2주 개발 일정」 기준 — **5일차까지 우리 조 레포의 이슈가 기존 사무실에 상자로 뜨는 것**이 첫 목표다(연동·사무실 단계: 3D 사무실 · 상세 패널). 그 전에는 가짜 어댑터로 먼저 화면을 띄운다.
- **7일차 1차 데모**에서는 요구사항 구역, 진행률, PR 추천·비추천 평가까지 보여야 한다(요구사항 추적 단계: 요구사항 패널 · 평가 표시). 평가 표시(FR-LRN-01~03)는 2026-10-28에 채택되어 데모 범위에 든다.
- 나머지 화면은 흐름·리포트 단계다. 강사 요청함 · 컨벤션 라이브러리도 이 단계의 Must다(IB-62\~67 — 2026-10-30 결정). 상단 바에서 강사에게 모든 조 레포가 보이는 것(FR-AUTH-07)은 연동·사무실 단계다(IB-60). Should · Could 와 활동 기록 제안은 8~10일차에 Must 가 끝났을 때만 착수하고, 10일차에 기능을 동결한다.
- 정보 뷰가 일정상 밀리면 패널 목록으로 대신한다.
- 이 문서와 이슈 본문이 어긋나면 **이슈 본문이 최신**이다.

## 충돌 방지

### 마음껏 건드려도 되는 곳

기존 데모는 화면별 폴더가 없고 `src/ui/`(HUD) · `src/scene/`(3D)에 파일을 나란히 둔다. 새 화면은 자기 파일(예: `src/ui/<화면이름>.tsx`)과 그 화면 전용 CSS에서 작업한다. 화면별 폴더는 만들지 않았다(2026-11-07 현재 — 화면 파일은 `src/ui/`에 나란히 두고, 진입 화면만 `src/ui/entry/` 폴더다). 폴더 구조 전면 변경은 하지 않는다.

### 건드리기 전에 말해야 하는 곳

| 파일 | 왜 |
| --- | --- |
| 공용 컴포넌트(상단 바 · 상세 패널 · 도형 5종 · 정보 뷰(최근 변화 · 담당 · 막힘)) | 여러 화면이 쓴다. 수정은 **별도 PR** 로 분리하고 먼저 공유한다. 경로는 `src/ui/TopBar.tsx` · `src/ui/OfficeDetail.tsx`(데모의 `Detail.tsx`) · `src/scene/TaskBox.tsx`(도형) · `src/scene/shared.tsx`(공용 도형 · 재질). 정보 뷰 틀이던 데모의 `src/scene/Overlays.tsx`는 GRAVIT에서 지웠다 |
| 계약 파일 — `src/data/types.ts` · `src/data/DataSource.ts` · `src/store.ts` · `src/data/selectors.ts`, 그리고 SceneModel(`src/scene/model/`) · ThemeLayout(`src/theme/station/`). 데모의 `src/office/layout.ts` · `src/data/insights.ts`는 GRAVIT에서 지웠다 | 화면 · 서버 · mock이 함께 기댄다. 데모의 `CLAUDE.md`도 앞의 파일들을 계약 파일로 묶어 둔다. 계약에 닿는 변경은 계약 문서(2026-11-03부터 GRAVIT `docs/CONTRACTS*.md`가 기준)를 문서 담당자가 먼저 고쳐 공유한 뒤에 하고, 백엔드(A · B)와 먼저 합의한다. 데이터 파일은 D, SceneModel · ThemeLayout은 C가 소유한다 |
| 기존 3D 데모의 3D 표현 코드(`src/scene/`) | 재사용 · 리팩터링한다. 새 모델링은 하지 않는다(기획안 「기능 동결 규칙」 — 확정 시안의 정거장 · 스페이 GLB 아바타를 R3F로 옮기는 것은 범위 안이고, 시안 밖 새 에셋은 만들지 않는다). 테마(2026-11-04부터 우주정거장 — 단일 테마)에 묶인 좌표 · 모양은 ThemeLayout · 테마 컴포넌트에 두고, 도메인 로직이 테마를 모르게 한다(2026-11-01 결정) |
| 의존성 목록 파일(`package.json` · `package-lock.json`) | 라이브러리 추가는 사전 합의(`.claude/rules/security.md`) |
| `src/App.tsx` · `src/ui/Hud.tsx` · `src/ui/RightPanel.tsx` · `src/ui/uiState.ts` · `src/index.css` · `vite.config.ts` · `tsconfig*.json` | 전원이 공유하는 레이아웃 · 설정 |

### 인증은 화면 작업이 아니다

GitHub App 로그인과 역할 판정은 서버(Spring Security) 공유 인프라를 건드린다. 화면 브랜치에 섞으면 전원과 충돌한다. **backend 담당 기호 B1(계정·권한 — 00-backlog의 IB 번호가 아니다)에서 구현한다** — [BACKEND_ASSIGNMENTS.md](./BACKEND_ASSIGNMENTS.md). 화면은 역할에 따라 버튼을 숨길 수 있지만, 그것을 권한 확인으로 보지 않는다(FR-AUTH-04).

### `modify/` 는 화면별로 쓴다

TODO: 대응 내용 없음 (삭제 여부 확인) — 구현이 기획 문서와 달라졌을 때의 기록 방식이 기준 문서에 없다.

## 브랜치와 PR

`.claude/rules/git-workflow.md` 를 따른다. 화면 작업은 `feat/<이슈번호>-<slug>` 다.

```bash
git fetch origin
git switch main
git pull --ff-only origin main
git switch -c feat/<이슈번호>-<slug>
```

PR base 는 `main` 이다. PR 에는 `Closes #<이슈번호>` 와 "관련 요구사항"(이슈에 적은 ID)을 넣고, PR 템플릿 「UI 변경」에 **화면 스크린샷을 첨부한다.**

## 공통 완료 조건

화면 이슈의 Acceptance Criteria 바탕이 되는 줄이다. **화면별 이슈 본문에 구체적으로 정해진 것이 이 줄보다 우선한다.**

- [ ] 07-screens.md 「모든 화면 공통 인수 조건」 1 · 2와 「공통 상태」 표 (지금은 초안 — 팀이 확인하면 이 줄의 "초안"을 지운다)
- [ ] 팀원 간 · 조 간 순위 · 비교 화면이 없고, 개인 지표(학습 타임라인 · 회고 리포트 · 기술 결정 카드)는 본인과 강사에게만 보인다 (FR-FLOW-06 · NFR-06 · 2026-10-30 결정)
- [ ] 막힘 표시는 막혔다는 사실만 보이고 이유 문구가 없다 (FR-FLOW-02 · 2026-10-29 결정)
- [ ] 위치 계산이 테마 좌표에 묶이지 않는다 — 요구사항 연결 · 회의 안건이 원본 도형의 위치를 바꾸지 않는다 (05 「상태값」 · 2026-11-01 결정)
- [ ] `npm run lint` · `npm run build`(타입 검사 · 3D lazy 번들 검사 포함) · `npm run test:contract`(mock 쪽)를 돌린 결과를 PR 「검증」에 적는다. 3D를 바꿨으면 `npm run test:e2e`도 돌린다(Chrome 필요). 명령은 GRAVIT README 「빌드 · 테스트 명령」을 따른다 — CI는 lint · test:contract · build를 돌리고, E2E는 로컬에서만(mock 기준) 돈다

## 알려진 문서 ↔ 디자인 차이

시안(GRAVIT `frontend/public/station/`)과 계약이 다르면 계약대로 한다(GRAVIT `CLAUDE.md` 「디자인 테마」 — 2026-11-04): PR 평가는 시안의 클린/스멜이 아니라 `recommend`/`not_recommend`(두 쪽 다 이유 필수 · 작성자 평가 불가 · 평가자 표시 금지), 안건 후보는 48시간 도움 요청뿐(시안의 "앱이 찾은 후보" 없음), 머지 준비 선반 F는 장식, 요구사항 구역은 시안에 없지만 Must라 정거장 안에 만든다(요구사항 데크 판). 작업 포드는 최대 8개(`deskIndex` 0 \~ 7 — 0 \~ 3은 항상, 4 \~ 7은 쓰는 팀원이 있을 때만)이고 자리가 없는 팀원은 3D에 없다. 시안의 JS · `data.js` 목업은 계약이 아니다.
