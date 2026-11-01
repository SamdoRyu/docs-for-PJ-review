# 공통 계약 — 오피스 v1

> 문서: CONTRACTS.md · 상태: **초안 — 팀 확정 전** · 마지막 갱신: 2026-11-01
> 이 문서를 읽는 에이전트에게: 여러 Claude Code 세션이 같은 계약을 쓰도록 모아 둔 곳이다. 여기 없는 계약을 세션마다 따로 만들지 말고, 필요하면 이 문서를 먼저 고치는 PR을 낸다(「9. 계약 변경 절차」). 요구사항 · 정책의 기준은 03 · 05 · 06이고, 이 문서는 그것을 구현 모양으로 옮긴 것이다.

**항목마다 붙인 표시**

| 표시 | 뜻 |
| --- | --- |
| **[확정]** | 결정 기록 · 03 · 05 · 06에서 이미 정한 것을 옮겼다. 이 문서에서 바꾸지 않는다 |
| **[계약 초안]** | 확정 결정을 구현하려면 필요한 모양(이름 · 필드 · 경로). 팀이 1차 단계에서 확정한다. 확정 전에도 이 모양으로 시작해도 되지만, 바꿀 때는 「9」 절차를 따른다 |
| **[구현 기본값]** | 합의된 기본값으로 시작하고, 구현 중 바꿔도 재승인이 필요 없는 값(2026-11-01 추가 결정). 단 보안 · 인증 · 권한 · 데이터 무결성에 닿는 값을 바꿀 때는 「9」 계약 PR로 낸다. 목록은 「10」 |
| **[결정 필요]** | 팀이 아직 승인하지 않은 세부 정책. 구현이 기대면 안 되는 부분이라, 기본값 없이 비워 두거나 설정 한 곳으로 모은다 |

스택 [확정]: 프론트 React + TypeScript + R3F + Zustand, 백엔드 Java 21 · Gradle · Spring Boot(MVC) · Spring Data JPA · Spring Security 세션, DB MySQL 8.x(2026-11-01 결정 · 2026-11-01 추가 결정). Spring Boot 버전 · migration 도구 · 구체 DB 설정 · 세션 저장소 세부는 구현 때, 배포 플랫폼은 Spring Boot 초기 프로젝트 구축 단계에서 정한다 — ARCHITECTURE.md 「기술 스택」.

---

## 1. 공통 원칙 [확정]

- 앱은 GitHub를 읽기만 한다. 앱이 만든 값은 앱 DB에만 저장한다 (CON-02 · P3).
- GitHub 원본을 바꾸는 사용자 동작(이슈 생성 · 닫기, PR 생성 · 병합 · 닫기, 라벨 · 담당자 · 상태 변경)은 API가 없다 — 화면이 해당 GitHub 웹페이지로 이동시키고, 바뀐 상태는 다음 동기화에서 읽는다. 앱 API의 쓰기는 앱이 관리하는 데이터(요구사항 연결 · 평가 · 안건 · 작업 마감 등)만이다 (P3 — 2026-11-01 추가 결정).
- 시각은 UTC로 저장 · 응답한다(ISO 8601 문자열). 한국 시간 변환은 화면에서 한다. 스탠드업 날짜처럼 "하루"를 뜻하는 날짜 값은 한국 시간 기준 날짜(`YYYY-MM-DD`)다 [구현 기본값].
- 사람별 합계 · 순위 · 비교 필드를 Entity에도 DTO에도 두지 않는다 (FR-FLOW-06 · FR-LRN-09 · P6).
- 개인 지표(학습 타임라인 · 회고 리포트 · 기술 결정 카드)는 본인과 강사의 요청에만 응답한다 (NFR-06 · P5).
- 평가자 · 동료 피드백 작성자는 저장하되 응답에 담지 않는다 (TBD-13 · TBD-12).
- 사무실 단위 데이터는 모두 사무실(Workspace)에 속하고, 모든 조회 · 쓰기 · 실시간 알림이 사무실 접근 검사를 거친다 (P2 — 2026-11-01 결정).
- 위치는 원본 상태 · 담당자에서만 계산한다. `RequirementLink` · `AgendaItem`은 원본 이슈 · PR의 상태 · 위치를 바꾸지 않는다 (05 「상태값」 — 2026-11-01 결정).

## 2. 도메인 모델과 Entity 관계 [계약 초안]

이름은 코드 컨벤션 (BE) 「이름」을 따른다. ID 형식(숫자 · UUID · 접두사 문자열)은 [결정 필요] — 데모 화면은 접두사 문자열(`t_` 등)을 쓴다.

