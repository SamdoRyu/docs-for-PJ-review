# Claude Code 프롬프트 모음

각자 자기 세션에서 **그대로 복사해 붙여 넣는** 프롬프트다. `<>` 안만 자기 것으로 바꾼다.

저장소 루트에서 `claude` 를 실행하면 `.claude/rules/` 의 규칙(git-workflow · code-quality · security · testing · scope-control)이 자동으로 적용된다. 그래서 프롬프트에 그 규칙을 다시 적지 않는다.

> 이 프로젝트에서 반복할 작업은 네 가지다 — **§1 요구사항 ID로 기능 구현 · §2 버그 수정 · §3 PR 준비(자주 쓰는 것) · §4 문서 정합성 점검.** 담당 · 상태는 [BACKEND_ASSIGNMENTS.md](./BACKEND_ASSIGNMENTS.md)(백엔드 기호 B0~B12) · [SCREEN_ASSIGNMENTS.md](./SCREEN_ASSIGNMENTS.md), Must 이슈 목록은 [00-backlog.md](./00-backlog.md) 「이슈 백로그 (Must)」(이슈 후보 IB-01~IB-50 · 착수 조건 열).

---

## 0. 받은 직후 한 번만

```
이 저장소를 처음 받았어. 아래를 순서대로 확인하고 결과만 짧게 알려줘.

1. 지금 branch 와 working tree 상태
2. docs/PROJECT_COMMANDS.md 의 설치 · 실행 명령이 채워져 있는지 (TODO 면 그렇다고만 알려줘)
3. 채워져 있으면 설치하고 lint 와 build 가 통과하는지
4. .env 계열 파일이 있는지 — 값은 출력하지 말고 "있음 / 없음" 만

문제가 있으면 원인만 알려주고 임의로 고치지는 마.
```

---

## 0-1. develop 기준으로 이어서 시작 — 기존 로컬 저장소

작업은 `feature → develop → main` 흐름이다(`.claude/rules/git-workflow.md`). 이미 받아 둔 저장소가 `main` 에 있으면 아래를 **한 번** 붙여 넣은 뒤 §1 로 이슈를 시작한다.

```
내 로컬을 develop 기준 흐름에 맞춰줘.
아래 순서대로 하고 단계마다 결과를 짧게 보여줘. 예상과 다르면 멈추고 알려줘.

1. git status 로 working tree 가 깨끗한지 확인해. 커밋하지 않은 변경이 있으면 아무것도 하지 말고 알려줘.
2. git switch main 후 git pull --ff-only origin main 으로 main 을 최신화해.
3. git fetch origin --prune 후 git switch develop 으로 develop 으로 넘어가.
   이미 로컬 develop 이 있으면 git pull --ff-only origin develop 으로 최신화해.
4. .claude/rules/git-workflow.md 를 읽고 브랜치 · 커밋 · PR · 머지 규칙을 세 줄로 요약해.

지킬 것:
- main · develop 에서 직접 커밋하지 마. 작업은 develop 에서 새 branch 를 만들어 해.
- force push · rebase · reset --hard 는 하지 마.
- merge 는 하지 마.
```

---

## 1. 요구사항 ID로 기능 구현

`<이슈번호>` 와 `<슬러그>` 두 군데만 자기 것으로 바꾼다. 슬러그는 짧은 영문 kebab-case, 3단어 이내다.

