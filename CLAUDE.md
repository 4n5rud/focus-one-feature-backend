# CLAUDE.md

이 프로젝트에서 작업할 때는 아래 문서를 반드시 참조한다.

## 핵심 워크플로우 (docs/rules) — 항상 적용
- 기능 구현/버그 수정 요청을 받으면 먼저 [docs/rules/branch-workflow.md](docs/rules/branch-workflow.md) 전체 순서를 그대로 따른다.
  (기존 코드 읽기 → 이슈 생성 → 기획서 작성 → 검토 → 브랜치 생성 → 구현 → 코드 리뷰 → QA/QC → 문제 보고 → 검토 → 완료 보고 → PR)
- 이슈 생성: [docs/rules/issue-template.md](docs/rules/issue-template.md)
- PR 생성: [docs/rules/pr-template.md](docs/rules/pr-template.md)
- 커밋 메시지: [docs/rules/commit-convention.md](docs/rules/commit-convention.md)
- API 디자인 원칙: [docs/rules/api-design.md](docs/rules/api-design.md) — 새 엔드포인트
  기획서 작성 시(branch-workflow.md 2단계) 반드시 검토
- 문장 다듬기(기획서/코드 주석): [docs/rules/sentence-refinement.md](docs/rules/sentence-refinement.md)
  — 기획서 본문, 코드 주석, PR 설명, 커밋 메시지를 작성할 때 반드시 따른다
  (branch-workflow.md 2단계 기획서 작성, 8단계 구현 시 주석 작성에 적용)
- 코드 리뷰 격리: [docs/rules/code-review-isolation.md](docs/rules/code-review-isolation.md) +
  [docs/rules/code-review-template.md](docs/rules/code-review-template.md)

## 도구 어댑터 (docs/adapters) — 프로젝트 스택에 맞게 교체
- 브랜치 전략: [docs/adapters/branch-strategy.md](docs/adapters/branch-strategy.md)
- 코드 스타일: [docs/adapters/code-style.md](docs/adapters/code-style.md)
- 테스트 컨벤션: [docs/adapters/test-convention.md](docs/adapters/test-convention.md)
- DB 마이그레이션: [docs/adapters/migration-convention.md](docs/adapters/migration-convention.md)
- 외부 API 문서 동기화(선택): [docs/adapters/external-doc-sync.md](docs/adapters/external-doc-sync.md)
- API 클라이언트 컬렉션(선택): [docs/adapters/api-client-collection.md](docs/adapters/api-client-collection.md)
- 자동 코드 리뷰 봇(선택): [docs/adapters/review-bot.md](docs/adapters/review-bot.md)

## Skills (docs/skills)
- 프로젝트 전용 작업 절차/스킬은 `docs/skills/`에 정리한다. (추가 예정)
