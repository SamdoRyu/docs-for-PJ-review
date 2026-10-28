# Backend Assignments

MVP backend 구현 Issue 의 담당·상태 표다. 화면 구현은 [SCREEN_ASSIGNMENTS.md](./SCREEN_ASSIGNMENTS.md) 를 본다.

- **배정 기준: 기획안 「역할 분담」** — A 기획, B 연동 백엔드, C 도메인 백엔드, D 3D·프론트엔드
- 작업 프롬프트: [CLAUDE_PROMPTS.md](./CLAUDE_PROMPTS.md) 「1. 요구사항 ID로 기능 구현」
- git 흐름(브랜치·커밋·PR·머지): `.claude/rules/git-workflow.md`
- 모듈 경계: [ARCHITECTURE.md](./ARCHITECTURE.md) · 실행·검증 명령: [PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md) · Must 이슈 목록: [00-backlog.md](./00-backlog.md) 「이슈 백로그 (Must)」

## 이 표를 믿는 범위

> **이 문서는 snapshot / cross-check 전용이다.** 착수 가능 여부는 이 표가 아니라 아래 순서로만 판정한다.
>
> 1. 배정 Issue **본문의 상태**와 "요구사항 ID" 칸
> 2. 「선행」의 Issue 마다, 그 PR 이 `origin/develop` 에 squash merge 됐는지
> 3. 필요한 TBD 가 05-policy.md 서비스 정책에서 [?] 가 아닌지

- 1~3 을 모두 통과했는데 이 표가 막혀 있으면 작업을 막지 않고 "BACKEND_ASSIGNMENTS 가 GitHub Issue 와 불일치 — 문서 stale" 만 보고한다.
- 반대로 Issue 본문이 막혀 있으면 이 표가 열려 있어도 막힌 것이다.
- 선행은 Issue 가 open/closed 인지로 판단하지 않는다. 이슈는 `develop` → `main` 승격 PR 에서 `Closes #N` 으로 닫으므로, develop 에 이미 들어간 작업의 이슈도 한동안 open 이다.
- 브랜치 이름은 번호에서 추론하지 말고 Issue 에 적힌 값을 쓴다(`<type>/<이슈번호>-<slug>`).

## 담당 표

| Symbol | Issue | 제목 | Status | 선행 | 담당 | canonical branch | Downstream |
| --- | --- | --- | --- | --- | --- | --- | --- |
| B0 | TODO | TBD 9건 팀 결정 (TBD-01~09) | decision-only | — | A 기획 · TODO: 이름 | (없음) | 전체 |
| B1 | TODO | 계정·권한 — FR-AUTH-01~05 | TODO | TODO | B 연동 백엔드 · TODO: 이름 | TODO | TODO |
| B2 | TODO | GitHub 연동 · 동기화 — FR-GH-01~06 | TODO | TODO | B 연동 백엔드 · TODO: 이름 | TODO | TODO |
| B3 | TODO | 데이터 어댑터(github · fake · local) — NFR-08 | TODO | TODO | B 연동 백엔드 · TODO: 이름 | TODO | TODO |
| B4 | TODO | PR 상태 동기화 | TODO | TODO | B 연동 백엔드 · TODO: 이름 | TODO | TODO |
| B5 | TODO | 보안 · 동기화 비기능 — NFR-04, NFR-05, NFR-09, NFR-10 | TODO | TODO | B 연동 백엔드 · TODO: 이름 | TODO | TODO |
| B6 | TODO | 요구사항 추적 — FR-REQ-01~07 | TODO | TODO | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B7 | TODO | 진행·막힘 — FR-FLOW-01~07 | TODO | TODO | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B8 | TODO | 협업 기본기 · 컨벤션 피드백 — FR-HAB-01~11 | TODO | TODO | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B9 | TODO | [제안] PR 투표 · 판정 로직 — FR-LRN | TODO | TODO | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B10 | TODO | [제안] 활동 기록 — FR-ACT-01~04 | TODO | TODO | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B11 | TODO | 회고 리포트 · 기술 결정 카드 — FR-REP-01~06, [제안] FR-REP-07 | TODO | TODO | C 도메인 백엔드 · TODO: 이름 | TODO | TODO |
| B12 | TODO | 회의·스탠드업 · 일정판 서버 — FR-MTG-01~05, FR-PLAN-01~04 | TODO | TODO | TODO — 기획안 「역할 분담」에 서버 담당이 없다(화면은 D) | TODO | TODO |

**담당 칸의 이름은 비어 있다. 이슈가 만들어지면 담당자를 배정한다.** 역할(A~D)은 기획안 「역할 분담」 표 그대로다.

## 지금 상태 (2026-10-28 snapshot)

- **GitHub Issue 가 아직 없다.** Must 이슈 후보는 [00-backlog.md](./00-backlog.md) 「이슈 백로그 (Must)」에 있다.
- 기존 3D 팀 오피스 코드와 DataSource 구조는 1~2일차에 4명이 함께 파악한다(기획안 「역할 분담」). 그 전에는 가짜 어댑터로 먼저 화면을 띄운다(기획안 「리스크 및 대응 방안」).
- 5일차까지 우리 조 레포의 이슈가 기존 사무실에 상자로 뜨는 것이 첫 목표이고, 7일차 1차 데모에서는 요구사항 구역, 진행률, PR 찬성·반대 투표까지 보여야 한다(기획안 「2주 개발 일정」).
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
| 사무실 열기 (팀장) · 수집 동의 (팀원) | B2 · B6 · B10 · B12 |
| 3D 사무실 (메인) · 2D 팀 보드 · 상세 패널 | B2 · B4 · B6 · B7 · B9 |
| 상단 바 | B2 · B7 · B12 |
| 요구사항 패널 | B6 |
| 활동 기록 | B10 |
| 스탠드업·회의 | B9 · B12 |
| 일정판 | B12 |
| 내 습관 체크 | B8 |
| 내 학습 타임라인 | B9 |
| 회고 리포트 | B11 |
