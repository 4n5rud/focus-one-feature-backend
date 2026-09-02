# PR 템플릿 사용 규칙

- [docs/templates/pull-request.md](../templates/pull-request.md)를 사용하고, 항목을
  임의로 삭제하지 않는다. GitHub의 PR 작성 화면에 기본 템플릿으로 띄우려면 이 파일을
  저장소 루트의 `.github/PULL_REQUEST_TEMPLATE.md`로 복사해 둔다.
- `관련 이슈`: `closes #{이슈번호}` 형식으로 명시한다.
  - 🔌 ⚠️ default 브랜치가 아닌 통합 브랜치로 feature PR을 머지하는 전략을 쓴다면
    ([adapters/branch-strategy.md](../adapters/branch-strategy.md) 참고), GitHub의
    "closes #N" 자동 종료는 **이 시점에 발동하지 않는다**(자동 종료는 default 브랜치로
    머지될 때만 동작한다). 승격 PR이나 이후 default 브랜치 PR에서 자동 종료되길 기대하지
    말고, feature PR을 통합 브랜치에 머지한 직후 관련 이슈를 `gh issue close {번호}`로
    직접 닫는다.
- `작업 내용` / `변경 사항 상세`: 기획서(`docs/domain/{도메인명}/{이슈번호}-{도메인명}-{간략한 제목}.md`)와
  실제 구현이 다른 부분이 있다면 반드시 명시한다.
- PR 설명 문장은 [sentence-refinement.md](./sentence-refinement.md)의 원칙 1~5를 따른다.
- `확인 사항` 체크리스트는 실제로 로컬에서 실행/확인한 항목만 체크한다.
  - 🔌 로컬 빌드/린트 통과 (명령어는 [adapters/code-style.md](../adapters/code-style.md) 참고)
  - CI(빌드/테스트/린트) 통과
  - (해당 시) 관련 도메인 라벨 지정
- PR은 [branch-workflow.md](./branch-workflow.md) 14~15단계(작업 완료 보고 → 사용자 확인)를
  거친 뒤에만 생성한다.