| Entity (이름 초안) | 범위 | 주요 필드 (초안) | 관계 | 근거 |
| --- | --- | --- | --- | --- |
| `Workspace` (사무실) | 서버 | 종류(`GITHUB` · `DEMO`), GitHub 조직 · 레포 식별자, 설치 ID, 열린 시각 | 1–1 `TeamSettings`, 1–1 `SyncState`, 1–N 아래 사무실 단위 항목 전부. `DEMO`는 Seed 데이터로 채우고 GitHub를 부르지 않는다 | 06 사무실 · P2 |
| (시연 사무실 접근 대상) | 사무실(`DEMO`) | 들어갈 수 있는 사용자 — 우리 조 실제 GitHub 계정 · 심사용 계정, 각자의 역할. 지정된 강사는 `InstructorAssignment`로 들어온다 | Workspace(`DEMO`) N–1, AppUser N–1. [확정] 명시적으로 등록한 대상만 들어간다(2026-11-01 추가 결정). 등록 방식(서버 설정 · 배포 때 시드)과 이름은 구현 때. 우리 조 계정의 역할은 우리 조 실제 사무실의 역할을 따른다 [구현 기본값] | P2 · P15 |
| `TeamSettings` | 사무실 | 프로젝트 시작 · 끝, 명세 문서 경로, `reviewWaitHours`, `noCommitHours`, 활동 기록 수집 동의 | Workspace 1–1 | 06 팀 설정 |
| `AppUser` | 서버 | 계정 종류(`GITHUB` · `REVIEW`), GitHub 사용자 ID(`GITHUB`만), 로그인 이름, 프로필 사진 주소. 이메일은 저장하지 않는다 `[제안]` | — | NFR-04 · P15 |
| `ReviewAccount` (심사용 계정) | 서버 | 로그인 ID, 비밀번호 해시(평문 금지 — Spring `PasswordEncoder`), 사용 가능 여부 | AppUser(`REVIEW`) 1–1. [확정] 시연 사무실에만 접근하고 GitHub 권한이 없다. 심사용 로그인이 켜진 환경에서만 쓰인다. 비밀번호 · 해시를 저장소에 커밋하지 않는다 (2026-11-01 추가 결정) | FR-AUTH-01 · P15 |
| (승인된 강사 계정) | 서버 | [확정] **서버 설정 허용 목록**의 GitHub 사용자 ID 목록 — DB에 두지 않고 관리 화면이 없다(2026-11-01 추가 결정 — ARCHITECTURE.md 「인증과 권한」 대안 A). 설정 이름은 환경 변수 가이드 | — | P1 |
| `InstructorAssignment` | 사무실 | 강사 계정, 지정한 사람, 지정 시각, 해제 시각 | Workspace N–1, AppUser N–1. 해제해도 행을 지우지 않고 해제 시각을 남기는 것이 `[제안]`(지정 전후 기록 보존 확인용) | FR-AUTH-06 · P1 |
| `Member` (팀원) | 사무실 | 사용자, 책상 자리, GitHub 권한(admin 여부) · `syncedAt` | Workspace N–1, AppUser N–1. 서버 설정 허용 목록의 강사 계정은 콜라보레이터여도 팀원이 아니다 [확정 — P1] | 06 팀원 · FR-GH-04 |
| `Task` (이슈 앱 모델) | 사무실 | `githubIssueNumber`, 제목, 열림 · 닫힘, 닫힘 사유(완료 · 계획 안 함), 라벨, 만든 · 고친 · 닫힌 시각, `syncedAt` | Workspace N–1. 담당자는 `TaskAssignee`로 둔다(2026-11-01 추가 결정 — 이전 초안의 "담당 Member N–1"을 대신함) | 06 GitHub 원본 데이터 |
| `TaskAssignee` (이슈 담당자) | 사무실 | 이슈, 팀원, `syncedAt` (이슈 + 팀원은 하나만) | Task N–1, Member N–1 — **Task와 Member의 다대다(N–M)를 표현하는 중간 Entity**. [확정] 담당자 수에 서버 제한이 없다(팀은 1명 권장). 동기화가 GitHub 담당자 전원으로 맞춘다. 팀원이 아닌 GitHub 담당자는 넣지 않는다 [구현 기본값]. 대표 담당자는 저장하지 않고 계산한다(「3」) (2026-11-01 추가 결정) | FR-GH-02 · FR-OFF-02 · P14 |
| `Submission` (PR 앱 모델) | 사무실 | PR 번호, 제목, 작성자, 열림 · Draft · 머지 · 닫힘, `headSha`, 리뷰 요청 · 리뷰, 라벨, 커밋 시각, `syncedAt` | Workspace N–1 | 06 · FR-GH-02 |
| `SubmissionIssueLink` | 사무실 | PR, 이슈, 출처(GitHub 연결 종류) | Submission N–M Task. [확정] **GitHub가 공식으로 인식하는 연결 관계만** 저장한다 — PR 본문 · 제목의 `#N` 언급만으로 만들지 않고, 앱이 추측해 만들지 않는다. 조회 API는 구현 때(2026-11-01 추가 결정) | FR-GH-04 |
| `Requirement` | 사무실 | ## 제목, 순서, 갱신 시각 | Workspace N–1 | FR-REQ-02 |
| `RequirementLink` | 사무실 | 요구사항, 이슈, 연결한 사람, 연결 시각 (요구사항 + 이슈는 하나만) | Requirement N–1, Task N–1 | FR-REQ-04 · 07 |
| `EvaluationRound` | 사무실 | **회차 ID**, 회차 번호(PR 안에서 1, 2, …), 시작 시점 `headSha`, 여는 사유(`NEW_PR` · `NEW_COMMIT` · `MEETING_REEVALUATE`), 시작 시각 | Submission 1–N. 회차 ID와 `headSha`는 별개 — 같은 `headSha`에 회차 여러 개 가능. 지우지 않는다 | FR-LRN-06 · P7 |
| `Evaluation` | 사무실 | 평가자, `recommend` · `not_recommend`, 이유(필수), 고친 시각 (회차 + 평가자는 하나만) | EvaluationRound N–1 | FR-LRN-01 · 02 |
| `AgendaItem` | 사무실 | 종류(이슈 · PR · 도움 요청 · 배운 점 공유 · 기타), 원본 참조(이슈 · PR · 도움 요청 중 하나 또는 없음), 제목, 배경, 해 본 것, 상태(확정 · 회의 완료 · 후보 · 거둠), 올린 사람, PR 결정, 결론, 후속 담당자 · 기한, 배운 점, 강사 피드백 요청 여부 | Workspace N–1, 원본 N–1(선택), Meeting N–1(선택) | 06 회의 안건 · FR-LRN-04~12 |
| `InstructorFeedback` | 사무실 | 안건(결정 기록), 강사, 내용, 시각 | AgendaItem 1–N | FR-LRN-22 |
| `Meeting` | 사무실 | 시작 · 끝, 참석자 | Workspace N–1, AgendaItem 1–N | 06 회의 |
| `Standup` | 사무실 | 작성자, 날짜(한국 시간 기준 날짜 [구현 기본값]), 어제 한 일, 막힌 것 | Workspace N–1, Member N–1 | FR-MTG-01 · 02 |
| `HelpRequest` · `HelpComment` | 사무실 | 요청자, 내용, 상태, "강사에게 요청" 여부, 닫을 때 배운 점, 도움이 된 코멘트 / 코멘트 작성자 · 내용 · 강사 답변 여부 | HelpRequest 1–N HelpComment, 스탠드업에서 넘어왔으면 Standup 참조(선택) | FR-LRN-15~21 |
| `TaskDueDate` | 사무실 | 이슈, 마감일, 입력한 사람 · 시각 | Task 1–1. [확정] 고칠 수 있는 사람은 그 사무실 ROLE-02와 그 이슈의 담당자(`TaskAssignee`) 전원 — P14. GitHub 이슈는 바뀌지 않는다 | FR-PLAN-02 · 03 (2026-11-01 결정 · 권한은 2026-11-01 추가 결정) |
| `DecisionCard` · `FollowUpQuestion` | 사무실 | PR, 본인, 9칸, 본인 확인 여부, 꼬리질문 요청 여부 / 강사, 내용 | DecisionCard 1–N FollowUpQuestion | FR-REP-07 · 11 · P11 |
| `RetroReport` | 사무실 | 본인, 배운 점, 만든 시각 (나머지 칸은 계산) | Member N–1 | FR-REP-01~03 |
| `ActivityRecord` · `PeerFeedback` | 사무실 | 06 「저장 항목」 칸 그대로 | Member N–1 | FR-ACT |
| `ConventionLibraryItem` | 서버 | 대상, 이름, 내용, 설명, 작성 강사, 수정 시각 | 서버 전체에 하나 | FR-LIB |
| `SyncState` | 사무실 | 마지막 성공 시각, 마지막 실패 시각 · code · 사유 | Workspace 1–1 | FR-GH-05 · P4 |