```
GitHub 이슈 #<이슈번호> 를 구현할 거야.

시작 전에:
1. gh issue view <이슈번호> 로 이슈 전문을 읽어. "요구사항 ID" 칸이 이 작업의 기준이야.
2. 그 요구사항 ID 를 docs/03-requirements.md 에서 찾아 요구 · 중요도를 확인해.
   [제안] 이 붙은 ID 면 docs/requirements-changes.md 에서 상태를 보고, "검토 중" 이면 멈추고 알려줘.
   "원본 확인 필요"로 표시된 ID 면 수용 기준이 없으니 멈추고 알려줘.
   기능 구현 기준은 docs/04-features.md 「인수 조건」 열이야. "초안"이면 그 사실도 함께 알려줘.
3. 그 ID 가 속한 기능을 docs/04-features.md 에서, 기능의 정책(P)을 docs/05-policy.md 에서,
   저장 항목을 docs/06-data.md 에서, 화면을 docs/07-screens.md 에서 확인해.
4. docs/ARCHITECTURE.md 의 모듈 경계를 확인해.

작업 규칙:
- develop 을 최신화한 뒤 feat/<이슈번호>-<슬러그> branch 를 새로 만들어.
- 이슈의 완료 조건과 범위 메모 안에서만 작업하고, 제외한 것은 하지 마.
- GitHub API 는 adapters/github 밖에서 부르지 말고, GitHub 원본(이슈 · PR · 코멘트 · 라벨)을 바꾸는 호출은 만들지 마. 로그인 · 설치 토큰 발급 같은 인증 요청은 괜찮아.
- 응답에 사람별 합계 · 순위 필드를 만들지 마. 개인 지표는 본인 요청에만 담아.
- 기준값(막힘 시간 · 투표 기준 · PR 크기)은 코드에 박지 말고 팩과 레포 설정에서 읽어.
- 화면 확인과 테스트는 GitHub 없이 브라우저 mock(주소에 `?source=` 없이)으로 해.
- 기획 문서(docs/01~07)는 고치지 마. 문서와 다르게 해야 하면 멈추고 알려줘.

다 만들면:
- docs/PROJECT_COMMANDS.md 의 lint · test · build 를 돌리고 결과를 그대로 보여줘.
- 요구사항 ID 의 수용 기준을 한 줄씩 대조해 충족 / 미충족 / 미검증으로 보여줘.
- 검증 안 한 건 했다고 하지 말고 그대로 말해줘.

먼저 계획을 짧게 보여주고 시작해.
```

### 영역 라벨별 요구사항

| 영역 라벨 | 요구사항 ID |
| --- | --- |
| `area:auth` | FR-AUTH |
| `area:sync` | FR-GH |
| `area:req` | FR-REQ |
| `area:office` | FR-OFF |
| `area:flow` | FR-FLOW |
| `area:vote` | FR-LRN (제안) |
| `area:habit` | FR-HAB |
| `area:meeting` | FR-MTG |
| `area:plan` | FR-PLAN |
| `area:report` | FR-REP |

라벨 목록은 `.github/labels.yml`, 커밋 scope 를 쓸 때는 `area:` 뒤의 값과 같게 쓴다(예: `feat(sync): …`).

---

## 2. 버그 수정

> Bug 템플릿 이슈에 쓴다. 재현 테스트를 먼저 추가하거나, 못 하면 재현 절차를 문서화한다(`.claude/rules/testing.md`).

```
GitHub 이슈 #<이슈번호> 의 버그를 고칠 거야.

시작 전에:
1. gh issue view <이슈번호> 로 현상(실제 · 기대) · 재현 절차 · 환경을 읽어.
   요구사항 ID 가 적혀 있으면 docs/03-requirements.md 에서 그 수용 기준을 확인해.
2. 재현해 보고, 재현됐는지 안 됐는지 먼저 알려줘. 재현이 안 되면 멈춰.

작업 규칙:
- develop 최신화 후 fix/<이슈번호>-<슬러그> branch.
  시연 직전 main 긴급 수정이면 main 에서 hotfix/<이슈번호>-<슬러그> branch.
- 원인을 찾기 전에 고치지 마. 원인과 근거(로그 · 재현 결과)를 먼저 보여줘.
- 가능하면 재현 테스트를 먼저 추가해. core 의 순수 규칙이면 단위 테스트로.
- 최소 수정만 해. 같이 고치면 좋아 보이는 것은 별도 이슈 후보로 한 줄만 적어.
- 테스트를 통과시키려고 제품 코드를 약화하지 마.

다 만들면:
- 재현 테스트가 수정 전 실패 · 수정 후 통과하는지 보여줘.
- docs/PROJECT_COMMANDS.md 의 lint · test · build 결과를 그대로 보여줘.
```

---

## 3. 작업 중 자주 쓰는 것

### 검증

```
docs/PROJECT_COMMANDS.md 의 lint · test · build 를 돌리고, 브라우저 mock(`?source=` 없이)으로 띄워서
<화면 이름> 이 docs/07-screens.md 「화면 목록」의 보이는 것대로 나오는지 확인해줘. 결과를 그대로 보여줘.
```

### PR 만들기

