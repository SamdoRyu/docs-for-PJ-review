# 코드 컨벤션 (BE)

> 2026-10-28 결정 회의 · 2026-10-30 강사 역할 결정 · 2026-11-01 기술 스택 결정 · 2026-11-01 추가 결정 · 2026-11-03 공통 계약 확정 결정 반영 · 2026-11-07 GRAVIT 구현 확인 반영(Spring Boot 4.1.2 · Flyway · 실제 클래스 이름). 꼭 지킬 것만 남기고 나머지는 권장으로 낮춘 판. GRAVIT 쪽 규칙 원본은 [`.claude/rules/code-quality.md`](https://github.com/SamdoRyu/GRAVIT/blob/main/.claude/rules/code-quality.md)

## 전제

> **백엔드 스택은 정했습니다(2026-11-01 결정)**: Spring Boot + Spring MVC, Spring Data JPA, Spring Security + 세션 기반 인증. 단일 Spring Boot 애플리케이션의 **모듈형 모놀리스**로 만듭니다. 마이크로서비스 · CQRS · 이벤트 소싱은 전제하지 않습니다. **Java 21 · Gradle · MySQL 8.x로 확정했습니다**(2026-11-01 추가 결정). Spring Boot 버전 · migration 도구 · 구체 DB 설정은 Spring Boot 골격을 만들 때 정합니다(ARCHITECTURE.md 「기술 스택」) → GRAVIT 골격에서 Spring Boot 4.1.2 · Flyway · MySQL 8.4(`mysql:8.4.13`)로 정했습니다.

기존 데모의 Hono 서버 · SQLite 코드는 **동작과 API 설계만 참고**하고 포팅하지 않습니다. 프론트엔드(React · TypeScript)는 데모 코드를 재사용하므로 프론트 쪽은 데모의 언어와 패턴을 먼저 따릅니다. 팀이 배운 범위(Spring MVC 요청 처리 · Controller · 요청/응답 데이터 · 파일 업로드 · 예외 처리, ORM · JPA · Entity 연관관계 · Repository · 페이징 · 정렬, Spring Security · 세션)를 기능에 자연스럽게 씁니다. 그 밖의 Spring 개념(AOP 등)은 필요할 때만 씁니다.

새 규칙과 기존 코드가 다르면 기존 코드는 그대로 두고 새 코드에만 적용합니다.

## 꼭 지킬 것

보안과 제품 정책에서 나온 것이라 리뷰에서 바로 반대합니다.

1. **GitHub API는 GitHub 어댑터 패키지(`github`) 안에서만 부릅니다.** 읽기만 합니다 — 이슈 만들기 · 코멘트 · 라벨 · 머지 · 상태 변경처럼 GitHub 원본을 바꾸는 호출(GraphQL mutation 포함)은 만들지 않습니다(CON-02). 사용자의 그런 동작은 화면이 GitHub 웹페이지로 이동시킵니다(P3). OAuth 코드 교환과 GitHub App 설치 토큰 발급은 인증 절차라 `POST`여도 괜찮고, 앱 서버 자체 API의 쓰기도 별개입니다. PR–이슈 연결은 GitHub가 공식으로 인식한 연결만 읽고, PR 본문의 `#N`을 직접 파싱해 만들지 않습니다(FR-GH-04).
2. **GitHub 토큰은 서버에만 둡니다.** 브라우저로 보내지 않고, 로그에 토큰 · 쿠키 · 이메일을 남기지 않습니다(NFR-04).
3. **권한은 API에서 확인합니다.** 버튼을 숨긴 것은 권한 확인이 아닙니다(FR-AUTH-04). 요청마다 로그인 · 사무실 접근 · 역할을 확인하고, 클라이언트가 보낸 사용자 값(`X-Actor` 같은 헤더 포함)을 믿지 않습니다. 실시간 갱신(SSE)도 같은 검사를 거칩니다(2026-11-01 결정).
4. **개인 지표(학습 타임라인 · 회고 리포트 · 기술 결정 카드)는 본인과 강사 요청의 응답에만 담습니다**(NFR-06 — 2026-10-30 결정). 다른 팀원의 요청에는 담지 않습니다. **사람별 합계 · 순위 필드는 만들지 않습니다**(FR-FLOW-06, FR-LRN-09).
5. **GitHub 동기화 실패를 삼키지 않습니다.** 마지막 성공 시각과 실패 사유를 남기고 화면은 마지막 데이터로 둡니다(FR-GH-05, NFR-09).
6. **쿼리는 매개변수 바인딩으로만** 씁니다. JPQL · 네이티브 쿼리에 사용자 입력을 문자열로 이어 붙이지 않습니다.
7. **시각은 UTC로** 저장 · 응답합니다(ISO 8601 문자열). 한국 시간 변환은 화면에서 합니다.
8. **사무실 단위 데이터는 항상 사무실(Workspace) 조건으로** 조회 · 저장합니다. 여러 조가 같은 DB를 씁니다(2026-11-01 결정).

## 에러 응답

데모 모양을 그대로 씁니다. code는 소문자 snake입니다. 스택이 바뀌어도 유지합니다(프론트 `toError` · `DataError`가 이 모양에 기댑니다).

```json
{ "error": { "code": "forbidden_role", "message": "이 작업을 할 권한이 없어요" } }
```

| code | HTTP | 언제 |
| --- | --- | --- |
| `invalid` | 400 (요청 출처 검사 실패는 403) | 입력 검증 실패. 이유 없는 추천 · 비추천 평가 등 — 기존 code |
| `not_found` | 404 | 대상 없음 — 기존 code |
| `conflict` | 409 | 상태 충돌 — 기존 code |
| `unavailable` | 503 | 서버 사용 불가 · 알 수 없는 오류 — 기존 code |
| `unauthorized` | 401 | 로그인 안 됨 · 세션 만료 — 신규 |
| `forbidden_role` | 403 | 역할이 맞지 않음. 강사의 허용되지 않은 쓰기(요청되지 않은 건 · 라이브러리 밖의 쓰기)도 이 code로 거부합니다(2026-10-30 결정) — 신규 |
| `github_unauthorized` | 503 | GitHub 인증 실패(설치 토큰 등) — 신규 |
| `github_rate_limited` | 503 | GitHub 호출 한도 초과 — 신규 |

- 실패는 공통 예외(앱 예외 클래스 하나 + code 열거형 — GRAVIT `ApiException` + `ErrorCode`)를 던지고, **전역 예외 처리 한 곳**(`@RestControllerAdvice` — GRAVIT `common/error/GlobalExceptionHandler.java`)이 위 모양으로 바꿉니다. Spring Security의 인증 · 인가 실패(401 · 403)도 같은 모양으로 응답합니다.
- 새 code를 더하면 프론트 `src/data/api/v1/index.ts`의 `toError`와 `DataError` 타입도 같이 고칩니다. 새 code는 팀 합의 없이 만들지 않습니다(2026-10-30 결정).
- `message`는 사용자에게 보일 한국어 문장입니다.

## 서버 코드 흐름

**Controller → Service → Repository**입니다.

- **Controller**: 요청 매핑, 요청 DTO 검증(Bean Validation — `spring-boot-starter-validation`), 인가 서비스로 권한 검사, Service 호출, 응답 DTO 반환. 규칙을 두지 않습니다.
- **Service**: 업무 규칙과 트랜잭션(`@Transactional`). 외부 호출 없이 값만으로 판정하는 규칙(막힘 · 평가 처리 · 카드 조립 등)은 따로 떼어 단위 테스트합니다.
- **Repository**: Spring Data JPA 인터페이스. 다른 패키지의 Repository를 직접 부르지 않고 그 패키지의 Service를 부릅니다(`[제안]`).
- **DTO**: Entity를 응답으로 바로 내보내지 않습니다. 본인 · 강사 여부로 필드를 빼야 하는 응답은 DTO를 따로 둡니다.
- 쓰기가 끝난 뒤 브라우저 알림(SSE)은 **트랜잭션 커밋 뒤에** 보냅니다(데모 원칙 유지 — GRAVIT `.claude/rules/code-quality.md` 「구조」에서 필수).
- 파일 업로드(Spring MVC 멀티파트)는 v1 요구사항에 쓰는 곳이 정해지지 않았습니다 — 필요한 기능이 생기면 그때 정합니다.

## 인증

- Spring Security로 인증 · 인가를 하고, **세션 기반**으로 로그인 상태를 둡니다(2026-11-01 결정). GitHub 로그인은 GitHub App의 사용자 인증으로 합니다(OAuth2 Login — GRAVIT `oauth2Login`, #64).
- **세션은 30분\~1시간 뒤 만료**됩니다(2026-10-28 결정). GRAVIT은 마지막 요청 뒤 60분(`application.yaml`).
- 역할(참여자 · 레포 관리자)은 **로그인할 때 GitHub 권한으로 레포마다 정하고** 세션에 둡니다. 요청마다 GitHub에 다시 묻지 않습니다. 팀 레포 admin은 레포 관리자, 그 밖의 레포 콜라보레이터 전원(read 포함)은 참여자입니다. 전체 관리자는 `MASTER_ADMIN_GITHUB_LOGIN`으로 지정합니다.
- 강사는 레포 관리자가 앱 팀 설정에서 **승인된 강사 계정 중에서** 지정합니다. 일반 학생은 지정할 수 없습니다(2026-11-01 결정). **강사 지정 여부는 요청마다 앱 DB에서 확인**하므로 지정 · 해제가 바로 반영됩니다(2026-10-30 결정).
- **강사 판정**(2026-11-01 추가 결정): GitHub 로그인 → 서버 설정 허용 목록(**GitHub 사용자 ID** — 로그인 이름이 아님) → 그 사무실의 지정 → ROLE-04. 허용 목록 계정은 콜라보레이터여도 ROLE-01 · 02로 판정하지 않고 팀원(`Member`)에도 넣지 않습니다. 지정되지 않은 사무실은 `not_found`입니다. 같은 사무실에서 강사와 참여자 · 레포 관리자 권한을 합치지 않습니다.
- **보안 심사용 ID/PW 로그인**(2026-11-01 추가 결정 · P15): 앱 내부 계정이고 시연 사무실(Demo Workspace)에만 접근합니다. Spring Security 안에서(폼 로그인 — GRAVIT `formLogin`, #63) 처리하고, 계정은 서버 설정 `REVIEW_ACCOUNTS`로 넣고, 비밀번호는 `PasswordEncoder` 해시로만 저장합니다. 비밀번호 · 해시를 저장소에 커밋하지 않습니다. 명시적으로 켠 배포 환경에서만 동작하고(기본은 꺼짐), 로그인 뒤에는 GitHub 로그인과 같은 세션 · CSRF · 인가 검사를 받습니다. 별도 인증 서버 · JWT는 만들지 않습니다.
- 외부 공개 배포라 **HTTPS, 세션 쿠키(`HttpOnly` · `Secure` · `SameSite`), CSRF 방어, 허용 출처 검사**를 갖춥니다(2026-11-01 결정). 세부 설정은 ARCHITECTURE.md 「인증과 권한」 · CONTRACTS.md 「6」.

## 저장 (Spring Data JPA)

- 06-data.md 「저장 항목」을 **JPA Entity**로 만듭니다. 데모의 `(id, data JSON)` 테이블 방식은 쓰지 않습니다(2026-11-01 결정 — 2026-10-28 결정을 대신함).
- 연관관계는 기본 **단방향 N–1**로 두고, 양방향은 꼭 필요한 곳만 씁니다. 기본은 지연 로딩이고, 목록 조회에서 N+1이 생기지 않게 조회 방식을 정합니다(fetch join · EntityGraph 등).
- 목록 API의 **페이지는 계약이 정한 목록(학습 타임라인 · 활동 기록)만** `?page=0&size=20`으로 받고 `PageResponse`로 응답합니다(Spring `Page`를 그대로 직렬화하지 않음). 그 밖의 목록은 배열 전체를 줍니다. 정렬은 계약에 적힌 고정 순서를 따릅니다(GRAVIT [CONTRACTS.md](https://github.com/SamdoRyu/GRAVIT/blob/main/docs/CONTRACTS.md) 「4」 · [CONTRACTS_API.md](https://github.com/SamdoRyu/GRAVIT/blob/main/docs/CONTRACTS_API.md)).
- 스키마는 migration 도구로 관리합니다 — Flyway(`backend/src/main/resources/db/migration/V{n}__{설명}.sql`, GRAVIT 골격에서 정함). 이미 적용된 파일은 고치지 않고 새 버전 파일을 더합니다. `ddl-auto`로 테이블을 만들지 않습니다(GRAVIT 규칙에서 필수).
- GitHub에서 온 값은 `syncedAt`을 같이 저장합니다.
- DB는 MySQL 8.x입니다(2026-11-01 추가 결정). 테스트 DB는 MySQL 8.4입니다 — 로컬은 `compose.yaml`, CI는 서비스 컨테이너(GRAVIT 골격에서 정함, 내장 DB는 쓰지 않음). 계약 테스트는 Spring 통합 테스트로 합니다(구현 기본값 — 프론트는 핵심 E2E).

## 이름 (권장)

- **새 타입은 아래 이름을 씁니다.** 데모에 이미 있는 이름(`Task`, `Member`, `Meeting`, `DataSource`, `Activity`)은 바꾸지 않습니다. 서버 Entity `Workspace`(사무실)와 데모 화면 타입 `Workspace`(전체 데이터 묶음)가 겹치는 문제는 CONTRACTS.md 「8」에서 정합니다 → 화면 타입은 `WorkspaceSnapshot`으로 정했습니다(GRAVIT `frontend/src/data/types.ts`).

  | 정의서 용어 | 코드 이름 |
  | --- | --- |
  | 명세 문서 · 요구사항 | `SpecDocument` · `Requirement` (GRAVIT에 `SpecDocument` 클래스는 없다 — 명세 경로는 `TeamSettings.specDocumentPath`) |
  | 제출(PR) | `Submission` (GitHub 원본 타입은 `GithubPullRequest`, 이슈는 `GithubIssue`) |
  | 요구사항–이슈 연결 | `RequirementLink` |
  | 막힘 · PR 평가(추천 · 비추천) · 평가 회차 · 회의 안건 · 스탠드업 | `Blocker`(GRAVIT은 Entity 없이 계산 — `flow/domain/BlockedRule.java`) · `Evaluation`(`recommend`, `not_recommend`) · `EvaluationRound` (2026-11-01 — CONTRACTS.md 2026-11-03 확정) · `AgendaItem` · `Standup` |
  | 도움 요청 | `HelpRequest` |
  | 컨벤션 라이브러리 항목 | `ConventionLibraryItem` |
  | 회고 리포트 · 기술 결정 카드 | `RetroReport` · `DecisionCard` |
  | 팀 설정 · 사무실 | `TeamSettings` · `Workspace` |

- 자바 쪽 표기는 Java 관례를 따릅니다: 클래스 `PascalCase`, 메서드 · 필드 `camelCase`, 상수 `UPPER_SNAKE`, 패키지 소문자. 프론트 쪽은 데모 관례(컴포넌트 `PascalCase.tsx`, 그 밖의 모듈 `camelCase.ts`)를 따릅니다.
- JSON 응답의 필드 이름은 `camelCase`, 문자열 열거 값은 프론트와 맞춰 소문자 snake(`'not_recommend'`)로 냅니다. 자바 열거형과의 변환은 공통 코드 한 곳에서 합니다(값 목록은 CONTRACTS.md 「3」).
- **ID**(2026-11-03 결정): Entity 기본키는 `Long`(MySQL `BIGINT`)이고, API에서는 내부 ID를 문자열로 주고받습니다(`"taskId": "42"`). 이슈 · PR 번호와 GitHub 사용자 · 레포 ID는 내부 ID와 다른 필드에 둡니다. 규칙은 CONTRACTS.md 「2」 ID 정책.
- 헷갈리기 쉬운 값만 이름으로 구분합니다.
  - 시간 값에는 단위: `reviewWaitHours`, `sessionTtlMinutes`
  - GitHub 번호와 앱 ID: `githubIssueNumber` ↔ `taskId`
  - 참 · 거짓은 `is` · `has` · `can`으로 시작하면 읽기 좋습니다.
- 함수 동사는 따로 정하지 않습니다. GitHub 코드는 `fetch` · `sync` 정도면 충분합니다.

## 기준값

- **팀 설정 화면에서 바꾸는 값**(프로젝트 기간, 막힘 기준 시간 — 기본 리뷰 대기 48시간 · 커밋 없음 48시간)은 `TeamSettings`에서 읽습니다.
- 그 밖의 숫자(폴링 5분, 도움 요청이 안건 후보가 되는 48시간, 우선순위 라벨 이름, 요구사항 판 수(5~10장 — 2026-11-06 결정, 프론트 SceneModel 상수) 등)는 **상수 한 곳에 모아 두면 됩니다**(백엔드 한 곳, 화면 계산용 프론트 한 곳). 코드 여기저기에 숫자를 직접 쓰지만 않으면 됩니다.

## 모듈 (권장)

| 모듈 | 위치 (GRAVIT — ARCHITECTURE.md 「개발 저장소」) |
| --- | --- |
| 순수 규칙 (core) | 각 패키지 Service 안의 규칙 클래스 — GitHub · DB · env를 부르지 않아 단위 테스트가 쉬움 |
| API | 각 패키지의 Controller |
| 저장 | 각 패키지의 Entity · Repository |
| GitHub 어댑터 | `github` 패키지 (실제 + 시연용 Seed) |
| 인증 · 인가 | `auth` · `common` 패키지 |
| 가짜 데이터 | 화면 개발 · 테스트: 프론트 `src/data/mock/`. 발표 시연: 서버 Seed 사무실(2026-11-01 결정) |

- 화면은 `src/data/DataSource.ts` 인터페이스로만 데이터를 받습니다. 화면 코드에서 `fetch('/api/…')`를 직접 부르지 않습니다.
- 공통 계약(Entity · DTO · API 경로 · 에러 · 보안 설정 · 어댑터)은 CONTRACTS.md 「9. 계약 변경 절차」대로 바꿉니다.

## 도구

| 항목 | 내용 |
| --- | --- |
| 빌드 도구 · JDK | Gradle · Java 21 (2026-11-01 추가 결정) |
| DB | MySQL 8.x (2026-11-01 추가 결정) — GRAVIT 8.4. migration은 Flyway(GRAVIT 골격에서 정함) |
| 테스트 | JUnit + Spring Boot 테스트. GitHub는 응답 예시 데이터로 대신하고 실제로 부르지 않습니다. 명령은 GRAVIT README 「빌드 · 테스트 명령」(PROJECT_COMMANDS.md) |
| 린터 · 포매터 | 결정 필요 — 새 도구는 팀 합의 후 (프론트는 데모의 `oxlint` 그대로). 2026-11-07 현재 백엔드 린터 · 포매터는 없다(GRAVIT `build.gradle`) |
