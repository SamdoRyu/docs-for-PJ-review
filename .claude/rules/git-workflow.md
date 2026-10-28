# Git Workflow

기준: 개발 컨벤션 > Git 전략(`docs/plan/Git 전략.md`). 이 파일과 충돌하면 Git 전략을 따른다.

## 꼭 지킬 것

- `main`에 직접 push하지 않는다. PR로만 합친다.
- `main`과 남의 브랜치에 force push하지 않는다.
- 작업은 이슈에서 시작하고, PR 본문에 `Closes #N`을 적는다. `Refs #N`은 쓰지 않는다.
- PR 제목은 `<type>(<scope>)?: <설명>` 형식이다. squash merge 후 `main`의 커밋이 된다.
- 머지는 GitHub 리뷰 승인 1명 이상을 받은 뒤 GitHub PR 화면에서 사람이 직접 한다. 앱의 찬성·반대 투표는 머지 조건이 아니다.
- 사용자의 명시적 요청 없이 merge · deploy하지 않는다.

## Branch

- 브랜치는 `main`과 작업 브랜치뿐이다. `develop` · 승격 · hotfix 브랜치는 없다.
- 작업 브랜치는 최신 `main`에서 만들고, PR로 `main`에 squash merge한다.
- 이름은 `<type>/<이슈번호>-<짧은-설명>`. type은 `feat`, `fix`, `refactor`, `test`, `docs`, `chore`. 이슈번호에 `#`을 붙이지 않는다. 예: `feat/12-github-login`, `fix/18-login-error`

```text
^(feat|fix|refactor|test|docs|chore)/[0-9]+-[a-z0-9]+(-[a-z0-9]+)*$
```

## Commit · PR 제목

- 브랜치 안의 커밋 메시지는 자유다. 형식은 PR 제목에만 요구한다.
- type: `feat`, `fix`, `refactor`, `perf`, `test`, `docs`, `style`, `build`, `ci`, `chore`.
- 설명은 50자 이내, 한국어 가능, 끝에 마침표 없음.

```text
^(feat|fix|refactor|perf|test|docs|style|build|ci|chore)(\([a-z0-9-]+\))?: .{1,50}$
```

## Issue · PR

- 이슈 템플릿은 Feature, Bug, Task. 요구사항 ID는 템플릿의 「요구사항 ID」 칸에 적는다(Feature는 필수).
- PR base는 `main`. 본문에 `Closes #N`.
- 본문은 `.github/PULL_REQUEST_TEMPLATE.md`. 「검증」에는 실제로 돌린 것만 적는다. CI가 placeholder인 동안 CI 통과는 검증이 아니다.
- 권장: 400줄을 넘으면 나눈다.

## 리뷰

- 리뷰 요청을 받으면 24시간 안에 첫 의견을 남긴다.
- 반대 · 변경 요청에는 이유를 한 줄 남긴다.
- 의견이 갈리면 회의실 안건으로 올린다.
