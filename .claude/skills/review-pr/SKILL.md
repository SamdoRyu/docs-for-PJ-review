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
- GitHub 쓰기 호출 없음 — GitHub API에 POST, PATCH, PUT, DELETE를 보내는 코드가 없다 (CON-02). GitHub API 호출은 `adapters/github` 안에만 있다.
- 사람별 합계·순위 필드 없음 — 응답 DTO와 화면에 사람별 투표 합계, 활동량 합계, 순위, 사람 간 비교가 없다 (FR-FLOW-06, FR-LRN-09). 개인 지표는 본인 요청에만 나간다 (NFR-06).

각 finding은:
- Severity: blocker / major / minor
- 위치
- 문제
- 왜 문제인지
- 최소 수정 제안

문제가 없으면 억지 finding을 만들지 않는다.
마지막에 "merge 가능 / 수정 후 가능 / 현재 merge 비권장" 중 하나로 결론낸다.
