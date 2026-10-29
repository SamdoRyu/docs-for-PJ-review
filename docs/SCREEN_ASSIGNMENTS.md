# Screen Assignments

누가 어느 화면을 맡는지, 그 화면의 디자인 원본이 어디인지 적는 표다.

- 만드는 방법: [ARCHITECTURE.md](./ARCHITECTURE.md)
- 실행·검증 명령: [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md)
- git 흐름(브랜치·커밋·PR·머지): `.claude/rules/git-workflow.md`
- **backend 구현 담당·상태: [BACKEND_ASSIGNMENTS.md](./BACKEND_ASSIGNMENTS.md)** ← 로그인 · GitHub 동기화 · 평가 처리 · 막힘 계산 · 저장은 이 문서가 아니라 저기다

이 문서는 **화면 구현 담당 문서**다. 화면은 기획안 「역할 분담」에 따라 **D(3D·프론트엔드)** 가 맡는다 — FR-OFF, 정보 뷰(최근 변화 · 담당 · 막힘 — 2026-10-29 결정), 평가 표시와 회의실 드래그(FR-LRN-03 · 04), 상단 바와 패널, FR-MTG · FR-PLAN · FR-ACT 화면.

화면 목록과 각 화면에 보이는 것은 [07-screens.md](./07-screens.md) 「화면 목록」이 기준이다. TODO: 디자인 원본(파일 · 링크)이 아직 없다 — 07-screens.md 「디자인」의 [?].

## 시작하기 전에

```bash
git fetch origin
git switch main
git pull --ff-only origin main
```

```bash
npm install
npm run dev:all      # 화면 :5173 + API :8787. 서버 데이터는 http://localhost:5173/?source=api
```

명령의 뜻과 선행 조건은 [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md). 화면 작업은 GitHub 없이 한다 — 브라우저 mock(`?source=` 없음) 또는 서버 데이터(환경 변수 가이드 「환경별 설정」). 명령은 기존 데모 저장소 기준이고, 이 문서를 쓰면서 실행해 보지는 않았다.

## 담당 표

**담당 칸의 이름은 비어 있다. 팀 회의에서 채운다.**

| 화면 | 디자인 줄 | route | 묶음 | 이슈 | 담당 | 상태 |
| --- | --- | --- | --- | --- | --- | --- |
| 로그인 — FR-AUTH-01 | TODO | 라우터 추가 — 주소 미정 (2026-10-28 결정) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 조직·레포 선택 — FR-GH-01 | TODO | 라우터 추가 — 주소 미정 (2026-10-28 결정) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 사무실 열기 (팀장) — FR-REQ-01, FR-PLAN-01 | TODO | 라우터 추가 — 주소 미정 (2026-10-28 결정) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 수집 동의 (팀원) — FR-ACT | TODO | 라우터 추가 — 주소 미정 (2026-10-28 결정) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 3D 사무실 (메인) — FR-OFF-01~06, FR-FLOW-02, FR-LRN-03 · 04 | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |
| [제안] 2D 팀 보드 — 대응 요구사항 ID 없음 (기획안 "(제안)" · requirements-changes.md 추가 제안 후보 10) | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 상단 바 — FR-PLAN-01, FR-FLOW-05, FR-GH-05, FR-REQ-05(진행률), FR-ACT 안내 | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 상세 패널 — FR-OFF-05, FR-LRN-01 | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 요구사항 패널 — FR-REQ-03~06 | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 활동 기록 — FR-ACT-01~04 | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 스탠드업·회의 — FR-MTG-01~05, FR-LRN-05 · 12 | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 일정판 — FR-PLAN-02~04 | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 내 습관 체크 — FR-HAB | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 내 학습 타임라인 — FR-LRN-08 | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 회고 리포트 — FR-REP-01~06, FR-REP-07, FR-ACT-03 | TODO | 라우터 없음 — 결정 필요 | TODO | TODO | D · TODO: 이름 | 시작 전 |

**묶음**: TODO — 팀 회의에서 정한다.

