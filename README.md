# Focus One Feature — Backend

> AI한테 "이거 구현해줘" 던지고 풀바이브로 맡기기엔 아직 불안한 개발자를 위한 워크플로우
> 하네스입니다. 하나의 기능에만 집중해서, 기획서를 먼저 검토하고, 승인받은 범위 안에서만
> 구현하고, 코드 리뷰와 QA를 거쳐 PR까지 가는 흐름을 문서로 강제합니다.

## 왜 필요한가요?

AI 에이전트에게 기능 구현을 맡기면 종종 묻지도 않고 여러 파일을 한꺼번에 바꾸고, 스코프를
넘어선 리팩터링까지 손대고, 검증 없이 "완료했습니다"라고 보고합니다. 이 하네스는 그 과정에
사람이 반드시 끼어드는 지점(승인 게이트)을 명시적으로 박아 넣어서 다음을 강제합니다.

- 구현 전에 반드시 **기획서로 먼저 합의**한다 — 승인 없이 코드부터 짜지 않는다
- 항상 **이슈 1개 = 브랜치 1개 = 기능 1개**만 동시에 진행한다 — 여러 기능이 뒤섞이지 않는다
- 구현이 끝나면 **컨텍스트가 격리된 다른 에이전트**가 diff를 다시 본다 — 자기가 짠 코드를
  자기가 리뷰하지 않는다
- 진행 상태는 대화 기록이 아니라 **GitHub 이슈 체크리스트**로 관리한다 — 세션이 끊겨도
  이어갈 수 있다
- 기획과 실제 코드가 달라지면 **"구현 세부사항"인지 "설계 변경"인지부터 분류**한다 — 후자면
  구현을 멈추고 재승인부터 받는다

## 구조: core와 adapter

이 하네스는 두 겹으로 나뉩니다.

- **`docs/rules/` (core)** — 승인 게이트, 외부화된 진행 상태, 설계 변경 vs 구현 세부사항
  분리 같은 구조적 규칙. 어떤 스택을 쓰든 그대로 유지되는 부분입니다.
- **`docs/adapters/`** — 브랜치 전략, 코드 스타일, 테스트 프레임워크, DB 마이그레이션처럼
  프로젝트마다 다른 부분. 각 문서는 "이 훅 포인트에 무엇을 정의해야 하는가"를 설명하고, 그
  다음 예시 하나(대부분 Java/Gradle 기준)를 붙여 놓은 형태입니다. **실제로 쓸 때는 이
  예시를 지우고 프로젝트의 실제 도구로 바꿔 씁니다.**

`branch-workflow.md`(core) 본문에는 어댑터가 필요한 자리마다 🔌 표시를 해뒀고, 문서 맨
아래에 전체 훅 목록 표가 있습니다.

## 사용법

1. 이 폴더를 통째로 프로젝트 루트에 복사합니다 (또는 Claude Code 스킬로 등록합니다).
2. 프로젝트 루트에 이미 `CLAUDE.md`가 있다면, 여기 `CLAUDE.md`의 참조 목록을 병합합니다.
   없다면 그대로 씁니다.
3. `docs/adapters/*.md`를 하나씩 열어서 예시로 들어있는 도구(Java/Gradle/Checkstyle/
   JUnit5/Flyway)를 프로젝트의 실제 스택으로 교체합니다. 각 문서 맨 아래 "이 훅을 다른
   ○○로 교체할 때" 절에 무엇만 채우면 되는지 정리해뒀습니다.
4. `docs/templates/`의 이슈/PR 템플릿을 GitHub의 템플릿 선택 화면에도 띄우고 싶다면 각각
   `.github/ISSUE_TEMPLATE/`, `.github/PULL_REQUEST_TEMPLATE.md`로 복사해 둡니다(안 해도
   `gh issue create --body-file`로는 바로 쓸 수 있습니다).
5. 이제 "이 기능 구현해줘"라고 요청하면, 에이전트가 `docs/rules/branch-workflow.md`의
   0~15단계를 순서대로 따라갑니다.

## 폴더 구조