- 학습 타임라인 · 강사 요청함 · 막힘 · 진행률 · 팀 지표 · 요구사항 묶음은 **저장하지 않고 계산한다** [확정 — 06 「화면에 보이지만 따로 저장하지 않는 값」].
- 연관관계는 기본 단방향(N–1)으로 두고, 양방향은 필요한 곳만 쓴다 `[제안]` — 코드 컨벤션 (BE) 「저장」.

## 3. 표시 상태 · 공간 역할 · 우선순위 [확정]

05-policy.md 「상태값」 표가 기준이다. 요약:

| 대상 | 원본 상태 | 표시 상태 → 공간 역할(`placeRole`) |
| --- | --- | --- |
| 이슈 | 열림 · 담당자 없음 | 대기 → `backlog` |
| 이슈 | 열림 · 담당자 있음 | 작업 중 → `desk:<대표 담당자 memberId>` — 담당자가 여럿이어도 상자는 하나 [확정] |
| 이슈 | 닫힘 | 완료 또는 종료 → `done`. GitHub 닫힘 사유로 완료 · 종료를 구분해 보일 수 있다 [구현 기본값] |
| PR | 열림 | 검토 → `review` |
| PR | 머지 | 완료 → `done` |
| PR | 닫힘(머지 안 됨) | 종료 → `done` [확정 — FR-LRN-07], 미병합 종료 표시 [구현 기본값] |
| PR | Draft | 열린 PR → `review`, Draft 배지 [구현 기본값] |