**route**: 기존 데모에는 URL 라우터가 없다. 한 페이지 위에 3D 장면과 HUD(상단 바 · 오른쪽 패널 탭 · 대화상자)를 겹쳐 쓰고, 주소에는 `?source=api`만 있다. 로그인 · 조직·레포 선택처럼 사무실 밖 화면은 주소별 화면으로 나누고 라우터를 추가한다(2026-10-28 결정). 주소 이름과 디자인은 더 토론한다(07-screens.md는 URL을 정하지 않는다).

### 기존 데모 코드와의 대응

기존 3D 데모(`SamdoRyu/warehouseDemo_fork`)의 화면 코드를 읽어 정리했다(2026-10-28, 실행은 안 함). 경로는 데모 저장소 기준이다. 화면 쪽 상세는 07-screens.md 「기존 데모 화면과의 대응」, 3D 부품별 판정은 ARCHITECTURE.md 「기존 데모와의 대응」.

> **구현 스택은 아직 정하지 않았다.** 아래 데모 경로 · 파일 · 명령은 데모 스택(React · TypeScript · Three.js · Hono)을 이어 쓸 때의 기준이다. HTML · CSS · JavaScript 위주로 새로 구현하게 되면 같은 내용을 "참고할 동작과 구조"로 읽는다 — ARCHITECTURE.md 「기술 스택」.

| 화면 | 기존 컴포넌트 · 경로 | 판정 | 수정 핵심 | 선행 데이터 (backend) |
| --- | --- | --- | --- | --- |
| 로그인 | 없음 | 신규 | GitHub 로그인 버튼 · 실패 상태. 대화상자 틀은 `src/ui/Dialogs.tsx` `Modal`을 참고할 수 있다 | B1 |
| 조직·레포 선택 | 없음 | 신규 | 조직 · 레포 목록, 승인 요청 안내 | B1 · B2 |
| 사무실 열기 (팀장) | 없음 | 신규 | 레포 · 기간 · 명세 문서 경로 입력 | B2 · B6 · B12 |
| 수집 동의 (팀원) | 없음 | 신규 (2026-10-28 추가 결정으로 채택) | — | B10 |
| **3D 사무실 (메인)** — 수정 규모 큼 | `src/scene/OfficeScene.tsx` · `TaskBox.tsx` · `TaskBoxes.tsx` · `slots.ts` · `MeetingRoomState.tsx` · `DragLayer.tsx` · `dropTarget.ts` · `Avatar.tsx` · `Overlays.tsx` · `src/office/layout.ts` | 수정 · 확장 | 공간 · 책상 · 아바타 · 카메라 · 이동 애니메이션은 데모와 같게 구현. PR 삼각기둥 등 새 도형, 요구사항 구역 · 진행률, 검토 영역 위치, 드래그 대상 변경(담당 · 상태 → 구역 연결 · 회의실), 사람별 업무량 표현(신호 다이아 · 업무량 원판 · 기울기) 제거 | B2 · B4 · B6 · B7 · B9 · B13 |
| [제안] 2D 팀 보드 | 비슷한 것: `src/ui/KanbanView.tsx` · `ListView.tsx`(작업 목록) | 신규 · 채택 전 착수 안 함 | 안건 아이콘 · 게시판 영역 | [제안] B9 · B13 |
| 상단 바 | `src/ui/TopBar.tsx` | 수정 | KPI · 균형 칩(가장 바쁜 팀원) 대신 요구사항 진행률 · 열린 PR 수 · 평균 리뷰 대기 · 동기화 상태. 레포 드롭다운 · D-day 신규. 오버레이 전환은 정보 뷰(최근 변화 · 담당 · 막힘) 전환으로 | B2 · B6 · B7 · B12 |
| 상세 패널 | `src/ui/Detail.tsx` · `src/ui/RightPanel.tsx` | 수정 | 편집 입력(상태 · 우선순위 · 포인트 · 담당 · 마감)을 읽기 전용과 GitHub 링크로. 연결 PR · 리뷰 상태 추가. 팀원 상세의 업무량 게이지(`Vitals`) 제거. 평가 버튼 · 추천·비추천과 이유 | B2 · B4 · B9 |
| 요구사항 패널 | 없음 — `RightPanel.tsx`의 탭 구조를 참고 | 신규 | 요구사항 목록 · 진행률 · 빈틈 · 연결 후보 | B6 |
| 활동 기록 | 없음 — `src/ui/ActivityFeed.tsx`는 데이터 변경 로그라 다른 기능 | 신규 (2026-10-28 추가 결정으로 채택) | — | B10 |
| 스탠드업·회의 | `src/ui/MeetingDialogs.tsx`(회의 시작 · 종료 · 회의록) · `src/scene/MeetingRoomState.tsx` | 수정 · 확장 | 회의 종료 결과(담당 지정 · 상태 변경 · 백로그)를 결정 · 배운 점 기록으로. 스탠드업 화면은 신규 | B12 · B9 |
| 일정판 | `src/ui/WeekView.tsx`(팀원 × 날짜 그리드) | 수정 | 화면 모양(팀원 × 날짜 그리드 · 탭)은 유지하고, 칸 · 행의 포인트 합계 · 팀원별 업무량 막대 · 드래그로 마감일 바꾸기(GitHub 원본)는 뺀다(2026-10-28 추가 결정). 마감은 작업 필드 새로 기획 예정 | B12 |
| 내 습관 체크 | 없음 | 신규 | — | B8 |
| 내 학습 타임라인 | 없음 | 신규 | — | B9 |
| 회고 리포트 | 없음 — 회의록(`MeetingHistoryDialog`)이 가장 가깝다 | 신규 | 리포트 · 마크다운 내보내기 | B11 |

