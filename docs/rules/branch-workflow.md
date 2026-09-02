# 기능 구현 워크플로우

사용자가 "이 기능을 구현해줘"라고 요청하면 아래 순서를 반드시 지킨다.
순서를 건너뛰거나 임의로 합치지 않는다. 각 단계 사이의 승인/보고 지점에서는 사용자의 확인
없이 다음 단계로 넘어가지 않는다.

> 이 문서는 **구조**(승인 게이트, 외부화된 진행 상태, 설계 변경 vs 구현 세부사항 분리)만
> 규정하는 core다. 브랜치 전략, 빌드/린트/테스트 도구, 외부 문서 동기화처럼 팀·프로젝트마다
> 달라지는 부분은 본문에서 🔌로 표시하고 `docs/adapters/`의 별도 문서로 뺐다 — 해당 부분은
> 실제 프로젝트가 쓰는 도구로 그 어댑터 문서를 통째로 교체해서 쓴다. 전체 훅 목록은 문서
> 맨 아래 표를 참고한다. **이 프로젝트에서 처음 실행하는 경우 0단계보다 먼저
> [project-setup.md](./project-setup.md)의 어댑터 확정 인터뷰부터 진행한다.**

## 🎤 첫 실행이라면 — 어댑터 확정 인터뷰
`docs/adapters/`에 아직 예시 내용(Java/Gradle/Flyway 등)이 남아있다면, 0단계로 넘어가기
전에 반드시 [project-setup.md](./project-setup.md)에 따라 구조화된 선택형 질문으로
사용자에게 실제 스택을 확인받고 어댑터 파일을 고쳐 쓴다. 이미 채워져 있다면 건너뛴다.

## 🚨 한 번에 하나의 기능만 + 📋 진행 상태 추적
- **항상 "이슈 1개 ↔ 브랜치 1개 ↔ 기능 1개"만 동시에 진행한다.** 진행 중인 이슈가 있으면
  새 이슈의 기획서 작성/브랜치 생성/구현을 시작하지 않는다.
- **진행 상태는 모델의 기억이 아니라 레포 상태(이슈의 0~15단계 체크박스)로 관리한다.**
  세션이 끊겨도 이 체크박스를 읽어서 이어간다.
- 상세 규칙(백로그 이슈 생성 기준, QA 중 발견한 버그 처리 등):
  [progress-tracking.md](./progress-tracking.md)

## 🔌 브랜치 전략
- 브랜치를 어떻게 나누고 어디로 머지하는지, 보호해야 할 브랜치가 있는지는 프로젝트마다
  다르다 — [adapters/branch-strategy.md](../adapters/branch-strategy.md)를 실제 전략으로
  채워 넣는다.
- 아래 단계에서 "통합 브랜치"라고 부르는 것은 이 어댑터 문서가 정의하는, feature 브랜치들이
  가장 먼저 모이는 브랜치를 뜻한다(트렁크 기반이면 default 브랜치 자체가 통합 브랜치다).
- **기획서(2단계)도 브랜치+PR 규율의 예외가 아니다.** "문서만 바뀌니 리뷰가 필요 없다"고
  임의로 판단해 통합 브랜치에 직접 커밋하지 않는다.

## 0. 사전 준비 — 기존 코드 읽기
- 관련 도메인 패키지(예: `user`, `order`, `payment` 등)의 기존 controller/service/dto/
  exception을 먼저 읽고 컨벤션을 파악한다.
    - 공통 응답 포맷, 공통 예외 처리, 캐시/큐 등 공통 인프라 유틸을 담당하는 모듈이 있는지
      먼저 확인한다.
- 유사 기능이 이미 구현되어 있으면 그 구조를 최대한 재사용한다. 새 패턴을 임의로 만들지 않는다.
- 직전 이슈가 PR 머지까지 완전히 끝났는지 확인한다 (아래 "진행 상태 추적" 참고).

## 1. 이슈 생성
- [templates/issue-feature.md](../templates/issue-feature.md) 또는
  [templates/issue-bug.md](../templates/issue-bug.md) 기반으로 생성.
- 이슈 본문에 0~15단계 체크리스트를 포함시킨다.
- 상세 규칙: [issue-template.md](./issue-template.md)

## 2. 기획서 생성 (`docs/domain/{도메인명}/{이슈번호}-{도메인명}-{간략한 제목}.md`)
- 엔드포인트를 설계하기 전 [api-design.md](./api-design.md)의 6원칙(단일 책임/빠른 시작/
  일관성/의미 있는 오류/확장성·성능/하위 호환성)을 검토하고, 기획서의 "리스크 및 고려사항"
  절에 해당 원칙과 관련된 판단(예: 페이지네이션 여부, 하위 호환 여부)을 남긴다.
- 기획서 본문 문장은 [sentence-refinement.md](./sentence-refinement.md)의 원칙 1~5를
  따른다(주어를 분명히, 필요한 정보만, 구체적으로, 자연스러운 표현, 일관된 용어).