- 우선순위: `priority:high` · `priority:medium` · `priority:low` 라벨, 없으면 중립. 라벨 이름 → 값 대응은 상수 한 곳. 라벨이 여러 개면 가장 높은 값 `[제안]`.
- 회의 안건은 `meeting`에 `AgendaItem`으로 따로 놓이고, 원본은 위 표대로 제자리다.
- 대표 담당자 [확정 — 결정적인 규칙으로 고름 · 2026-11-01 추가 결정]: 규칙은 [구현 기본값] 그 사무실 팀원인 담당자 중 GitHub 사용자 ID가 가장 작은 사람이다. 팀원인 담당자가 없으면 `backlog`. 상세 화면에는 담당자 전원을 보인다.

## 4. API [계약 초안]

- 경로는 `/api/**`. 사무실 단위는 `/api/workspaces/{workspaceId}/…`. 아래는 묶음과 역할만 정한 초안이고, 세부 경로 · 쿼리 이름은 확정 때 정한다.
- 목록 API는 페이징 · 정렬(`page` · `size` · `sort`)을 받는다 `[제안]`.
- 모든 쓰기는 CSRF 토큰이 필요하다(「6」).

| 묶음 | 메서드 · 경로 (초안) | 할 수 있는 역할 [확정 정책에서] |
| --- | --- | --- |
| 상태 확인 | `GET /api/health` | 누구나 |
| 로그인 · 세션 | GitHub 로그인 시작 · 콜백(Spring Security 경로), 심사용 ID/PW 로그인(Spring Security 폼 로그인 경로 — 심사용 로그인이 켜진 환경에서만 존재), `POST /api/logout`, `GET /api/me`(사용자 · 사무실별 역할) | 로그인 시작은 누구나, 나머지는 로그인 사용자. 심사용 로그인도 로그인 뒤 같은 세션 · 검사를 거친다(P15) |
| 사무실 | `GET /api/workspaces`(내가 들어갈 수 있는 사무실), `POST /api/workspaces`(사무실 열기) | 목록: 로그인 사용자 — 심사용 계정에게는 시연 사무실만 나온다. 열기: 그 레포의 ROLE-02 |
| 전체 데이터 | `GET …/snapshot` | 그 사무실에 접근 가능한 사람. 개인 지표 필드는 본인 · 강사만 |
| 변경 알림 | `GET …/events` (SSE) | 위와 같음 — 연결할 때 검사 |
| 동기화 | `POST …/sync`, `GET …/sync-status` | 수동 동기화: ROLE-01 · 02. 강사는 할 수 없다 [구현 기본값]. 상태: 접근 가능한 사람 |
| 팀 설정 · 강사 지정 | `GET` · `PUT …/settings`, `PUT` · `DELETE …/instructors/{login}` | 보기: 접근 가능한 사람. 바꾸기 · 지정: ROLE-02 |
| 요구사항 | `GET …/requirements`, `POST …/requirement-links`, `DELETE …/requirement-links/{id}` | 보기: 접근 가능한 사람. 연결: ROLE-02 |
| PR 평가 | `GET …/submissions/{id}/rounds`, `PUT …/submissions/{id}/evaluations/mine`(현재 회차) | 평가: ROLE-01 · 02, 작성자 제외. 강사 `forbidden_role` |
| 회의 안건 · 회의 | `POST …/agenda-items`, `POST …/agenda-items/{id}/withdraw`, `POST …/agenda-items/{id}/decision`, `POST …/agenda-items/{id}/feedback-request`, `POST …/meetings`, `POST …/meetings/{id}/end` | ROLE-01 · 02. 거두기는 올린 사람만 |
| 강사 피드백 | `POST …/agenda-items/{id}/instructor-feedback` | ROLE-04 — 피드백 요청이 켜진 결정 기록에만 |
| 스탠드업 | `GET` · `PUT …/standups/{date}/mine` | 쓰기: 본인(ROLE-01 · 02) |
| 도움 요청 | `POST …/help-requests`, `POST …/help-requests/{id}/comments`, `POST …/help-requests/{id}/close`, `POST …/help-requests/{id}/to-agenda`, `PATCH …/help-requests/{id}`(강사에게 요청 켜기) | 작성 · 닫기 · 전환 · 요청 켜기: 요청자. 코멘트: ROLE-01 · 02, 강사는 요청이 켜진 글에만 |
| 작업 마감 | `PUT` · `DELETE …/tasks/{taskId}/due-date` | ROLE-02와 그 이슈의 담당자 전원(ROLE-01 · 02) [확정 — P14]. 그 밖의 학생 · 강사 `forbidden_role` |
| 학습 타임라인 · 회고 리포트 · 카드 | `GET …/members/{memberId}/timeline`, `…/reports`, `…/decision-cards`, `POST …/decision-cards/{id}/follow-up-request`, `POST …/decision-cards/{id}/follow-up-questions` | 보기: 본인 · 강사. 꼬리질문 요청: 본인. 꼬리질문: ROLE-04 — 요청된 카드에만 |
| 활동 기록 · 동료 피드백 | `…/activities`, `…/peer-feedback` | ROLE-01 · 02 (P12). 강사 보기만 |
| 강사 요청함 | `GET /api/instructor/inbox` | ROLE-04 — 지정된 사무실의 요청만 |
| 컨벤션 라이브러리 | `GET /api/convention-library`, `POST` · `PUT /api/convention-library/{id}` | 보기: 로그인 사용자. 쓰기: ROLE-04 |

