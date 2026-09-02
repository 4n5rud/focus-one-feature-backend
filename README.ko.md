# Focus One Feature — Backend

[English](README.md) | 한국어

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

## 시작하기

### 준비물
- GitHub 리모트가 있는 git 저장소, 그리고 인증된 [GitHub CLI](https://cli.github.com/)
  (`gh auth login`) — 워크플로우가 `gh issue create`, `gh issue close` 등을 직접 실행합니다.
- Claude Code.

### 1. 하네스를 프로젝트에 들여오기
아래 중 하나를 고릅니다.

- **Claude Code plugin으로 설치 (권장 — 프로젝트 저장소가 깔끔하게 유지됨)**:
  ```
  /plugin marketplace add 4n5rud/focus-one-feature-backend
  /plugin install focus-one-feature-backend@focus-one-feature-backend
  ```
- **개인 skill로 등록** (내 모든 프로젝트에서 쓰기, plugin 설치 없이): 이 저장소를
  `~/.claude/skills/focus-one-feature-backend/`에 클론합니다.
- **프로젝트에 직접 복사** (스킬/플러그인을 안 쓴 팀원도 저장소만 보고 규칙을 읽을 수 있게):
  ```
  git clone https://github.com/4n5rud/focus-one-feature-backend /tmp/focus-one-feature-backend
  cp -r /tmp/focus-one-feature-backend/docs /tmp/focus-one-feature-backend/CLAUDE.md <내-프로젝트>/
  ```
  `<내-프로젝트>/CLAUDE.md`가 이미 있다면 덮어쓰지 말고 참조 목록만 병합합니다.

### 2. 어댑터를 실제 스택으로 교체
직접 손으로 안 해도 됩니다: 이 프로젝트에서 **처음으로** 기능 구현을 요청하면, 에이전트가
어댑터에 아직 예시 내용이 남아있는 걸 확인하고 브랜치 전략/린터/테스트 프레임워크/
마이그레이션 도구/선택 어댑터 사용 여부를 구조화된 선택형 질문으로 먼저 물어봅니다
([`docs/rules/project-setup.md`](docs/rules/project-setup.md) 참고). 답변받은 내용으로
그 자리에서 어댑터 파일을 고쳐 쓰고, 이후로는 다시 묻지 않습니다.

미리 직접 채워두고 싶다면(예: 에이전트가 실행되기 전에 PR로 먼저 검토받고 싶은 경우)
`docs/adapters/`의 파일을 하나씩 열어서 예시 도구를 실제로 쓰는 도구로 바꿉니다.

| 파일 | 결정할 것 |
|---|---|
| `branch-strategy.md` | 3단계(`main`/`staging`/`dev`) vs 트렁크 기반 — 안 쓰는 예시 절은 지웁니다. |
| `code-style.md` | 실제 언어의 린터/스타일 가이드와 정확한 CI/커밋 전 체크 명령어. |
| `test-convention.md` | 실제 테스트 프레임워크와 정확한 테스트 명령어. |
| `migration-convention.md` | 실제 DB 마이그레이션 도구(DB가 없으면 이 파일 자체를 지웁니다). |
| `external-doc-sync.md`, `api-client-collection.md`, `review-bot.md` | 선택 — 안 쓰면 지웁니다. |

### 3. GitHub 초기 설정 (한 번만)
- `issue-template.md`가 쓰는 **라벨**을 미리 만들어둡니다:
  ```
  gh label create feature --color 0E8A16
  gh label create bug --color D73A4A
  gh label create "domain:USER" --color BFD4F2
  gh label create priority:high --color B60205
  gh label create priority:medium --color FBCA04
  gh label create priority:low --color C2E0C6
  gh label create needs-discussion --color D876E3
  gh label create blocked --color 000000
  ```
  (도메인이 늘어날 때마다 `domain:X` 라벨을 추가로 만들어줍니다.)
- **브랜치** — `branch-strategy.md`의 3단계 예시를 그대로 쓴다면:
  ```
  git checkout -b dev && git push -u origin dev
  git checkout -b staging && git push -u origin staging
  ```
  그다음 GitHub → Settings → Branches에서 `dev`/`staging`에 직접 push를 막는 보호 규칙을
  걸어둡니다(PR 필수). 트렁크 기반이면 default 브랜치 보호 규칙만 있으면 됩니다.
- **이슈/PR 템플릿**(선택) — GitHub 자체 템플릿 선택 화면에 띄우려면 복사해 둡니다:
  ```
  mkdir -p .github/ISSUE_TEMPLATE
  cp docs/templates/issue-feature.md .github/ISSUE_TEMPLATE/feature.md
  cp docs/templates/issue-bug.md .github/ISSUE_TEMPLATE/bug.md
  cp docs/templates/pull-request.md .github/PULL_REQUEST_TEMPLATE.md
  ```

### 4. 실제로 어떻게 명령하는가
그냥 평소처럼 기능을 설명하면 됩니다. 예:

> 이 기능 구현해줘: 사용자가 이메일로 비밀번호를 재설정할 수 있게 해줘

이후 에이전트가 `branch-workflow.md`의 단계를 알아서 밟아나가지만, **매 검토 지점마다
반드시 멈추고 사용자를 기다립니다**:

1. 기존 코드/컨벤션을 먼저 읽고, 0~15단계 체크리스트가 담긴 GitHub 이슈를 생성합니다
   (`gh issue list` / `gh issue view <번호>`로 확인 가능).
2. `docs/domain/.../` 아래에 기획서를 작성하고 검토를 요청합니다 — 사용자가 응답하기
   전에는 아무것도 구현하지 않습니다. 피드백을 주거나 "승인"이라고 답하면 다음 단계로
   넘어갑니다.
3. 승인 후에는 브랜치를 만들고, 승인된 범위 안에서만 구현하고, 격리된 에이전트가 자기
   diff를 리뷰하고, 기획서 기준으로 QA를 돌린 뒤 결과를 보고합니다 — PR을 만들기 전에
   다시 한번 확인을 받습니다.
4. 승인된 기획 범위를 벗어나는 변경(구현 세부사항이 아니라 진짜 설계 변경)이 필요해지면,
   조용히 진행하지 않고 멈춰서 재승인을 받으러 옵니다.

진행 상태는 대화가 아니라 **이슈 체크리스트**에 있습니다 — 세션이 끊겨도 다시 시작하면
이슈를 읽어서 어디까지 했는지 스스로 파악합니다.

### 전체 사이클 예시
```
"회원가입 시 이메일 인증 기능 추가해줘"
  → 이슈 #12 생성 (0~15단계 체크리스트)
  → docs/domain/user/12-user-email-verification.md 작성, 검토 요청
  → 사용자 승인
  → dev에서 feat/#12-email-verification 브랜치 생성
  → 구현 + 자체 리뷰 문서(…-code-review.md) + QA 문서(…-QA.md)
  → 사용자가 QA 보고 확인
  → dev로 PR 생성, 머지 후 이슈 #12 종료
```

별도의 "실행" 명령어는 없습니다 — 기능을 설명하기만 하면 나머지는 승인 게이트가 알아서
굴러갑니다.

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
│   │   ├── code-review-template.md  # 코드 리뷰 결과 문서 형식
│   │   └── project-setup.md         # 첫 실행 시 어댑터 확정 인터뷰 (구조화된 질문)
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