```
작업이 끝났어. PR 을 준비해줘.

- .github/PULL_REQUEST_TEMPLATE.md 양식을 그대로 채워.
- 제목은 Conventional Commits 형식(50자 이내).
- base 는 develop 이야(hotfix 만 main). body 에는 `Refs #<이슈번호>` 를 넣어.
- 「관련 요구사항」에는 이슈에 적은 요구사항 ID 를 그대로 적어.
- 「고민한 점」은 내가 쓸 테니 비어 있으면 나한테 물어봐. 지어내서 채우지 마.
- 「GitHub 권한·API 호출량 변경」을 diff 로 확인해 없음 / 있음을 적어.
- 「검증」에는 실제로 돌린 명령과 결과만 적어. 안 한 건 안 했다고 적어.
- 화면 스크린샷은 내가 직접 붙일 테니 「UI 변경」 자리만 비워둬.
- push 와 PR 생성은 내가 요청할 때만 해. merge 는 하지 마.
```

### 문서와 다른 걸 발견했을 때

```
방금 발견한 <무엇> 이 기획 문서와 달라.
기획 문서는 고치지 말고, 문서가 말하는 것(파일:줄)과 구현이 해야 하는 것을 나란히 정리해서 보여줘.
요구사항 ID 가 걸려 있으면 그 ID 도 적어줘. 문서를 고칠지는 팀이 정한다.
```

### 막혔을 때

```
지금 상황을 정리해줘. 무엇을 시도했고 결과가 어땠는지,
원인을 아는지 모르는지 구분해서 알려줘. 추측으로 고치지는 마.
```

### Claude 가 범위를 넘어설 때

```
그건 이번 이슈 범위 밖이야. 지금 작업에는 넣지 말고,
필요하면 별도 이슈 후보로 한 줄만 적어줘.
```

---

## 4. 문서 정합성 점검 — `/doc-check` 로 시작한다

- 기획 문서(docs/00~07 · PRD)를 고친 뒤, 회의 결과를 반영한 뒤, requirements-changes.md 의 상태가 바뀐 뒤에 쓴다.
- `/doc-check` 는 `doc-consistency-auditor` 에게 감사를 맡긴다. 이 에이전트는 읽기 전용이라 문서를 고치지 않는다.

```
/doc-check <범위 — 비우면 전체 | 최근 | 04 중심>

함께 전달할 것:
- 직전 회의에서 정한 것: <채택 · 제외된 [제안] ID, 새로 결정된 TBD, 후순위로 미룬 항목>
- 정의서 · 기획안 ID ↔ 이슈 ↔ docs 연결(C9)도 점검해줘. 이슈는 gh 조회 명령으로만 읽어.
```

리포트를 받은 뒤:

```
리포트의 BLOCKER 와 확인 필요 항목만 요약해줘.
제안을 문서에 자동으로 반영하지 마. 내가 고를게.
```

### 4-1. TBD 결정 작업용 — 구현 프롬프트와 섞지 않는다

TBD 결정은 **decision-only** 다(BACKEND_ASSIGNMENTS 「B0」). branch · 코드 · PR 이 없다. 위 §1 프롬프트를 쓰지 않는다.

```
docs/05-policy.md 서비스 정책의 [?] TBD 마다 선택지 · 기획안의 근거 · 결정하면 바뀌는 문서와 요구사항 ID 를 표로 정리해줘.
문서 수정은 내가 요청할 때만 해. 결정은 팀이 한다.
```

---

## 주의할 것

- **기획 문서는 프롬프트로 고치지 않는다.** 구현 중 문서와 다르게 해야 하면 멈추고 알린다. 문서 수정은 팀이 정한 뒤 따로 한다.
- **"테스트 완료"를 그대로 믿지 마라.** 실제로 돌린 명령과 결과만 믿는다. 테스트하지 못한 부분은 PR 에 명시한다.
- **GitHub 에 쓰는 코드는 바로 반대한다.** 앱은 GitHub 를 읽기만 한다(CON-02). 리뷰에서 GitHub 원본(이슈 · PR · 코멘트 · 라벨)을 바꾸는 GitHub API 호출을 보면 blocker 다. OAuth 코드 교환 · 설치 토큰 발급 같은 인증 요청과 앱 서버 자체 API의 쓰기는 해당하지 않는다.
- **merge 는 리뷰 규칙대로만 한다.** 앱 투표 기능 전에는 GitHub 승인 2명, 이후에는 앱에서 찬성 2표 · 반대 0표(`.claude/rules/git-workflow.md` 「리뷰와 머지」). "앱 투표 기능 후" 규칙은 [제안] FR-LRN이 채택되고 구현된 뒤에만 쓴다. CI가 placeholder인 동안 "CI 통과"는 검증이 아니다([PROJECT_COMMANDS.md](./PROJECT_COMMANDS.md) 「PR 전에 반드시」). Claude 에게 merge 를 맡기지 않는다.
- **env 값은 출력하지 않는다.** 확인은 "설정됨 / 미설정" 까지만 한다.
