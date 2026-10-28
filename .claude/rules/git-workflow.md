# Git Workflow

기준: 개발 컨벤션 > Git 전략. 이 파일과 충돌하면 Git 전략을 따른다.

## Branch

`main`은 시연·배포용이다. develop → main 승격 PR과 hotfix PR만 받는다.

`develop`은 통합용이다. 1일차에 main에서 한 번 만들고, 이슈 PR을 받는다. 직접 개발하지 않는다.

develop → main 승격은 7일차 1차 데모, 10일차 기능 동결, 12일차 시연 영상 촬영, 14일차 발표 직전, 네 번 한다 (기획안 일정 기준 계획, 팀 확정 전).

| 브랜치 | 이름 | 만드는 곳 | 합치는 곳 |
| --- | --- | --- | --- |
| 배포 | `main` | 레포 기본 | 승격 PR과 hotfix PR만 받음 |
| 통합 | `develop` | main에서 1일차에 한 번 | 이슈 PR을 받음 |
| 작업 | `<type>/<이슈번호>-<slug>` | 최신 develop | develop (squash merge) |
| 긴급 수정 | `hotfix/<이슈번호>-<slug>` | main | main (squash merge), 이어서 main → develop 동기화 |

- 이슈 하나당 브랜치 하나다.
- type은 `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `hotfix` 중 하나이고, 이슈의 종류 라벨을 따른다.
- slug는 짧은 영문 kebab-case로, 3단어 이내로 쓴다. 예: `feat/12-github-login`
- 머지된 브랜치는 바로 삭제한다.
- 팀원의 브랜치를 force push하지 않는다.

브랜치 이름 패턴 (앱의 브랜치 이름 검사 FR-HAB-08에도 같은 패턴을 등록한다):

```text
^(feat|fix|refactor|test|docs|chore|hotfix)/[0-9]+-[a-z0-9]+(-[a-z0-9]+)*$
```

## Commit

Conventional Commits `<type>: <무엇을 했는지>`. 이슈 PR은 squash merge하므로 develop에 남는 최종 커밋은 PR 제목이다.

| type | 언제 |
| --- | --- |
| `feat` | 사용자가 쓰는 기능 추가 |
| `fix` | 버그 수정 |
| `refactor` | 동작은 그대로, 구조만 바꿈 |
| `perf` | 성능 개선 |
| `test` | 테스트 추가·수정 |
| `docs` | 문서만 바꿈 |
| `style` | 포맷·세미콜론 등 동작과 무관한 변경 |
| `build` | 빌드 설정, 의존성 |
| `ci` | CI 설정 |
| `chore` | 그 밖의 잡일 |

- `: ` 뒤 설명은 50자 이내, 한국어로 써도 되고 끝에 마침표를 찍지 않는다.
- `hotfix`는 브랜치 type일 뿐 커밋 type이 아니다. hotfix PR 제목은 `fix: …`로 쓴다.
- 범위를 밝히려면 `feat(sync): …`처럼 괄호에 영역을 적는다. 영역 이름은 라벨의 `area:` 값과 같게 쓴다.
- 본문은 필요할 때만 쓰고, "무엇을"보다 "왜"를 적는다.
- 한 커밋에 한 가지 변경만 담는다.

커밋 메시지 패턴 (앱의 커밋 규칙 검사 FR-HAB-04에도 같은 패턴을 등록한다):

```text
^(feat|fix|refactor|perf|test|docs|style|build|ci|chore)(\([a-z0-9-]+\))?: .{1,50}$
```

## Issue

- 모든 작업은 이슈에서 시작한다.
- 템플릿은 Feature, Bug, Task 세 가지만 쓴다. 빈 이슈는 막혀 있다 (`.github/ISSUE_TEMPLATE/config.yml`).
- 제목 앞머리 `[Feature]`, `[Bug]`, `[Task]`는 템플릿이 붙인다. 뒤에는 결과를 한 줄로 쓴다.
- Feature와 Task는 "요구사항 ID"가 필수다. 해당 ID가 없는 설정 작업은 "없음"이라고 쓴다.
- 이슈 하나는 1~2일 안에 끝낼 크기로 나눈다. 넘으면 하위 이슈로 쪼갠다.
- 작업을 시작할 때 담당자를 지정한다.
- 라벨은 종류 라벨 하나와 영역 라벨(`area:*`) 하나를 붙인다. 목록은 `.github/labels.yml`.

## PR

- base: `develop`. hotfix와 develop → main 승격 PR만 `main`이다. GitHub 기본 base가 `main`이라 PR을 만들 때 꼭 확인한다.
- 제목: 커밋 메시지와 같은 형식. squash merge 후 그대로 develop의 커밋이 된다.
- 이슈 연결: develop 대상 PR은 `Refs #N`, 승격 PR은 포함된 이슈를 `Closes #N`으로 한 줄씩 나열한다.
- 크기: 변경이 400줄을 넘으면 나눈다.
- 본문: `.github/PULL_REQUEST_TEMPLATE.md`를 채운다. "관련 요구사항"은 이슈에 적은 ID와 같게, "고민한 점"은 검토한 대안과 고른 이유를 쓴다 (회고 리포트의 기술 결정 카드로 옮겨진다).

## 리뷰와 머지

| 시기 | 머지 조건 | 의견이 갈리면 |
| --- | --- | --- |
| 앱의 투표 기능 전 (지금) | GitHub 리뷰 승인 2명 + 리뷰어가 PR 「검증」의 실제 명령·결과 확인. CI가 placeholder인 동안 CI 통과는 검증으로 세지 않는다 | 다음 날 스탠드업에서 안건으로 다룸 |
| 앱의 투표 기능 후 (FR-LRN-01~03 채택·구현·확인과 팀의 전환 합의 뒤) | CI 통과(실제 단계가 있을 때) + 앱에서 찬성 2표, 반대 0표 → 작성자가 GitHub에서 squash merge | 회의실 안건으로 올려 회의 |

- 투표와 상관없이 누구나 PR을 회의실 안건으로 올릴 수 있다.
- 리뷰 요청을 받으면 12시간 안에 첫 의견을 남긴다.
- 반대에는 PR 내용에 대한 이유를 한 줄 이상 남긴다.
- 이슈 PR과 hotfix는 squash merge, develop → main 승격과 main → develop 동기화는 merge commit으로 합친다.

## 절대 하지 않을 것

- `main` · `develop` 직접 개발/직접 push
- force push to `main` · `develop`, 남의 브랜치 force push
- 승격 · 동기화 PR을 squash merge
- unrelated changes 포함
- merge conflict를 추측으로 해결
- 사용자의 명시적 요청 없는 merge/deploy