v1 화면 목록에 없는 기존 화면 — `src/ui/TeamView.tsx`(팀원별 업무량 카드), `src/ui/Suggestions.tsx`(업무 재배정 추천), `src/ui/KanbanView.tsx` · `ListView.tsx` · `FilterBar.tsx`(작업 편집 · 목록), 작업 · 팀원 만들기 대화상자(`src/ui/Dialogs.tsx`) — 는 사람별 비교이거나 GitHub 원본을 바꾸는 기능이라 빼거나 다른 용도로 바꾼다. 칸반 · 목록 탭은 화면 모양만 유지하고, 상태 · 담당 편집(GitHub 원본)은 뺀다(2026-10-28 추가 결정).

화면에 붙은 요구사항 ID는 기획안 「화면 구성」 표의 "관련 요구사항" 열을 바탕으로 07-screens.md 「화면 목록」과 맞췄다. [제안]이 붙은 화면 · 요소(2D 팀 보드)는 requirements-changes.md에서 아직 후보인 것에 기대므로 채택 전에는 착수하지 않는다. 수집 동의 · 활동 기록 · 상단 바 안내 · 활동 유형 분포(FR-ACT)와 3D 사무실의 평가 · 회의실 · 게시판 도움 요청(FR-LRN-15~20 포함)은 2026-10-28에 채택했다(07-screens.md).

**담당 쏠림**: 기획안 「역할 분담」대로면 화면 15개를 모두 D 한 명이 맡는다. 데모를 참고해도 쏠림은 크게 줄지 않는다 — 15개 중 데모에 비슷한 화면이 있는 것은 5개(3D 사무실 · 상단 바 · 상세 패널 · 스탠드업·회의 · 일정판)이고 나머지 10개는 신규다(그중 2D 팀 보드 1개는 [제안]이라 지금 착수하지 않음 → 착수할 신규 9개). 게다가 가장 큰 3D 사무실은 데모에 비슷한 화면이 있지만 구현 범위가 넓다. 로그인 · 조직·레포 선택처럼 B1 · B2와 붙어 있는 화면, 스탠드업 · 일정판처럼 B12와 붙어 있는 화면을 어떻게 나눌지 포함해 역할 조정은 팀이 정한다.

### 시작 순서

