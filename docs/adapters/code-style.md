# 코드 스타일 어댑터 (예시: Java + Google Java Style + Checkstyle)

[branch-workflow.md](../rules/branch-workflow.md) 8/10단계가 참조하는 "🔌 코드 스타일" 훅이다.
언어/린터가 다르면 이 문서를 그 스택 기준으로 통째로 교체한다.

## 예시: Java + Google Java Style
- 린터(Checkstyle의 `google_checks.xml`)를 CI/커밋 전 체크에 연결하고, 경고 허용치를 0으로
  맞춰 경고가 하나라도 있으면 빌드가 실패하게 한다.
- 커밋 전 반드시 린트 명령어(`./gradlew checkstyleMain`, 테스트 코드 변경 시
  `./gradlew checkstyleTest`)로 통과를 확인한다.
- 주요 규칙:
  - 들여쓰기 2칸, 탭 사용 금지
  - 한 줄 100자 제한
  - import는 static import와 일반 import를 각각 알파벳 순으로 정렬(와일드카드 import 금지)
  - 중괄호는 K&R 스타일(Egyptian brackets), 항상 사용(한 줄짜리 if라도 생략 금지)
- 주석/문서화 주석:
  - 공개 API(서비스/컨트롤러의 public 메서드, 클래스)에만 문서화 주석(Javadoc)을 작성한다.
  - 코드만 읽어도 알 수 있는 내용은 주석으로 남기지 않는다(WHY만, WHAT은 지양).
  - 문장 구조·표현은 [sentence-refinement.md](../rules/sentence-refinement.md)(원칙 1~6,
    특히 원칙 6 "코드 주석 전용 규칙")를 따른다.
- 기존 도메인 패키지 구조(예: `controller / dto / service / exception / entity / repository`
  — 언어/프레임워크에 따라 레이어 이름은 다를 수 있다)를 기본으로 따른다.
  - **단, 컨벤션이 유지보수성보다 우선하지 않는다.** 목록에 없는 폴더가 정말 필요하면(기존
    레이어 중 어디에도 자연스럽게 안 맞는 경우) 임의로 아무 데나 넣기보다, 기획서/PR에서
    "왜 새 폴더가 필요한지"를 명시해 검토받고 추가한다.

## 이 훅을 다른 스택으로 교체할 때
언어가 바뀌어도 위 5개 항목(린터 강제 방식/명령어, 스타일 규칙 요약, 주석 정책, 레이어
구조, 컨벤션 예외 처리 방법)만 그 스택 기준으로 채우면 된다. 예: TypeScript + ESLint +
Prettier, Python + Ruff/Black 등.
