---
name: review-pr
description: PR diff를 요구사항, 회귀, 타입, 에러 처리, 보안, 테스트 관점에서 검토할 때 사용한다.
---

# Review PR

리뷰는 코드 스타일 취향보다 defect risk를 우선한다.

우선순위:
1. 이슈의 요구사항 ID 수용 기준 불충족 — PR의 "관련 요구사항" ID가 이슈와 같은지, 그 ID의 수용 기준을 diff가 채우는지
2. 기능 regression
3. 보안/권한
4. 데이터 손실/잘못된 상태
5. race condition / async 오류
6. error handling
7. 타입 불일치
8. 테스트 누락
9. 유지보수성

팀 오피스 원칙 점검 (위반이면 blocker):
- GitHub 쓰기 호출 없음 — GitHub 원본(이슈 · PR · 코멘트 · 라벨 등)을 바꾸는 GitHub API 호출이 없다 (CON-02). OAuth 코드 교환 · 설치 토큰 발급 같은 인증 요청과 앱 서버 자체 API의 쓰기는 위반이 아니다. GitHub API 호출은 `adapters/github` 안에만 있다.
- 사람별 합계·순위 필드 없음 — 응답 DTO와 화면에 사람별 평가 합계, 활동량 합계, 순위, 사람 간 비교가 없다 (FR-FLOW-06, FR-LRN-09). 개인 지표는 본인과 강사 요청에만 나간다 (NFR-06).
- 강사 쓰기 제한 — 강사(ROLE-04)의 쓰기는 학생이 요청한 건(도움 요청 답변 · 꼬리질문 · 결정 피드백)과 컨벤션 라이브러리에만 열린다. 그 밖의 강사 쓰기는 API에서 `forbidden_role`로 막혀 있다 (FR-AUTH-08). 강사 의견이 결정 · 평가 · 안건 · 도움 요청 상태를 바꾸지 않는다 (SP3).

각 finding은:
- Severity: blocker / major / minor
- 위치
- 문제
- 왜 문제인지
- 최소 수정 제안

문제가 없으면 억지 finding을 만들지 않는다.
마지막에 "merge 가능 / 수정 후 가능 / 현재 merge 비권장" 중 하나로 결론낸다.