- 기획안 「2주 개발 일정」 기준 — **5일차까지 우리 조 레포의 이슈가 기존 사무실에 상자로 뜨는 것**이 첫 목표다(연동·사무실 단계: 3D 사무실 · 상세 패널). 그 전에는 가짜 어댑터로 먼저 화면을 띄운다.
- **7일차 1차 데모**에서는 요구사항 구역, 진행률, PR 추천·비추천 평가까지 보여야 한다(요구사항 추적 단계: 요구사항 패널 · 평가 표시). 평가 표시(FR-LRN-01~03)는 2026-10-28에 채택되어 데모 범위에 든다.
- 나머지 화면은 흐름·리포트 단계다. Should · Could 와 활동 기록 제안은 8~10일차에 Must 가 끝났을 때만 착수하고, 10일차에 기능을 동결한다.
- 정보 뷰가 일정상 밀리면 패널 목록으로 대신한다.
- 이 문서와 이슈 본문이 어긋나면 **이슈 본문이 최신**이다.

## 충돌 방지

### 마음껏 건드려도 되는 곳

기존 데모는 화면별 폴더가 없고 `src/ui/`(HUD) · `src/scene/`(3D)에 파일을 나란히 둔다. 새 화면은 자기 파일(예: `src/ui/<화면이름>.tsx`)과 그 화면 전용 CSS에서 작업한다. 화면별 폴더를 만들지는 결정 필요 — 폴더 구조 전면 변경은 하지 않는다.

### 건드리기 전에 말해야 하는 곳

| 파일 | 왜 |
| --- | --- |
| 공용 컴포넌트(상단 바 · 상세 패널 · 도형 5종 · 정보 뷰(최근 변화 · 담당 · 막힘)) | 여러 화면이 쓴다. 수정은 **별도 PR** 로 분리하고 먼저 공유한다. 경로는 `src/ui/TopBar.tsx` · `src/ui/Detail.tsx` · `src/scene/TaskBox.tsx`(도형) · `src/scene/shared.tsx`(공용 도형 · 재질) · `src/scene/Overlays.tsx`(정보 뷰 틀) |
| 계약 파일 — `src/data/types.ts` · `src/data/DataSource.ts` · `src/store.ts` · `src/office/layout.ts` · `src/data/selectors.ts` · `src/data/insights.ts` | 화면 · 서버 · mock이 함께 기댄다. 데모의 `CLAUDE.md`도 이 파일들을 계약 파일로 묶어 둔다. 바꾸려면 B · C와 먼저 합의한다 |
| 기존 3D 팀 오피스의 3D 표현 코드(`src/scene/`) | 표현을 참고해 구현한다. 새 모델링은 하지 않는다(기획안 「기능 동결 규칙」) |
| 의존성 목록 파일(`package.json` · `package-lock.json`) | 라이브러리 추가는 사전 합의(`.claude/rules/security.md`) |
| `src/App.tsx` · `src/ui/Hud.tsx` · `src/ui/RightPanel.tsx` · `src/ui/uiState.ts` · `src/index.css` · `vite.config.ts` · `tsconfig*.json` | 전원이 공유하는 레이아웃 · 설정 |

### 인증은 화면 작업이 아니다

GitHub App 로그인과 역할 판정은 서버 공유 인프라를 건드린다. 화면 브랜치에 섞으면 전원과 충돌한다. **backend 담당 기호 B1(계정·권한 — 00-backlog의 IB 번호가 아니다)에서 구현한다** — [BACKEND_ASSIGNMENTS.md](./BACKEND_ASSIGNMENTS.md). 화면은 역할에 따라 버튼을 숨길 수 있지만, 그것을 권한 확인으로 보지 않는다(FR-AUTH-04).

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
- [ ] 팀원 간 순위 · 비교 화면이 없고, 개인 지표(습관 체크 · 학습 타임라인 · 회고 리포트)는 본인 화면에만 보인다 (FR-FLOW-06 · NFR-06)
- [ ] 막힘 표시는 막혔다는 사실만 보이고 이유 문구가 없다 (FR-FLOW-02 · 2026-10-29 결정)
- [ ] `npm run lint` · `npm run build`(타입 검사 · 3D lazy 번들 검사 포함) · `npm run test:contract`를 돌린 결과를 PR 「검증」에 적는다. 3D를 바꿨으면 `npm run test:e2e`도 돌린다(Chrome 필요)

## 알려진 문서 ↔ 디자인 차이

TODO: 대응 내용 없음 (삭제 여부 확인) — 디자인 원본이 아직 없다.
