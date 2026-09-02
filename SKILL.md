---
name: focus-one-feature-backend
description: "기능 단위 승인 게이트 워크플로우 하네스(백엔드). 기능 구현/버그 수정 요청을 받으면 이슈 생성 → 기획서 작성/승인 → 브랜치 생성 → 구현 → 자체 코드 리뷰 → QA → PR까지 순서를 강제한다. \"이 기능 구현해줘\", \"버그 고쳐줘\" 같은 요청에 사용한다."
license: MIT
metadata:
  version: 1.0.0
  author: 4n5rud
  category: development
---

# Focus One Feature — Backend Workflow Harness

기능 구현/버그 수정 요청을 받으면 먼저 [docs/rules/branch-workflow.md](docs/rules/branch-workflow.md)
전체 순서(0~15단계)를 그대로 따른다. 순서를 건너뛰거나 임의로 합치지 않는다. 각 단계 사이의
승인/보고 지점에서는 사용자의 확인 없이 다음 단계로 넘어가지 않는다.

## 핵심 워크플로우 (docs/rules) — 항상 적용
- **이 프로젝트에서 처음 실행하는 경우**, 먼저 [docs/rules/project-setup.md](docs/rules/project-setup.md)에
  따라 구조화된 선택형 질문으로 어댑터를 확정받는다. 이미 채워져 있으면 건너뛴다.
- 전체 순서: [docs/rules/branch-workflow.md](docs/rules/branch-workflow.md)
  (기존 코드 읽기 → 이슈 생성 → 기획서 작성 → 검토 → 브랜치 생성 → 구현 → 코드 리뷰 →
  QA/QC → 문제 보고 → 검토 → 완료 보고 → PR)
- 진행 상태 추적("한 번에 하나만" + 이슈 체크리스트): [docs/rules/progress-tracking.md](docs/rules/progress-tracking.md)
- 이슈 생성: [docs/rules/issue-template.md](docs/rules/issue-template.md)
- PR 생성: [docs/rules/pr-template.md](docs/rules/pr-template.md)
- 커밋 메시지: [docs/rules/commit-convention.md](docs/rules/commit-convention.md)
- API 디자인 원칙: [docs/rules/api-design.md](docs/rules/api-design.md) — 새 엔드포인트
  기획서 작성 시(branch-workflow.md 2단계) 반드시 검토
- 문장 다듬기(기획서/코드 주석): [docs/rules/sentence-refinement.md](docs/rules/sentence-refinement.md)
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

## 이슈/PR 템플릿 (docs/templates)
- 기능 이슈: [docs/templates/issue-feature.md](docs/templates/issue-feature.md)
- 버그 이슈: [docs/templates/issue-bug.md](docs/templates/issue-bug.md)
- PR: [docs/templates/pull-request.md](docs/templates/pull-request.md)

자세한 설명과 사용법, core/adapter 구조는 [README.md](README.md) 참고.
