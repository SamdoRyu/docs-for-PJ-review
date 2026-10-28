# Git 전략

## 브랜치 전략

main은 시연·배포용, develop은 통합용으로 두고, 모든 작업은 이슈 하나당 브랜치 하나를 최신 develop에서 만들어 PR로 합칩니다.

&#91;embedded content: 브랜치 흐름 · main, develop, 작업, hotfix\]

develop → main 승격은 7일차 1차 데모, 10일차 기능 동결, 12일차 시연 영상 촬영, 14일차 발표 직전, 이렇게 네 번 합니다.

| 브랜치 | 이름 | 만드는 곳 | 합치는 곳 |
| --- | --- | --- | --- |
| 배포 | `main` | 레포 기본 | 승격 PR과 hotfix PR만 받음 |
| 통합 | `develop` | main에서 1일차에 한 번 | 이슈 PR을 받음 |
| 작업 | `<type>/<이슈번호>-<slug>`, 예: `feat/12-github-login` | 최신 develop | develop (squash merge) |
| 긴급 수정 | `hotfix/<이슈번호>-<slug>` | main | main (squash merge), 이어서 main → develop 동기화 |

**규칙**

- type은 `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `hotfix` 중 하나이고, 이슈의 종류 라벨을 따릅니다.
- slug는 짧은 영문 kebab-case로, 3단어 이내로 씁니다.
- main과 develop에는 직접 push와 force push를 하지 않습니다. 남의 브랜치도 force push하지 않습니다.
- 머지된 브랜치는 바로 삭제합니다.

우리 앱의 브랜치 이름 검사(FR-HAB-08)에는 아래 패턴을 등록합니다.

```text
^(feat|fix|refactor|test|docs|chore|hotfix)/[0-9]+-[a-z0-9]+(-[a-z0-9]+)*$
```

## 커밋 메시지

커밋은 Conventional Commits 형식 `<type>: <무엇을 했는지>`로 쓰고, 이슈 PR은 squash merge하므로 develop에 남는 최종 커밋은 PR 제목입니다.

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

**규칙**

- 제목은 50자 이내, 한국어로 써도 되고 끝에 마침표를 찍지 않습니다.
- 범위를 밝히고 싶으면 `feat(sync): …`처럼 괄호에 영역을 적습니다. 영역 이름은 라벨의 `area:` 값과 같게 씁니다.
- 본문은 필요할 때만 쓰고, "무엇을"보다 "왜"를 적습니다.
- 한 커밋에 한 가지 변경만 담습니다.

**예시**

```text
feat(sync): 레포 변경분 5분 폴링 추가
fix(vote): 작성자 본인 투표가 저장되던 문제 수정
docs: 환경 변수 가이드에 GitHub App 설정 추가
```

우리 앱의 커밋 규칙 검사(FR-HAB-04)에는 아래 패턴을 등록합니다.

```text
^(feat|fix|refactor|perf|test|docs|style|build|ci|chore)(\([a-z0-9-]+\))?: .{1,50}$
```

## 이슈 규칙

모든 작업은 이슈에서 시작하고, 기능과 작업 이슈에는 요구사항 정의서의 ID를 반드시 적어 설계·개발·테스트를 ID로 추적합니다.

- 템플릿은 Feature, Bug, Task 세 가지만 쓰고 빈 이슈는 막아 둡니다 (`config.yml`).
- 제목 앞머리 `[Feature]`, `[Bug]`, `[Task]`는 템플릿이 붙입니다. 뒤에는 결과를 한 줄로 씁니다. 예: `[Feature] 레포 변경분 폴링 동기화`
- Feature와 Task는 "요구사항 ID"가 필수입니다. 설정 작업처럼 해당 ID가 없으면 "없음"이라고 씁니다.
- 이슈 하나는 1\~2일 안에 끝낼 크기로 나눕니다. 넘으면 하위 이슈로 쪼갭니다.
- 작업을 시작할 때 담당자를 지정합니다. 우리 앱에서는 담당자 지정이 곧 상자가 내 책상으로 옮겨지는 것입니다 (FR-OFF-02).

**템플릿 수정안**

`feature.yml`과 `task.yml`의 `body:` 맨 위에 아래 칸을 넣습니다. `bug.yml`에는 같은 칸을 `required: false`로 넣습니다.

```yaml
  - type: input
    id: requirement
    attributes:
      label: 요구사항 ID
      description: 요구사항 정의서의 ID. 여러 개면 쉼표로 구분합니다.
      placeholder: "예: FR-GH-03, NFR-10"
    validations:
      required: true