- **경로/문서명 규칙**: `docs/domain/{도메인명}/` 폴더 아래에 둔다. 도메인명은 `user`/
  `order`/`payment` 등 커밋 스코프와 동일한 규칙을 따른다(GitHub 이슈에 `domain:X`
  라벨이 있으면 그 값을 소문자로, 없으면 이슈 종류 라벨을 그대로 폴더명으로 쓴다 — 예:
  `chore`/`bug`). 해당 도메인 폴더가 아직 없으면 새로 만든다.
  - 문서명 예: `docs/domain/order/41-order-query.md`. 폴더로 도메인이 이미 구분되지만,
    파일명에도 도메인명을 유지한다(검색 결과·링크만 봐도 어떤 문서인지 알 수 있게).
  - 같은 이슈에서 파생되는 QA 문서/부가 문서는 이 base 이름 뒤에 접미사를 붙인다
    (`{base}-QA.md`, `{base}-api-spec.md` 등), 같은 폴더에 둔다.
  - 그 도메인 전체를 다루는 마스터 기획서(예: `order-domain.md`)가 있으면 파일명 맨 앞에
    `1_`을 붙여(`1_order-domain.md`) 폴더 안에서 항상 맨 위로 정렬되게 한다.
- 이슈 1개당 엔드포인트 1~2개, 또는 기존 서비스 로직 수정 1건으로 범위를 좁힌다. 범위가
  넘치면 이슈를 분리한다.
- 엔드포인트 중심 양식:
    - 개요/목적, 관련 이슈 링크
    - 엔드포인트별: `Method + Path`, 요청 DTO, 응답 DTO, 성공/에러 상태코드 및 에러 코드,
      인증/권한 요구사항
    - 기존 로직을 수정하는 경우: 변경 전/후 동작 차이
    - 🔌 데이터 모델 변경: 엔티티 필드 추가/변경, DB 마이그레이션 필요 여부(파일명/버전
      형식은 [adapters/migration-convention.md](../adapters/migration-convention.md) 참고)
    - 영향 받는 기존 코드/테스트
    - 리스크 및 고려사항

## 3. 기획서 검토 요청
- 기획서 작성 후 반드시 사용자에게 검토를 요청한다. 사용자가 검토하기 전에는 절대 먼저
  작성/구현을 진행하지 않는다.

## 4. 검토 받기
- 사용자의 피드백을 기다린다.

## 5. 수정
- 검토 중 지적된 사항은 기획서에 즉시 반영하고 재검토를 요청한다.

## 6. 검토 종료
- 사용자의 명시적 승인이 있어야 다음 단계(브랜치 생성)로 진행한다.

## 7. 브랜치 생성
- 🔌 [adapters/branch-strategy.md](../adapters/branch-strategy.md)가 정의하는 통합
  브랜치에서 분기한다.
- 네이밍: `feat/#{이슈번호}-{짧은-영문-slug}` (버그 수정은 `fix/#{이슈번호}-...`)

## 8. 기능 구현
- 기획서에 정의된 범위를 벗어나지 않는다 (스코프 크립 금지). 범위 밖의 리팩터링/개선은
  별도 이슈로 분리 제안한다.
- 작은 task 단위로 커밋한다 (예: 엔티티/마이그레이션 → 서비스 로직 → 컨트롤러 → 테스트
  순서로 분리 커밋). 커밋 규칙: [commit-convention.md](./commit-convention.md)
- 🔌 코드 스타일: [adapters/code-style.md](../adapters/code-style.md)
- 코드 내 주석의 문장 구조: [sentence-refinement.md](./sentence-refinement.md)(원칙 1~6,
  특히 원칙 6 "코드 주석 전용 규칙")
- 🔌 테스트: [adapters/test-convention.md](../adapters/test-convention.md)
- **기획서와 실제 코드가 달라지는 경우, 변경의 성격에 따라 다르게 처리한다** — 이 판단
  기준이 이 워크플로우의 핵심 구조다:
    - **구현 세부사항 변경** (DTO 필드 통합/분리, 내부 메서드 시그니처, 변수명 등 — 승인받은
      엔드포인트 계약·정책에 영향 없음): 그 즉시 기획서 파일을 수정해 실제 구현과 일치시키고
      계속 진행한다. PR 설명에만 적어두고 넘어가지 않는다.
    - **설계/정책 변경** (엔드포인트 경로/메서드, 요청·응답 스키마, 인증 요구사항, 에러코드
      정책 등 승인받은 계약 자체가 바뀌는 경우): **구현을 중단하고 4단계(검토 받기)로 되돌아가
      재승인을 받는다.** 승인된 기획서를 임의로 사후 수정해서 넘어가지 않는다 — 이건 3~6단계
      승인 게이트를 무력화하는 것과 같다.
    - 애매하면 설계/정책 변경 쪽으로 간주하고 재승인을 요청한다.