```
.
├── CLAUDE.md                        # 진입점 — core/adapter 문서를 참조하라는 지시
├── docs/
│   ├── rules/                       # core — 스택이 바뀌어도 유지되는 구조적 규칙
│   │   ├── branch-workflow.md       # 0~15단계 전체 워크플로우 (🔌 훅 포인트 표 포함)
│   │   ├── progress-tracking.md     # "한 번에 하나만" + 이슈 체크리스트 운영 규칙
│   │   ├── issue-template.md        # 이슈 생성 규칙
│   │   ├── pr-template.md           # PR 생성 규칙
│   │   ├── commit-convention.md     # 커밋 메시지 규칙
│   │   ├── api-design.md            # 엔드포인트 설계 6원칙
│   │   ├── sentence-refinement.md   # 기획서/주석/PR 설명 문장 다듬기 규칙
│   │   ├── code-review-isolation.md # 자체 코드 리뷰를 별도 에이전트에 맡기는 규칙
│   │   └── code-review-template.md  # 코드 리뷰 결과 문서 형식
│   ├── adapters/                    # 프로젝트 스택에 맞게 교체하는 부분 (예시 포함)
│   │   ├── branch-strategy.md       # 예: 3단계(main/staging/dev) + 트렁크 기반 대안
│   │   ├── code-style.md            # 예: Java + Google Java Style
│   │   ├── test-convention.md       # 예: JUnit5 + Mockito + AssertJ
│   │   ├── migration-convention.md  # 예: Flyway 타임스탬프 버전
│   │   ├── external-doc-sync.md     # 선택 — 예: Notion API 명세서
│   │   ├── api-client-collection.md # 선택 — 예: Postman 컬렉션 구조
│   │   └── review-bot.md            # 선택 — 예: CodeRabbit
│   ├── skills/                      # 프로젝트 전용 작업 절차를 쌓아두는 폴더
│   └── templates/
│       ├── issue-feature.md         # 기능 이슈 템플릿
│       ├── issue-bug.md             # 버그 이슈 템플릿
│       └── pull-request.md          # PR 템플릿
```

## 이 워크플로우가 가정하는 것 / 안 하는 것

- **가정**: Git + GitHub(이슈, PR, `gh` CLI). 브랜치 전략·빌드 도구는 가정하지 않고
  `docs/adapters/`의 훅으로 열어뒀습니다.
- **아직 안 함(프론트엔드)**: 이 버전은 백엔드(API) 기능 구현을 기준으로 씌어 있습니다.
  프론트엔드 전용 버전은 별도로 준비 중입니다.

## 🔌 이렇게 확장해보면 좋아요~!

핵심 하네스는 일부러 최소한으로 유지했고, `docs/adapters/`에 있는 선택 훅 3개는 팀 상황에
따라 켜고 끄면 됩니다. 보통 QA(10단계) 직후 또는 PR(15단계) 이후가 끼워 넣기 자연스러운
자리입니다.

- **API 문서 동기화** ([`external-doc-sync.md`](docs/adapters/external-doc-sync.md)) —
  Notion, Confluence 같은 곳에 팀 전체가 보는 API 명세서를 두고 있다면, PR 머지 후 갱신하는
  단계를 추가하세요. 머지 전에 미리 반영하면 아직 확정 안 된 스펙을 팀원이 보고 작업할
  위험이 있어서, 이 훅은 일부러 "머지 후"로 시점을 고정해뒀습니다.
- **API 클라이언트 컬렉션** ([`api-client-collection.md`](docs/adapters/api-client-collection.md)) —
  Postman/Insomnia 등으로 팀이 공유하는 요청 모음이 있다면, QA 때 실제로 검증한 요청을
  그대로 컬렉션에 옮겨 담는 단계를 QA 직후에 추가하세요. QA와 별도 작업이 아니라 QA의
  연장선으로 두면 중복 작업이 없습니다.
- **자동 코드 리뷰 봇** ([`review-bot.md`](docs/adapters/review-bot.md)) — core의 9단계
  (자체 코드 리뷰)는 컨텍스트가 격리된 에이전트가 diff를 다시 보는 것으로 이미 "리뷰를 한
  겹 더 한다"는 목적을 채웁니다. 여기에 CodeRabbit 같은 CI 연동형 리뷰 봇을 추가로 붙이면
  사람 리뷰 전에 한 겹 더 걸러낼 수 있습니다. 다만 무료 플랜은 계정 단위로 리뷰 횟수가
  제한되는 경우가 많으니, push마다 자동 실행되게 두면 금방 소진됩니다 — PR이 준비됐을 때만
  트리거하도록 설정을 좁히는 걸 권장합니다.

## 라이선스/출처

- `docs/rules/sentence-refinement.md`는 [toss/technical-writing](https://github.com/toss/technical-writing)의
  "문장 다듬기" 챕터를 재구성한 것입니다(CC BY-NC-SA 4.0, Viva Republica, Inc.).
- `docs/rules/api-design.md`의 6원칙은 [Slack Engineering: How We Design Our APIs at Slack](https://slack.engineering/how-we-design-our-apis-at-slack/)을
  참고했습니다.
