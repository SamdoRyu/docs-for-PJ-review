---
name: prepare-pr
description: 현재 branch의 변경을 리뷰 가능한 GitHub Pull Request로 정리할 때 사용한다.
disable-model-invocation: true
---

# Prepare PR

PR을 만들기 전에:

1. `git status`
2. base가 `develop`인지 확인 (hotfix · 승격 PR만 `main`). GitHub 기본 base가 `main`이라, 이슈 PR이 실수로 `main`을 잡지 않았는지 본다
3. `git diff origin/develop...HEAD` 기준 전체 변경 검토 (base가 `main`인 PR은 `origin/main...HEAD`)
4. unrelated change 제거
5. debug log / mock / TODO / secret 확인
6. 프로젝트의 가능한 lint/test/typecheck/build 수행
7. Issue acceptance criteria와 대조
8. "관련 요구사항"에 이슈의 요구사항 ID와 같은 ID를 적었는지 확인
9. "고민한 점"에 검토한 대안과 고른 이유를 적었는지 확인. 비어 있으면 작성자에게 묻는다 — 지어내서 채우지 않는다

PR 제목:

```text
<type>: <summary>
```

PR body에는 반드시 포함:
- 무엇을 · 왜 (변경 목적과 주요 변경)
- 이슈 PR은 `Refs #<issue>` / 승격 PR은 포함 이슈 전부 `Closes #<issue>`
- 관련 요구사항 (요구사항 정의서 ID)
- 고민한 점 (검토한 대안, 고른 이유)
- 실제 수행한 검증
- UI 변경 자료(해당 시)
- GitHub 권한·API 호출량 변경 (없음 / 있음). API/DB/env/dependency 영향과 미검증 · 위험은 있을 때만

검증하지 않은 것을 했다고 적지 않는다.

사용자가 요청하지 않았다면 `git push`, PR 생성, merge를 자동 실행하지 않는다.