```

## PR 규칙

PR은 develop을 base로 이슈 하나당 하나씩 올리고, 리뷰와 머지는 우리 앱의 찬성·반대 기능이 나오면 그 기능으로 직접 해 봅니다.

- **base:** `develop`. hotfix와 develop → main 승격 PR만 `main`입니다. GitHub 기본 base가 `main`이라 PR을 만들 때 꼭 확인합니다.
- **제목:** 커밋 메시지와 같은 형식입니다. squash merge 후 그대로 develop의 커밋이 됩니다. 예: `feat(sync): 레포 변경분 5분 폴링 추가`
- **이슈 연결:** develop 대상 PR은 `Refs #N`, 승격 PR은 포함된 이슈를 `Closes #N`으로 한 줄씩 나열합니다.
- **크기:** 변경이 400줄을 넘으면 나눕니다. 우리 앱의 PR 크기 안내(FR-HAB-07) 기준도 400줄로 맞춥니다.
- **본문:** 템플릿을 채웁니다. 특히 "고민한 점"은 회고 리포트의 기술 결정 카드에서 "왜 그렇게 결정했나" 칸으로 그대로 옮겨집니다.

**리뷰와 머지**

| 시기 | 머지 조건 | 의견이 갈리면 |
| --- | --- | --- |
| 앱의 투표 기능 전 (약 7일차까지) | CI 통과 + GitHub 리뷰 승인 2명 | 다음 날 스탠드업에서 안건으로 다룸 |
| 앱의 투표 기능 후 | CI 통과 + 앱에서 찬성 2표, 반대 0표 → 작성자가 GitHub에서 squash merge | 투표와 상관없이 누구나 PR을 회의실 안건으로 올려 회의 |

- 리뷰 요청을 받으면 12시간 안에 첫 의견을 남깁니다. 막힘 기준 기본값(리뷰 대기 12시간, TBD-05)과 같습니다.
- 반대에는 PR 내용에 대한 이유를 한 줄 이상 남깁니다.
- 이슈 PR과 hotfix는 squash merge, develop → main 승격과 main → develop 동기화는 merge commit으로 합칩니다.

**PR 템플릿 수정안**

"관련 Issue" 아래에 두 섹션을 넣습니다.

```markdown
## 관련 요구사항

<!-- 요구사항 정의서 ID. 이슈에 적은 것과 같게 -->

FR-

## 고민한 점

<!-- 검토한 대안과 이 방법을 고른 이유. 회고 리포트의 기술 결정 카드로 옮겨집니다 -->

- 검토한 대안:
- 고른 이유:
```

"영향 범위 / 위험" 목록에는 `- GitHub 권한·API 호출량 변경: 없음 / 있음` 한 줄을 더합니다.

## 라벨

라벨은 종류 라벨 하나와 영역 라벨 하나를 붙입니다. 종류 라벨은 브랜치와 커밋 type을 정하고, 영역 라벨은 회고 리포트의 "다룬 기술"(FR-REP-01)로 모입니다.

| 종류 라벨 | 붙는 곳 | 브랜치·커밋 type |
| --- | --- | --- |
| `feature` | Feature 템플릿 | `feat` |
| `bug` | Bug 템플릿 | `fix` (시연 직전 긴급 수정은 `hotfix`) |
| `chore` | Task 템플릿 | `chore`, `refactor`, `test`, `docs` 중 맞는 것 |

| 영역 라벨 | 정의서 기능 그룹 |
| --- | --- |
| `area:auth` | 계정·권한 (FR-AUTH) |
| `area:sync` | GitHub 연동 (FR-GH) |
| `area:req` | 요구사항 추적 (FR-REQ) |
| `area:office` | 3D 사무실 (FR-OFF) |
| `area:flow` | 진행·막힘 (FR-FLOW) |
| `area:vote` | PR 투표와 회의실 학습 (FR-LRN, 제안) |
| `area:habit` | 협업 기본기·컨벤션 피드백 (FR-HAB) |
| `area:meeting` | 회의·스탠드업 (FR-MTG) |
| `area:plan` | 일정판 (FR-PLAN) |
| `area:report` | 회고 리포트 (FR-REP) |

이 Git 전략 전체를 우리 앱의 컨벤션 규칙(FR-HAB-10)에 그대로 등록해, 개발 중인 우리 레포를 직접 검사 대상으로 씁니다.