- 전체 관리자(ROLE-03) [구현 기본값 — 최소 권한부터]: v1에 전용 화면 · API를 두지 않고, 다른 사무실 데이터나 개인 지표에 접근하는 추가 권한도 주지 않는다. 운영 값(강사 허용 목록 · 심사용 계정 · 시연 사무실 접근 대상)은 서버 설정으로 다룬다. 필요한 권한이 생기면 그 기능을 만들 때 최소 범위로 더한다(02-workflow.md ROLE-03의 "사무실 관리 · 사용자 · 권한 관리"는 그때 구체화).
- 데모의 작업 · 팀원 CRUD, `/api/demo` · `/api/reset`은 만들지 않는다 `[제안]`.

## 5. 응답 DTO 원칙

- [확정] Entity를 그대로 응답하지 않는다. 본인 · 강사 여부로 필드를 빼는 응답은 DTO를 따로 둔다.
- [확정] 평가 응답에는 평가자가 없고, 현재 회차의 추천·비추천 수와 이유만 있다. 이전 회차는 회차 목록으로 따로 읽는다.
- [확정] 사람별 합계 · 순위 필드가 없다.
- [계약 초안] 시각 필드 이름은 `…At`(UTC), 시간 길이는 단위를 붙인다(`reviewWaitHours`). GitHub 번호와 앱 ID를 나눈다(`githubIssueNumber` ↔ `taskId`).
- [계약 초안] 시연 사무실의 응답에는 사무실 종류(`DEMO`)가 들어 있어 화면이 "시연 데이터"를 표시할 수 있다.
- [계약 초안] 이슈 응답에는 담당자 전원(`assigneeIds`)이 들어 있다. 대표 담당자는 화면(SceneModel)이 「3」 규칙으로 계산한다.

## 6. 에러 응답과 Security 규칙

### 에러 [확정 — 코드 컨벤션 (BE) 「에러 응답」]

`{ "error": { "code": "...", "message": "..." } }` — code는 소문자 snake. `invalid`(400, 요청 출처 검사 실패는 403) · `not_found`(404) · `conflict`(409) · `unavailable`(503) · `unauthorized`(401) · `forbidden_role`(403) · `github_unauthorized`(503) · `github_rate_limited`(503). 새 code를 만들지 않는다(2026-10-30 결정).

- `[제안]` 승인되지 않은 계정을 강사로 지정하려는 요청은 `invalid`, 레포 관리자가 자기 자신을 강사로 지정하는 요청은 `conflict`.
- [구현 기본값] 접근할 수 없는 사무실의 요청은 사무실이 있는지 드러내지 않도록 `not_found`(404)다. 사무실 안에서 역할이 맞지 않는 동작은 `forbidden_role`이다.

### 역할 판정 [확정 — 05 P1]

| 역할 | 판정 | 언제 확인 |
| --- | --- | --- |
| ROLE-02 레포 관리자 | 그 레포 admin | 로그인할 때 레포마다 확인해 세션에 둔다 |
| ROLE-01 참여자 | 그 밖의 레포 콜라보레이터(read 포함) | 위와 같음 |
| ROLE-04 강사 | GitHub 로그인 → 서버 설정 허용 목록(GitHub 사용자 ID)에 있음 → 레포 관리자가 그 사무실에 지정함. 콜라보레이터가 아니어도 된다 | 허용 목록은 서버 설정, 지정은 **요청마다** 앱 DB 확인 |
| ROLE-03 전체 관리자 | `MASTER_ADMIN_GITHUB_LOGIN` | 서버 설정 |
| 심사용 계정 | 시연 사무실 접근 대상에 등록된 역할(ROLE-01 · 02 · 04 중 하나) | 로그인할 때 확인해 세션에 둔다. 강사 역할은 지정과 같이 요청마다 확인한다 |

