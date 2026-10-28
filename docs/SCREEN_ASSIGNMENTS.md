# Screen Assignments

누가 어느 화면을 맡는지, 그 화면의 디자인 원본이 어디인지 적는 표다.

- **Claude Code 로 바로 시작하기: [CLAUDE_PROMPTS.md](./CLAUDE_PROMPTS.md)** ← 복사해 붙여 넣는 프롬프트
- 만드는 방법: [ARCHITECTURE.md](./ARCHITECTURE.md)
- 실행·검증 명령: [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md)
- git 흐름(브랜치·커밋·PR·머지): `.claude/rules/git-workflow.md`
- **backend 구현 담당·상태: [BACKEND_ASSIGNMENTS.md](./BACKEND_ASSIGNMENTS.md)** ← 로그인 · GitHub 동기화 · 투표 판정 · 막힘 계산 · 저장은 이 문서가 아니라 저기다

이 문서는 **화면 구현 담당 문서**다. 화면은 기획안 「역할 분담」에 따라 **D(3D·프론트엔드)** 가 맡는다 — FR-OFF, 정보 뷰, 투표 표시와 회의실 드래그(FR-LRN-03 · 04), 상단 바와 패널, FR-MTG · FR-PLAN · FR-ACT 화면.

화면 목록과 각 화면에 보이는 것은 [07-screens.md](./07-screens.md) 「화면 목록」이 기준이다. TODO: 디자인 원본(파일 · 링크)이 아직 없다 — 07-screens.md 「디자인」의 [?].

## 시작하기 전에

```bash
git fetch origin
git switch develop
git pull --ff-only origin develop
```

TODO(스택 확인 후): 의존성 설치와 개발 서버 실행 명령 — [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md). 화면 작업은 `DATA_SOURCE=fake` 로 GitHub 없이 한다(환경 변수 가이드 「환경별 설정」).

## 담당 표

**담당 칸의 이름은 비어 있다. 팀 회의에서 채운다.**

| 화면 | 디자인 줄 | route | 묶음 | 이슈 | 담당 | 상태 |
| --- | --- | --- | --- | --- | --- | --- |
| 로그인 — FR-AUTH-01 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 조직·레포 선택 — FR-GH-01 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 사무실 열기 (팀장) — FR-REQ-01, FR-PLAN-01 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 수집 동의 (팀원) — [제안] FR-ACT | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 3D 사무실 (메인) — FR-OFF-01~06, FR-FLOW-02, [제안] FR-LRN-03 · 04 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 2D 팀 보드 — 대응 요구사항 ID 없음 (기획안 "(제안)") | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 상단 바 — FR-PLAN-01, FR-FLOW-05, FR-GH-05 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 상세 패널 — FR-OFF-05, [제안] FR-LRN-01 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 요구사항 패널 — FR-REQ-03~06 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 활동 기록 — [제안] FR-ACT-01~04 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 스탠드업·회의 — FR-MTG-01~05 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 일정판 — FR-PLAN-02~04 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 내 습관 체크 — FR-HAB | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 내 학습 타임라인 — [제안] FR-LRN-08 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |
| 회고 리포트 — FR-REP, [제안] FR-ACT-03 | TODO | TODO(스택 확인 후) | TODO | TODO | D · TODO: 이름 | 시작 전 |

**묶음**: TODO — 팀 회의에서 정한다.

화면에 붙은 요구사항 ID는 기획안 「화면 구성」 표의 "관련 요구사항" 열 그대로다.

### 시작 순서

- 기획안 「2주 개발 일정」 기준 — **5일차까지 우리 조 레포의 이슈가 기존 사무실에 상자로 뜨는 것**이 첫 목표다(연동·사무실 단계: 3D 사무실 · 상세 패널). 그 전에는 가짜 어댑터로 먼저 화면을 띄운다.
- **7일차 1차 데모**에서는 요구사항 구역, 진행률, PR 찬성·반대 투표까지 보여야 한다(요구사항 추적 단계: 요구사항 패널 · 투표 표시).
- 나머지 화면은 흐름·리포트 단계다. Should · Could 와 활동 기록 제안은 8~10일차에 Must 가 끝났을 때만 착수하고, 10일차에 기능을 동결한다.
- 정보 뷰가 일정상 밀리면 패널 목록으로 대신한다.
- 이 문서와 이슈 본문이 어긋나면 **이슈 본문이 최신**이다.

## 충돌 방지

### 마음껏 건드려도 되는 곳

TODO(스택 확인 후): 자기 화면 폴더 경로. 그 화면에서만 쓰는 조각은 같은 폴더에 둔다.

### 건드리기 전에 말해야 하는 곳

| 파일 | 왜 |
| --- | --- |
| 공용 컴포넌트(상단 바 · 상세 패널 · 도형 5종 · 정보 뷰) | 여러 화면이 쓴다. 수정은 **별도 PR** 로 분리하고 먼저 공유한다. 경로는 TODO(스택 확인 후) |
| 기존 3D 팀 오피스의 3D 표현 코드 | 재사용 대상이다. 새 모델링은 하지 않는다(기획안 「기능 동결 규칙」) |
| 의존성 목록 파일 | 라이브러리 추가는 사전 합의(`.claude/rules/security.md`) |
| TODO(스택 확인 후) | 레이아웃 · 설정 파일처럼 전원이 공유하는 파일 |

### 인증은 화면 작업이 아니다

GitHub App 로그인과 역할 판정은 서버 공유 인프라를 건드린다. 화면 브랜치에 섞으면 전원과 충돌한다. **backend 의 B1(계정·권한)에서 구현한다** — [BACKEND_ASSIGNMENTS.md](./BACKEND_ASSIGNMENTS.md). 화면은 역할에 따라 버튼을 숨길 수 있지만, 그것을 권한 확인으로 보지 않는다(FR-AUTH-04).

### `modify/` 는 화면별로 쓴다

TODO: 대응 내용 없음 (삭제 여부 확인) — 구현이 기획 문서와 달라졌을 때의 기록 방식이 기준 문서에 없다.

## 브랜치와 PR

`.claude/rules/git-workflow.md` 를 따른다. 화면 작업은 `feat/<이슈번호>-<slug>` 다.

```bash
git fetch origin
git switch develop
git pull --ff-only origin develop
git switch -c feat/<이슈번호>-<slug>
```

PR base 는 `develop` 이다. PR 에는 `Refs #<이슈번호>` 와 "관련 요구사항"(이슈에 적은 ID)을 넣고, PR 템플릿 「UI 변경」에 **화면 스크린샷을 첨부한다.**

## 공통 완료 조건

화면 이슈의 Acceptance Criteria 바탕이 되는 줄이다. **화면별 이슈 본문에 구체적으로 정해진 것이 이 줄보다 우선한다.**

- [ ] 07-screens.md 「모든 화면 공통 인수 조건」 — TODO: 팀이 두 줄로 쓴 뒤 옮긴다
- [ ] 팀원 간 순위 · 비교 화면이 없고, 개인 지표(습관 체크 · 학습 타임라인 · 회고 리포트)는 본인 화면에만 보인다 (FR-FLOW-06 · NFR-06)
- [ ] 막힘 표시에는 이유 문구가 붙는다 (FR-FLOW-02)
- [ ] TODO(스택 확인 후): lint · build 통과 명령

## 알려진 문서 ↔ 디자인 차이

TODO: 대응 내용 없음 (삭제 여부 확인) — 디자인 원본이 아직 없다.
