# Project Commands

이 저장소에서 실제로 쓰는 명령 전부다. `.claude/rules/testing.md` 가 이 문서를 가리킨다.

**기존 3D 팀 오피스 서버를 그대로 쓰므로(CON-05) 명령은 그 서버의 스택이 정해진 뒤 채운다.** 지금 확정된 것은 환경 변수와 검증 원칙뿐이다.

## 처음 받았을 때

```bash
# TODO(스택 확인 후): 레포 주소
git clone <레포 주소>
cd <레포 폴더>
# TODO(스택 확인 후): 의존성 설치
# TODO(스택 확인 후): 개발 서버 실행
```

→ `APP_ORIGIN`(로컬 기본값 `http://localhost:3000`, 포트는 스택 확인 후)

**값 파일이 없으면 서버가 뜨지 않는다.** 서버는 시작할 때 필수 값을 검사하고, 없으면 바로 멈춘다 — 아래 「2. 환경 변수」를 먼저 본다.

**포트가 같아야 한다.** 개발용 GitHub App을 모두가 함께 쓰려면 로컬 포트가 같아야 한다. 포트가 다르면 콜백 주소가 어긋나 로그인이 실패한다.

화면 작업은 `DATA_SOURCE=fake` 로 GitHub 없이 한다. 연동 작업과 시범 운영만 실제 GitHub App을 쓴다.

## 명령

| 명령 | 하는 일 | 무엇을 보장하나 |
| --- | --- | --- |
| TODO(스택 확인 후) | 의존성 설치 | — |
| TODO(스택 확인 후) | 개발 서버 | 눈으로 보는 확인 |
| TODO(스택 확인 후) | 빌드 | TODO |
| TODO(스택 확인 후) | lint | TODO — 포매터 · 린터 설정 파일은 레포에 커밋해 모두 같은 규칙으로 돌린다(코드 컨벤션 BE) |
| TODO(스택 확인 후) | test | core 의 순수 규칙 단위 테스트 |
| TODO(스택 확인 후) | DB 스키마 적용 | TODO |

## DB

`DATABASE_URL` 하나로 앱 서버 저장소에 붙는다. 형식은 스택 확인 후 정한다(환경 변수 가이드). 저장 항목은 [06-data.md](./06-data.md).

### 1. 로컬 DB

TODO(스택 확인 후): 로컬 DB 를 띄우는 방법.

### 2. 환경 변수

`.env.example` 을 복사해 값 파일을 만든다. 파일 이름(`.env` 또는 `.env.local`)은 스택 확인 후 정한다. **값은 팀이 정한 비밀 공유 수단으로만 받는다.**

- 실제 값 파일(`.env`, `.env.local`)과 GitHub App 키 파일(`*.pem`)은 `.gitignore` 로 무시된다. `.env.example` 만 커밋된다(이름만, 비밀 값 없음).
- **값을 터미널에 찍거나 Issue · PR · 댓글 · 채팅에 붙이지 않는다.** 확인은 "설정됨 / 미설정" 까지만 한다.
- **빈 값(`KEY=`)은 미설정과 같다.** 복사만 하고 두면 서버는 여전히 시작하지 않는다.
- 브라우저로 나가는 변수에는 비밀 값을 넣지 않는다.
- 막힘 기준 시간처럼 팀이 화면에서 조정하는 값은 환경 변수가 아니라 팩과 레포 설정에 둔다.

| 변수 | 어디에 쓰나 | 필수 | 서버 전용 | 로컬 개발 값 | 관련 |
| --- | --- | --- | --- | --- | --- |
| `APP_ORIGIN` | 로그인 콜백 주소, 요청 출처 검사 | 필수 | 아니오 | `http://localhost:3000` (포트는 스택 확인 후) | FR-AUTH-01, NFR-05 |
| `SESSION_SECRET` | 로그인 세션 서명 | 필수 | 예 | 각자 만든 32자 이상 임의 문자열 | FR-AUTH-01 |
| `GITHUB_APP_ID` | 레포 읽기용 설치 토큰 발급 | 필수 | 예 | 개발용 GitHub App의 ID | FR-GH-01 |
| `GITHUB_APP_CLIENT_ID` | GitHub 로그인 | 필수 | 아니오 | 개발용 GitHub App 값 | FR-AUTH-01 |
| `GITHUB_APP_CLIENT_SECRET` | GitHub 로그인 | 필수 | 예 | 개발용 GitHub App 값 | NFR-04 |
| `GITHUB_APP_PRIVATE_KEY_PATH` | 설치 토큰 발급용 키 파일 경로 | 필수 | 예 | `./secrets/github-app.pem` | NFR-04 |
| `MASTER_ADMIN_GITHUB_LOGIN` | 전체 관리자 계정 지정 | 필수 | 예 | 본인 GitHub 아이디 | FR-AUTH-03 |
| `DATA_SOURCE` | 데이터 어댑터 선택: `github`, `fake`, `local` | 필수 | 예 | `fake` | NFR-08 |
| `PACK` | 용어·기능 묶음 선택 | 선택 | 예 | `bootcamp` | NFR-08 |
| `SYNC_INTERVAL_MINUTES` | GitHub 폴링 간격 | 선택 | 예 | `5` | FR-GH-03, TBD-06 |
| `DATABASE_URL` | 앱 서버 저장소 | 필수 | 예 | 로컬 DB 주소 (형식은 스택 확인 후) | 06-data.md |
| `LOG_LEVEL` | 로그 수준 | 선택 | 예 | `debug` |  |