- [확정] 일반 학생은 강사로 지정할 수 없다.
- [확정] 한 사무실에서 한 사람의 역할은 하나다. 강사로 지정된 사무실에서는 강사 정책만 적용하고 ROLE-01 · 02 쓰기 권한을 합치지 않는다(권한 상승 방지). 레포 관리자가 자기 자신을 지정하는 요청은 거부한다.
- [확정] 허용 목록의 강사 계정은 어느 사무실에서도 ROLE-01 · 02로 판정하지 않는다. 지정되지 않았거나 지정이 해제된 사무실에는 콜라보레이터여도 접근할 수 없고(`not_found`), 팀원(`Member`)에 들지 않는다. 해제해도 과거 기록은 남는다(2026-11-01 추가 결정 — 사용자 확인).
- [확정] 심사용 계정은 시연 사무실에만 접근하고 GitHub 권한이 없다. 테스트 계정이라고 검사를 건너뛰지 않는다(P15).

### 검사 순서 `[제안]`

1. 로그인(세션) — 없으면 `unauthorized`
2. 사무실 접근 — 그 사무실의 현재 GitHub 자격(세션의 레포 역할 — 허용 목록 강사 계정은 해당 없음), 강사 지정(DB), 시연 사무실 접근 대상(심사용 계정 · 등록된 우리 조 계정) 중 하나. 없으면 `not_found`
3. 동작 권한 — 위 「4」 표의 역할. 강사의 허용되지 않은 쓰기는 `forbidden_role`
4. 대상 조건 — 작성자 제외, 요청이 켜진 건인지, 회의 전인지 등

검사는 인가 서비스 한 곳에서 하고 각 Controller가 부른다. 화면에서 버튼을 숨긴 것은 검사가 아니다(FR-AUTH-04).

### 세션 · 쿠키 · CSRF · SSE `[제안]` (외부 공개 배포 — 2026-11-01 결정)

- 세션 30분~1시간 [확정]. 단일 서버의 기본 세션(서버 메모리)으로 시작한다 [구현 기본값]. 쿠키 `HttpOnly` · `Secure` · `SameSite` [제안 — 값은 구현 때].
- 로그인 방식이 둘이어도(GitHub OAuth · 심사용 ID/PW) 세션 · CSRF · 인가는 하나의 Spring Security 설정을 쓴다. 로그인할 때 세션 ID를 새로 발급한다. 심사용 로그인의 반복 실패 제한은 구현 때 검토한다 `[제안]`.
- CSRF: Spring Security 기본 방어를 켜고, SPA는 쿠키로 받은 토큰을 헤더로 보낸다.
- SSE: 연결할 때 「검사 순서」 1 · 2를 하고, 이벤트에는 변경 종류 · 대상 ID만 담는다. 화면은 REST로 다시 읽는다. 세션이 끝나면 연결을 닫는다.

## 7. GitHub 어댑터 인터페이스 [계약 초안]

GitHub API를 부르는 유일한 곳이다(코드 컨벤션 (BE) 「꼭 지킬 것」 1). **읽기 메서드만 둔다** — 원본을 바꾸는 메서드를 인터페이스에 만들지 않는다.

| 메서드 (이름 초안) | 하는 일 | 메모 |
| --- | --- | --- |
| 사용자 · 레포 권한 조회 | 로그인한 사용자의 레포별 권한(admin · 콜라보레이터) | 어느 자격 증명으로 조회하나는 B1 구현 때 정한다 |
| 설치 목록 · 레포 목록 | 앱이 설치된 조직과 레포 | FR-GH-01 |
| 이슈 변경분 | 기준 시각 이후 바뀐 이슈(담당자 전원 · 닫힘 사유 포함) | NFR-10 · FR-GH-02 |
| PR 변경분 | 기준 시각 이후 바뀐 PR(`headSha` · Draft · 머지 포함) | FR-GH-02 |
| 리뷰 · 리뷰 요청 | PR별 | FR-FLOW 계산 재료 |
| 커밋 시각 | PR · 이슈별 마지막 커밋 시각 | 막힘 "커밋 없음" |
| 콜라보레이터 | 레포 콜라보레이터 목록 | FR-GH-04 |
| PR–이슈 연결 | GitHub가 공식으로 인식하는 연결 관계 | [확정] 본문 · 제목의 `#N` 언급은 읽지 않는다. 조회 API는 구현 때 — ARCHITECTURE.md 「GitHub 원본 상태와 앱 상태」 |
| 명세 문서 읽기 | 경로의 파일 내용 | FR-REQ-01 · 02 |

