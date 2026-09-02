# 테스트 컨벤션 어댑터 (예시: JUnit5 + Mockito + AssertJ)

[branch-workflow.md](../rules/branch-workflow.md) 8/10단계가 참조하는 "🔌 테스트" 훅이다.
테스트 프레임워크가 다르면 이 문서를 그 스택 기준으로 통째로 교체한다.

## 예시: Java + JUnit5 + Mockito + AssertJ
- 프레임워크: JUnit5 + Mockito(`BDDMockito`의 `given/willReturn`) + AssertJ
- 클래스 구조:
  - 메서드 단위로 `@Nested` 클래스 + `@DisplayName("메서드명")`으로 그룹화
  - 각 테스트 케이스는 `@Test @DisplayName("시나리오 설명")`(표기 언어는 팀 공용어로 통일)
- Mock: 실제 DB/캐시/외부 API(SMS, 결제 등)는 호출하지 않고 목킹한다.
- 검증 범위: 정상 케이스뿐 아니라 예외 케이스(커스텀 예외 + 해당 도메인 에러 코드)까지
  반드시 작성한다.
- 새 서비스/컨트롤러 로직을 추가하면 대응하는 테스트를 반드시 함께 작성한다(기획서에
  정의된 엔드포인트당 최소 1개 이상).
- 커밋 전 테스트 명령어(`./gradlew test`)로 통과를 확인한다.

## 이 훅을 다른 스택으로 교체할 때
프레임워크가 바뀌어도 위 5개 항목(프레임워크, 그룹화/네이밍 구조, 목킹 대상, 필수 검증
범위, 커밋 전 실행 명령어)만 채우면 된다. 예: Jest/Vitest(Node), Pytest(Python),
Go의 `testing` + `testify` 등.