## 9. 코드 리뷰 (자체 점검)
- 기능 구현이 끝나면 QA 전에 diff를 리뷰한다. **직접 리뷰하지 않고, 컨텍스트가 격리된 별도
  에이전트에게 `code-review` 스킬로 리뷰를 맡긴다.** 결과는 QA(10단계)와 합치지 않고
  `docs/domain/{도메인명}/{이슈번호}-{도메인명}-{간략한 제목}-code-review.md`로 즉시
  문서화한다.
- 에이전트를 어떻게 격리하는지, 결과를 어떤 형식으로 남기는지는
  [code-review-isolation.md](./code-review-isolation.md) +
  [code-review-template.md](./code-review-template.md) 참고.
- 🔌 CI에 자동 리뷰 봇(CodeRabbit 등)을 추가로 붙이고 싶다면
  [adapters/review-bot.md](../adapters/review-bot.md) 참고 — 이 자체 점검을 대체하지
  않고 보완하는 용도다.

## 10. 기능 구현 종료 → QA/QC
- 🔌 로컬 빌드/린트/테스트를 통과했는지 확인한다(명령어는
  [adapters/code-style.md](../adapters/code-style.md),
  [adapters/test-convention.md](../adapters/test-convention.md) 참고).
- **CI 파이프라인도 통과했는지 확인한다.** 로컬 통과와 CI 통과는 다를 수 있다(예: CI에서만
  필요한 서비스 컨테이너 의존성). 로컬 통과만으로 이 단계를 끝내지 않는다.
- 기획서에 정의된 엔드포인트별 정상/에러 케이스를 직접 검증한다(가능하면 실제 서버 기동 후
  확인).
- 🔌 팀이 API 클라이언트 컬렉션(Postman 등)을 공유한다면, 이 검증에 쓴 요청들을
  [adapters/api-client-collection.md](../adapters/api-client-collection.md)에 따라
  정리한다.

## 11. 문제사항 보고
- QA(10단계)에서 발견된 문제를 심각도별로 정리해
  `docs/domain/{도메인명}/{이슈번호}-{도메인명}-{간략한 제목}-QA.md` 파일로 문서화한 후
  보고한다(코드 리뷰 결과는 9단계에서 이미 별도 파일로 작성했으므로 여기서는 QA 결과만
  다룬다 — 보고 시에는 두 파일을 같이 언급한다). 임의로 판단해 누락하지 않는다.
    - **Critical**: 데이터 유실, 보안/인증 우회
    - **High**: 핵심 플로우 실패
    - **Medium**: 예외 케이스 미처리, 환경 제약으로 검증 못 한 항목
    - **Low**: 사소한 개선점

## 12. 검토
- 정리한 보고 내용을 바탕으로 사용자에게 검토를 요청한다.

## 13. 수정 반영
- 사용자가 수정을 요청하면 반영하고, 요청이 없으면 다음 단계로 넘어간다.
- 이 수정이 엔드포인트 계약을 바꾸는 경우, 8단계의 "설계/정책 변경" 규칙에 따라 기획서도
  함께 갱신한다.

## 14. 작업 완료 보고
- 변경 사항 요약, 테스트 결과, 남은 이슈(있다면)를 간결하게 보고한다.

## 15. PR 생성
- 보고 후 사용자의 확인 절차를 거친 뒤에만 PR을 생성한다.
- 상세 규칙: [pr-template.md](./pr-template.md)
- 🔌 통합 브랜치가 default 브랜치가 아니라면([adapters/branch-strategy.md](../adapters/branch-strategy.md)
  참고) `closes #N`이 자동 종료되지 않으므로 관련 이슈를 직접 `gh issue close`로 닫는다.
- 🔌 PR 머지 후 외부 API 문서(Notion 등)를 갱신하는 팀이라면
  [adapters/external-doc-sync.md](../adapters/external-doc-sync.md) 참고.

---

## 🔌 훅 포인트 요약
이 워크플로우가 특정 도구를 가정하지 않고 열어둔 지점들이다. 프로젝트에 필요한 것만 골라
`docs/adapters/`에서 실제 도구로 채운다.

| 훅 포인트 | 기본 예시 어댑터 | 언제 필요한가 |
|---|---|---|
| 브랜치 전략 | [branch-strategy.md](../adapters/branch-strategy.md) | 항상 (하나는 정해야 한다) |
| 코드 스타일/린트 | [code-style.md](../adapters/code-style.md) | 항상 |
| 테스트 컨벤션 | [test-convention.md](../adapters/test-convention.md) | 항상 |
| DB 마이그레이션 버전 | [migration-convention.md](../adapters/migration-convention.md) | DB 스키마 변경이 있는 프로젝트 |
| 외부 API 문서 동기화 | [external-doc-sync.md](../adapters/external-doc-sync.md) | 선택 |
| API 클라이언트 컬렉션 | [api-client-collection.md](../adapters/api-client-collection.md) | 선택 |
| 자동 코드 리뷰 봇 | [review-bot.md](../adapters/review-bot.md) | 선택 |
