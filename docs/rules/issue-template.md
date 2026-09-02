# 이슈 템플릿 사용 규칙

- 신규 기능: [docs/templates/issue-feature.md](../templates/issue-feature.md)
- 버그: [docs/templates/issue-bug.md](../templates/issue-bug.md)
- GitHub의 "새 이슈" 템플릿 선택 화면에서 바로 쓰려면 위 두 파일을
  `.github/ISSUE_TEMPLATE/`에 복사해 둔다(frontmatter의 `name`/`about`/`labels`가 선택
  화면에 노출된다).
- `gh issue create --body-file`로 생성하고, 템플릿의 항목 구조는 임의로 삭제하지 않고
  내용만 채운다.
- ⚠️ **`--body-file`에 넘기는 파일에는 템플릿 맨 위 YAML frontmatter
  (`---\nname: ...\nabout: ...\nlabels: ...\n---`)를 포함하지 않는다.** 그 frontmatter는
  GitHub 웹 UI의 "이슈 템플릿 선택" 화면에서만 쓰이는 메타데이터라 `gh` CLI는 이해하지
  못하고, `--body-file`에 넣으면 그대로 이슈 본문에 텍스트로 찍혀버린다. 본문 파일에는
  `## 배경`/`## 재현 방법` 같은 실제 섹션만 담고, 라벨은 `--label` 플래그로 별도 지정한다.
- 제목은 팀의 공용어로, 어떤 기능/버그인지 바로 파악되도록 간결하게 작성한다.
- 라벨은 아래 조합으로 지정한다.
  - 종류: `feature` / `bug` / `docs` / `refactor` / `chore` / `test`
  - 도메인(해당 시): `domain:USER`처럼 프로젝트의 도메인명을 대문자로 쓴다. 없는 도메인이면
    `domain:{도메인명}` 형식으로 새로 만들어 사용한다.
  - 우선순위: `priority:high` / `priority:medium` / `priority:low`
- 담당/논의가 필요한 이슈는 `needs-discussion`, 다른 작업에 막힌 경우 `blocked` 라벨을
  추가로 지정한다.