- 구현은 두 개다. ① GitHub 어댑터(실제 사무실) ② Seed 어댑터(시연 사무실, 같은 결과 모양). 시연 사무실 · 심사용 계정은 GitHub를 부르지 않는다.
- 이슈 · PR 만들기 · 닫기 · 병합, 라벨 · 담당자 변경 메서드는 만들지 않는다 — 화면이 GitHub 웹페이지로 이동시킨다(P3).
- 실패는 `github_unauthorized` · `github_rate_limited` · `unavailable`로 바꾸고, `SyncState`에 마지막 성공 시각과 사유를 남긴다(FR-GH-05).
- 어댑터 밖으로 GitHub 응답 모양을 내보내지 않는다.

## 8. 프론트 계약 [계약 초안]

### DataSource (`src/data/DataSource.ts`)

이름과 교체 구조는 유지한다(CON-05). 구현은 `mock`(화면 개발 · 테스트)과 `api`(Spring) 두 개.

```ts
interface DataSource {
  load(workspaceId: string): Promise<WorkspaceSnapshot>;      // 사무실 하나의 전체 데이터 (화면 묶음 타입)
  subscribe(workspaceId: string, onChange: (c: Change) => void): () => void;
  // 앱 자체 기록만 쓴다 — GitHub 원본(이슈 · PR · 담당 · 상태)을 바꾸는 메서드는 없다
  linkRequirement(requirementId: string, taskId: string): Promise<void>;
  createAgendaItem(input: AgendaItemInput): Promise<AgendaItem>;
  evaluate(submissionId: string, verdict: 'recommend' | 'not_recommend', reason: string): Promise<void>;
  // … 스탠드업 · 도움 요청 · 마감 · 강사 답변 등은 「4」 API 묶음과 1:1로 더한다
}
```

- `WorkspaceSnapshot`은 데모의 `Workspace` 타입을 이어받은 화면용 묶음이다. 서버 Entity `Workspace`(사무실)와 구분하려고 화면 타입은 `WorkspaceSnapshot`으로 부른다 [구현 기본값 — 2026-11-01 추가 결정]. 프론트 화면 모델은 서버 Entity와 다른 타입으로 둔다.
- 에러는 `DataError(message, code)` — code는 「6」의 값.

### SceneModel · ThemeLayout

```ts
type PlaceRole =
  | 'backlog' | `desk:${string}` | 'review' | 'done'
  | 'meeting' | 'board' | `requirement:${string}`;

interface SceneEntity {
  id: string;
  kind: 'issue' | 'pr' | 'help' | 'learning' | 'other' | 'avatar' | 'requirement' | 'requirementGroup' | 'board';
  placeRole: PlaceRole;
  badges: string[];          // 막힘 · 추천·비추천 수 · 묶음 개수 · 시연 데이터 등
  highlight?: 'blocked' | 'recent' | 'dim' | 'focus';
  groupKey?: string;         // 요구사항 묶음 — 요구사항 종류(ID 접두사) [확정], 없으면 "미분류"
}

interface ThemeLayout {
  anchor(role: PlaceRole, index: number): [x: number, y: number, z: number];
  dropZoneAt(point: [number, number, number]): PlaceRole | null;
  route?(from: PlaceRole, to: PlaceRole): [number, number, number][]; // 아바타 이동
}
```

- SceneModel은 도메인 데이터 → `SceneEntity[]`의 순수 함수다. 테마를 모른다.
- ThemeLayout 구현은 기본 테마(사무실) 하나다. 테마 엔진은 만들지 않는다 [확정].
- 드롭 결과: `requirement:*` → `linkRequirement`(상자는 제자리), `meeting` → `createAgendaItem`(원본은 제자리), 그 밖은 아무 일도 없음 [확정 — 위치 규칙].

## 9. 계약 변경 절차 [계약 초안]

| 계약 묶음 | 파일 (개발 저장소 기준 — 신규 예정) | 소유 (BACKEND · SCREEN_ASSIGNMENTS) |
| --- | --- | --- |
| 도메인 모델 · Entity · DB 스키마 | 백엔드 각 패키지 `domain/` · migration 파일 | B · C 공동 |
| 요청 · 응답 DTO · API 경로 | 백엔드 `dto/` · Controller, 이 문서 「4」 | B · C 공동, D 확인 |
| 공통 에러 · 보안 설정 · 인가 서비스 | 백엔드 `common/` · `auth/` | B |
| GitHub 어댑터 인터페이스 | 백엔드 `github/` | B |
| DataSource · 화면 타입 | `frontend/src/data/DataSource.ts` · `types.ts` | D, B · C 확인 |
| SceneModel · ThemeLayout | `frontend/src/scene/model/` · `theme/` | D |

