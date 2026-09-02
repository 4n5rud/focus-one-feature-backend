# 커밋 규칙

- 형식: `<type>(<scope>): <설명>` (scope 생략 가능: `<type>: <설명>`)
- `type`: `feat`, `fix`, `refactor`, `chore`, `test`, `docs`
- `scope`: 변경이 속한 도메인명 (`auth`, `user`, `order`, `payment` 등)
- 설명은 현재형("~구현", "~수정", "~추가")으로 간결하게 작성한다(언어는 팀 공용어를 따른다 —
  아래 예시는 한글 기준).
- 문장 구조는 [sentence-refinement.md](./sentence-refinement.md)의 원칙 1~5(특히 원칙 2
  "필요한 정보만 남기기")를 따른다.
- 작은 task 단위로 자주 커밋한다. 한 기능을 한 번에 몰아서 커밋하지 않는다.
  - 예: 엔티티/마이그레이션 → 서비스 로직 → 컨트롤러 → 테스트 순서로 분리
- 브랜치 네이밍은 [branch-workflow.md](./branch-workflow.md) 7단계를 따른다: `feat/#{이슈번호}-{slug}` / `fix/#{이슈번호}-{slug}`

## 예시
- `feat(user): 회원가입 시 이메일 인증 로직 추가`
- `feat(auth): 로그인 시 액세스/리프레시 토큰 발급 구현`
- `fix(order): 주문 취소 시 재고가 복구되지 않던 문제 수정`
- `test(payment): 결제 실패 케이스 테스트코드 추가`
