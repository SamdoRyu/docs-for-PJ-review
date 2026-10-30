# Git 전략

> 문서 상태: 2026-10-28 결정 회의 · 2026-10-30 강사 역할 결정 반영. `main` 하나에 작업 브랜치만 두는 가벼운 흐름  
> 적용 대상: 프로젝트 전체 팀원  
> 적용 시점: 개발 레포를 만들고 「8. GitHub 설정」을 마친 뒤

## 1. 한눈에 보기

**꼭 지킬 것 (5개)**

1. `main`에 직접 push하지 않고 PR로만 합칩니다. `main`과 남의 브랜치에는 force push하지 않습니다.
2. 작업은 이슈에서 시작하고, PR 본문에 `Closes #N`을 적습니다.
3. **PR 제목**은 `<type>: <설명>` 형식으로 씁니다. squash merge하면 PR 제목이 그대로 `main`의 커밋이 되기 때문입니다.
4. 머지는 **GitHub 리뷰 승인 1명** 이상을 받은 뒤 GitHub PR 화면에서 직접 합니다.
5. 비밀 값(토큰 · 키 · `.env`)과 개인정보를 커밋하지 않습니다.

나머지는 모두 **권장**입니다. 어겨도 PR을 막지 않습니다.

## 2. 브랜치

```text
main ──┬── feat/12-github-login ──(PR · squash)──▶ main
       └── fix/18-login-error ────(PR · squash)──▶ main
```

- 브랜치는 `main` 하나와 작업 브랜치뿐입니다. `develop`, 승격, hotfix 브랜치는 따로 두지 않습니다.
- 작업 브랜치는 최신 `main`에서 만들고, PR로 `main`에 squash merge합니다. 시연 직전 급한 수정도 똑같이 `fix/…` 브랜치에서 PR로 합칩니다.
- 브랜치가 오래 열려 있으면 `main`을 가끔 받아 옵니다(merge든 rebase든 편한 쪽). 내 브랜치는 force push해도 됩니다.
- 머지된 브랜치는 지웁니다(GitHub 자동 삭제를 켜 두면 신경 쓸 일 없음).
- 시연 · 발표에 쓸 버전은 필요하면 그 시점의 `main`에 태그를 붙여 둡니다(예: `demo-1`). 권장입니다.

**브랜치 이름**: `<type>/<이슈번호>-<짧은-설명>`

- type: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`
- 이슈번호는 숫자만 씁니다. `#`은 URL이 깨지기 쉬워서 붙이지 않습니다.
- 설명은 영문 소문자, 숫자, `-`만 씁니다.
- 예: `feat/12-github-login`, `fix/18-login-error`, `docs/21-readme`

브랜치 이름 패턴:

```text
^(feat|fix|refactor|test|docs|chore)/[0-9]+-[a-z0-9]+(-[a-z0-9]+)*$
```

## 3. 커밋과 PR 제목

**브랜치 안의 커밋은 자유롭게 씁니다.** squash merge로 하나로 합쳐지므로 `main`에 남는 것은 PR 제목뿐입니다.

**PR 제목**은 Conventional Commits 형식입니다. `(scope)`는 선택입니다.

```text
feat(sync): 레포 변경분 5분 폴링 추가
fix: 작성자 본인 평가가 저장되던 문제 수정
feat(sync): 레포 변경분 5분 폴링 추가 (#24)    ← squash 후 main에 남는 모습
```

| type | 언제 |
| --- | --- |
| `feat` | 기능 추가 |
| `fix` | 버그 수정 |
| `refactor` | 동작은 그대로, 구조만 바꿈 |
| `docs` · `test` · `style` · `perf` · `build` · `ci` · `chore` | 문서 · 테스트 · 포맷 · 성능 · 빌드 · CI · 그 밖의 잡일 |

- 설명은 50자 이내, 한국어 OK.
- 패턴은 아래와 같습니다. squash 후 `main` 커밋의 첫 줄(PR 제목)에 적용합니다.

```text
^(feat|fix|refactor|perf|test|docs|style|build|ci|chore)(\([a-z0-9-]+\))?: .{1,50}$
```

## 4. 이슈

New issue를 누르면 템플릿 세 개 중 하나를 고릅니다(`.github/ISSUE_TEMPLATE/`).

| 템플릿 | 언제 | 꼭 쓰는 칸 |
| --- | --- | --- |
| Feature `[Feature]` | 새 기능 | 요구사항 ID, 문제 / 목적, 완료 조건 |
| Bug `[Bug]` | 잘못된 동작 | 현상, 재현 절차 |
| Task `[Task]` | 설정 · 문서 · 리팩터링 · 테스트 | 목표 |