1. 계약을 바꾸는 변경은 **기능 PR과 따로** "계약 PR"로 낸다. 이 문서를 같은 PR에서 고친다.
2. 위 표의 소유 역할이 모두 리뷰한다. 다른 세션은 계약 PR이 머지된 `main`에서 다시 시작한다.
3. 계약 PR이 머지되기 전에는 다른 기능 PR에서 계약 파일을 고치지 않는다.

## 10. 구현 기본값과 남은 결정

2026-11-01 추가 결정으로 이전 「미승인 세부 정책 모음」의 대부분을 닫았다 — 팀 재정의 4건 채택, PR–이슈 연결(GitHub 공식 연결만), 강사 계정 관리(서버 설정 허용 목록 · GitHub 사용자 ID), 요구사항 묶음 키(요구사항 종류), 작업 마감 권한(ROLE-02 + 담당자 전원), 시연 사무실 접근 대상(명시 등록), DB(MySQL 8.x) · JDK(21) · 빌드 도구(Gradle). 근거는 결정 기록 「추가 결정 (2026-11-01, v3 검토 뒤)」.

### [구현 기본값] 목록

| 항목 | 기본값 | 기댄 곳 |
| --- | --- | --- |
| Draft PR | 검토 공간(`review`) + Draft 배지 | 「3」 |
| 머지되지 않고 닫힌 PR | 완료 공간(`done`) + 미병합 종료 표시 | 「3」 |
| 닫힌 이슈 | 완료 공간, GitHub 닫힘 사유로 완료 · 종료 구분 가능 | 「3」 |
| 대표 담당자 규칙 | 그 사무실 팀원인 담당자 중 GitHub 사용자 ID가 가장 작은 사람, 없으면 `backlog` | 「3」 · SceneModel |
| 팀원이 아닌 GitHub 담당자 | `TaskAssignee`에 넣지 않는다 | 「2」 |
| 권한 없는 사무실 | `not_found`(404) — 존재를 숨김. 사무실 안 역할 위반은 `forbidden_role` | 「6」 |
| 세션 저장 | 단일 서버의 기본 세션 | 「6」 |
| 계약 테스트 | Spring 통합 테스트 + 프론트 핵심 E2E | ARCHITECTURE.md 「테스트 경계」 |
| 화면 묶음 타입 | `WorkspaceSnapshot` — 서버 Entity와 구분 | 「8」 |
| 아바타 색 | 팀원 구분 용도로 자유롭게(사람 비교 표시가 아님 — P6은 도형 색 규칙) | ARCHITECTURE.md 「색」 |
| 아바타 이동 | 회의 시작 · 종료 등 앱 내부 이벤트 우선 | 05 「상태값」 |
| 스탠드업 날짜 | 한국 시간 기준 날짜 | 「1」 · `Standup` |
| 스탠드업 "막힌 것" → 도움 요청 | 내용이 채워진 작성 화면이 열리고 요청자가 올려야 생성 | FR-MTG-02 |
| 강사 수동 동기화 | 허용하지 않음 | 「4」 |
| 전체 관리자(ROLE-03) | 전용 화면 · API 없음, 추가 데이터 접근 없음 — 최소 권한부터 | 「4」 |
| 우리 조 계정의 시연 사무실 역할 | 우리 조 실제 사무실의 역할을 따름 | 「2」 시연 사무실 접근 대상 |
| 요구사항 묶음 전환 개수 · 시각 효과 | ARCHITECTURE.md 「요구사항 묶음 표시」의 상수 예시로 시작 | SceneModel |

### 남은 결정 (개발 착수를 막지 않음 — 정할 시점이 정해짐)

| 항목 | 언제 · 누가 |
| --- | --- |
| ID 형식(숫자 · UUID · 접두사 문자열) | 이 문서 확정(1차 단계) 때 |
| Spring Boot 버전 · DB migration 도구 · 구체 DB 설정 · 세션 저장소 세부 | Spring Boot 골격 만들 때(구현 단계) |
| 배포 플랫폼 · SPA 서빙 방식 | Spring Boot 초기 프로젝트 구축 단계 — 외부 접속 · HTTPS · 세션 · 서버 측 접근 제어 요구는 그대로 |
| 개발 저장소 구성(한 저장소 / 두 저장소) | 개발 저장소 만들기 전(1차 단계) |
| 레포 권한을 조회할 자격 증명 | B1 구현 때 |
| 백엔드 담당(B12 · B13 · B14 미정 허용) | 이 문서 확정 뒤 GitHub 이슈를 만들며 배분 — **병렬 기능 개발 전에 담당 · 작업 경계 지정** |