**환경별 설정**

| 환경 | `DATA_SOURCE` | GitHub App | 값을 가진 사람 |
| --- | --- | --- | --- |
| 로컬 화면 작업 | `fake` | 개발용 (로그인에만 씀) | 각자 |
| 로컬 연동 작업 | `github` | 개발용, 우리 조 레포에 설치 | 각자 |
| 시범 서버 (TBD-09) | `github` | 운영용 따로 생성 | 서버에만 |
| CI | 없음 | 없음 | env 없이 통과해야 함 |

**로컬 처음 설정**

1. `.env.example` 을 복사해 값 파일을 만든다.
2. 연동 백엔드 담당(B)이 개발용 GitHub App을 하나 만들어 둔다. 권한은 Metadata, Contents, Issues, Pull requests를 모두 읽기 전용으로 두고, 콜백 주소는 `APP_ORIGIN` 기준으로 등록한다.
3. B가 App ID, Client ID, Client secret, 키 파일을 팀 비밀 공유 수단으로 나눠 준다. 키 파일은 `./secrets/` 에 두며, 이 폴더는 git과 Claude Code 양쪽에서 막혀 있다.
4. `MASTER_ADMIN_GITHUB_LOGIN` 에 본인 GitHub 아이디를 넣고 서버를 띄운다.
5. 연동 작업을 할 때만 `DATA_SOURCE` 를 `github` 로 바꾼다.

TODO: 루트 `.env.example` 파일이 아직 레포에 없다 — 위 표의 변수 이름과 비밀이 아닌 기본값만 담아 커밋한다(환경 변수 가이드 「.env.example」).

### 3. 스키마 적용

TODO(스택 확인 후): 스키마 적용 명령.

### migration 정책

TODO(스택 확인 후): migration 도구와 정책. 생성된 파일은 사람이 수동 편집하지 않는다(`.claude/rules/code-quality.md`).

### production 을 바꾸기 전 — backup / rollback

TODO: 대응 내용 없음 (삭제 여부 확인) — 시범 서버 위치(TBD-09)가 정해진 뒤 쓴다.

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

## PR 전에 반드시

```bash
# TODO(스택 확인 후): lint · test · build 명령
```

CI(`.github/workflows/ci.yml`)는 **secret 없이 통과**해야 한다. 지금 CI의 lint · test · build step 은 스택 확인 전이라 placeholder 다.

## 테스트가 덮는 범위

테스트 대상은 **core 의 순수 규칙**이다 — 막힘 계산, 투표 판정, 컨벤션 검사, 기술 결정 카드 조립처럼 외부 서비스 없이 값만으로 판정할 수 있는 것(코드 컨벤션 BE 「모듈 구조」). 화면 시연과 테스트는 **fake 어댑터**로 하고 GitHub API 를 부르지 않는다.

TODO(스택 확인 후): 테스트 도구와 실행 명령, 화면 테스트 여부.

PR 에 "테스트 완료"라고만 적지 않는다. 실제로 한 것만 적고, 테스트하지 못한 부분은 PR 에 명시한다(`.claude/rules/testing.md`).

## 확인할 때 자주 쓰는 것

TODO(스택 확인 후)

## 검증에 쓰는 것

TODO: 대응 내용 없음 (삭제 여부 확인) — 시범 운영(NFR-01 · TBD-05)과 발표 준비(NFR-08) 단계의 검증 절차가 정해지면 쓴다.

## 주의

- Claude Code 는 `.env` 계열 파일과 `secrets/`, `*.pem` 을 읽지 못하게 막혀 있다(`.claude/settings.json` 의 `deny`).
- GitHub API 에 쓰는 요청(POST · PATCH · PUT · DELETE)을 보내는 명령이나 스크립트를 만들지 않는다(CON-02).
- TODO(스택 확인 후): 개발 서버 중복 실행 등 스택별 주의.