- 요구사항 ID는 템플릿의 「요구사항 ID」 칸에 적습니다. Feature는 필수, Bug · Task는 있으면 적습니다.
- 작업을 시작할 때 담당자를 지정합니다. 앱에서는 담당자 지정이 곧 상자가 내 책상으로 오는 것입니다(FR-OFF-02).

## 5. PR

- base는 `main`입니다.
- 본문에 `Closes #N`을 적습니다. 머지되면 이슈가 자동으로 닫힙니다. `Refs #N`은 쓰지 않습니다.
- 본문(`.github/PULL_REQUEST_TEMPLATE.md`):

| 칸 | 쓰는 것 |
| --- | --- |
| 무엇을 · 왜 | 2\~4줄 |
| 관련 Issue · 요구사항 | `Closes #N`, 이슈에 적은 요구사항 ID |
| 고민한 점 | 검토한 대안과 고른 이유. 없으면 "없음". 회고 리포트의 기술 결정 카드(FR-REP-07)로 옮겨집니다 |
| 검증 | 실제로 해 본 것. 못 한 것은 못 했다고 |
| UI 변경 · 영향 범위 | 해당할 때만 |

권장: 변경이 400줄을 넘으면 나눕니다.

## 6. 리뷰와 머지

| 머지 조건 | 머지하는 곳 | 의견이 갈리면 |
| --- | --- | --- |
| GitHub 리뷰 승인 1명 이상 + (실제 단계가 있으면) CI 통과 | **GitHub PR 화면에서 직접** squash merge | 회의실 안건으로 올려 다룸 |

- **앱의 PR 추천 · 비추천 평가는 머지 조건이 아닙니다.** 팀원끼리 보는 평가 척도이고, 앱은 GitHub PR 화면 링크만 줍니다.
- 리뷰 요청을 받으면 24시간 안에 첫 의견을 남깁니다. 앱의 리뷰 대기 막힘 기준(48시간)보다 짧게 잡은 팀 약속입니다.
- GitHub 리뷰에서 변경 요청(Request changes)을 할 때는 이유를 한 줄 남깁니다.
- CI가 placeholder(`echo`)인 동안 CI 초록 표시는 검증으로 세지 않습니다. 리뷰어가 「검증」 칸을 봅니다.

## 7. 라벨

| 라벨 | 붙는 곳 | 쓰임 |
| --- | --- | --- |
| `feature` · `bug` · `chore` | 템플릿이 자동으로 | 이슈 종류 |
| `area:auth` · `sync` · `req` · `office` · `flow` · `vote` · `instructor` · `meeting` · `plan` · `report` | 직접 (선택) | 기능 영역. 회고 리포트의 "담당 기능 영역"(FR-REP-01)에 모임. 커밋 scope와 같은 값 |

- GitHub 기본 라벨은 `bug`만 남기고 지웁니다(`enhancement`, `documentation` 등은 종류 라벨과 헷갈림).
- 정의 파일은 `.github/labels.yml`입니다. GitHub에 자동 반영되지 않으니 라벨 화면에서 같은 이름으로 만듭니다.

## 8. GitHub 설정

**개발 레포는 새로 만듭니다**(데모 fork `SamdoRyu/warehouseDemo_fork`를 그대로 쓰지 않음). 이 문서 레포(`SamdoRyu/docs-for-PJ-review`)의 설정이 개발 레포에 있다고 가정하지 않습니다. 레포를 만든 뒤 아래를 합니다.

| 설정 | 할 일 |
| --- | --- |
| Issues 기능 | 켜져 있는지 확인 |
| `main` 보호 | PR 필수, 필수 승인 1명, force push · 삭제 금지 |
| 머지 방식 | squash merge만 켜 둠 |
| 머지 후 브랜치 자동 삭제 | 켜기 |
| `.github/` | 이 문서 레포의 `ISSUE_TEMPLATE/`, `PULL_REQUEST_TEMPLATE.md`, `labels.yml`을 옮긴다 |
| 라벨 | `labels.yml`대로 만들고, 기본 라벨은 `bug`만 남김 |
| CI | `npm run lint`, `npm run build`, `npm run test:contract`로 채우고 필수 status check로 등록(`docs/PROJECT_COMMANDS.md`) |
| `.gitignore` | `.env`, `*.pem`, `secrets/` 추가(데모 코드에는 없음) |
| Node 버전 | `.nvmrc`에 Node 24 LTS |
