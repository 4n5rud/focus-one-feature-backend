# Focus One Feature — Backend

English | [한국어](README.ko.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill%20%2F%20Plugin-5A45FF)](SKILL.md)
[![Scope](https://img.shields.io/badge/Scope-Backend-blue)](#what-this-workflow-assumes--doesnt-assume)

> A workflow harness for developers who are still uneasy handing a feature over to full
> "vibe coding". It forces a discipline: focus on one feature at a time, get the plan
> reviewed before writing code, implement only within the approved scope, and go through
> code review and QA before opening a PR.

## Contents
- [Why this exists](#why-this-exists)
- [Why it's lightweight](#why-its-lightweight)
- [Structure: core vs. adapter](#structure-core-vs-adapter)
- [Getting Started](#getting-started)
- [Screenshot & demo](#screenshot--demo)
- [Folder structure](#folder-structure)
- [What this workflow assumes / doesn't assume](#what-this-workflow-assumes--doesnt-assume)
- [🔌 Extending it further](#-extending-it-further)
- [License / credits](#license--credits)

## Why this exists

Left alone, an AI agent implementing a feature will often change several files at once
without asking, drift into refactors outside the requested scope, and report "done"
without real verification. This harness inserts explicit human checkpoints (approval
gates) into that process and enforces:

- **Agree on a plan before writing code** — no implementation starts without approval
- Always work on **exactly one issue = one branch = one feature** at a time — features
  never get mixed together
- Once implementation is done, **a separate, context-isolated agent** reviews the diff —
  nobody reviews their own code
- Progress is tracked in a **GitHub issue checklist**, not in conversation history — work
  can resume even after a session is lost
- When the plan and the actual code diverge, **classify the change first**: is it an
  "implementation detail" or a "design change"? The latter halts implementation until
  re-approved

## Why it's lightweight

A lot of skill/plugin marketplaces bundle hundreds of skills, Python tools, and
reference docs into one install, whether or not you'll ever touch most of them — that's
context sitting in your project from day one. This harness is the opposite: it's built
to be read piece by piece, not loaded all at once.

- `SKILL.md`, the entry point, is **~50 lines**. It only points at files — it doesn't
  try to inline the whole workflow.
- The always-on core (`docs/rules/`) is **~700 lines across 10 files**. Each step of
  `branch-workflow.md` links to exactly one rule file when it's relevant, instead of
  dumping every rule into context up front.
- Adapters you don't use don't just sit there unused — the [first-run interview](docs/rules/project-setup.md)
  **deletes** the optional adapter files (external doc sync, API client collection,
  review bot) you say you don't need. What's left in your repo is only what you
  actually use.
- No bundled scripts, no reference-doc library, no persona catalog — just markdown
  rules a coding agent reads on demand.

## Structure: core vs. adapter

This harness has two layers.

- **`docs/rules/` (core)** — structural rules: approval gates, externalized progress
  state, the design-change-vs-implementation-detail split. These hold regardless of tech
  stack.
- **`docs/adapters/`** — the parts that differ per project: branch strategy, code style,
  test framework, DB migrations. Each document explains "what this hook point needs to
  define," followed by one worked example (mostly Java/Gradle). **When you actually use
  this, delete the example and replace it with your project's real tooling.**

`branch-workflow.md` (core) marks every spot that needs an adapter with 🔌, and the
bottom of that document has a full table of hook points.

## Getting Started

### Prerequisites
- A git repo with a GitHub remote, and the [GitHub CLI](https://cli.github.com/) (`gh`)
  authenticated (`gh auth login`) — the workflow drives `gh issue create`,
  `gh issue close`, etc.
- Claude Code.

### 1. Get the harness into your project
Pick one:

- **As a Claude Code plugin (recommended, keeps your repo clean):**
  ```
  /plugin marketplace add 4n5rud/focus-one-feature-backend
  /plugin install focus-one-feature-backend@focus-one-feature-backend
  ```
- **As a personal skill** (all your projects, no plugin install): clone this repo into
  `~/.claude/skills/focus-one-feature-backend/`.
- **Copied straight into the project** (so teammates without the skill/plugin installed
  can still read the rules from the repo itself):
  ```
  git clone https://github.com/4n5rud/focus-one-feature-backend /tmp/focus-one-feature-backend
  cp -r /tmp/focus-one-feature-backend/docs /tmp/focus-one-feature-backend/CLAUDE.md <your-project>/
  ```
  If `<your-project>/CLAUDE.md` already exists, merge in the reference list instead of
  overwriting it.

### 2. Adapt the adapters to your real stack
You don't have to do this by hand: the **first time** you ask the agent to implement
anything in this project, it notices the adapters still hold example content and
interviews you with structured multiple-choice questions (branch strategy, linter, test
framework, migration tool, which optional adapters to keep) before doing anything else —
see [`docs/rules/project-setup.md`](docs/rules/project-setup.md). It rewrites the
adapter files with your answers on the spot, and won't ask again afterward.

If you'd rather pre-fill them yourself (e.g. to review in a PR before the agent ever
runs), open every file in `docs/adapters/` and replace the example tooling directly:

| File | Decide |
|---|---|
| `branch-strategy.md` | 3-tier (`main`/`staging`/`dev`) or trunk-based? Delete whichever example section you don't use. |
| `code-style.md` | Your language's linter/style guide and the exact CI/pre-commit command. |
| `test-convention.md` | Your test framework and the exact test command. |
| `migration-convention.md` | Your DB migration tool (or delete this file entirely if there's no DB). |
| `external-doc-sync.md`, `api-client-collection.md`, `review-bot.md` | Optional — delete any you don't use. |

### 3. One-time GitHub setup
- **Labels** used by `issue-template.md` — create them once:
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
  (Add one more `domain:X` label per part of your codebase as they come up.)
- **Branches** — if you're keeping the 3-tier example from `branch-strategy.md`:
  ```
  git checkout -b dev && git push -u origin dev
  git checkout -b staging && git push -u origin staging
  ```
  Then, in GitHub → Settings → Branches, add protection rules for `dev` and `staging`
  that block direct pushes (require a PR). If you went trunk-based instead, you only
  need the usual protection on your default branch.
- **Issue/PR templates** (optional) — copy them so they show up in GitHub's own template
  picker:
  ```
  mkdir -p .github/ISSUE_TEMPLATE
  cp docs/templates/issue-feature.md .github/ISSUE_TEMPLATE/feature.md
  cp docs/templates/issue-bug.md .github/ISSUE_TEMPLATE/bug.md
  cp docs/templates/pull-request.md .github/PULL_REQUEST_TEMPLATE.md
  ```

### 4. How you actually talk to it
Just describe the feature in plain language, e.g.:

> Implement password reset via email for signed-up users.

From there the agent drives itself through `branch-workflow.md`'s steps, but **stops and
waits for you** at every review gate:

1. Reads existing code/conventions, then opens a GitHub issue with the 0–15 step
   checklist (watch it with `gh issue list` / `gh issue view <n>`).
2. Writes a plan document under `docs/domain/.../` and asks you to review it — nothing
   gets implemented until you respond. Reply with feedback, or "approved" to move on.
3. Once approved, it branches, implements strictly within the approved scope, has an
   isolated agent review its own diff, runs QA against the plan, and reports results —
   asking for confirmation again before opening the PR.
4. If it ever needs to change something outside the approved plan (a real design change,
   not just an implementation detail), it stops and comes back to you for re-approval
   instead of quietly proceeding.

Progress lives in the **issue's checklist**, not in the chat — if a session gets
interrupted, just resume and it re-reads the issue to see where it left off.

### A full cycle, concretely
```
"Add email verification to sign-up"
  → issue #12 opened (0–15 checklist)
  → docs/domain/user/12-user-email-verification.md written, review requested
  → you approve
  → branch feat/#12-email-verification created off dev
  → implementation + self-review doc (…-code-review.md) + QA doc (…-QA.md)
  → you review the QA report
  → PR opened against dev, issue #12 closed after merge
```

There's no separate "trigger" command — just describe the feature and let the gates do
their job.

## Screenshot & demo

<!--
Drop files into docs/assets/ with these exact names and they'll show up here with no
other edits needed — see docs/assets/README.md for details:
  - docs/assets/screenshot.png  → a single screenshot (e.g. an approval-gate prompt)
  - docs/assets/demo.gif        → a short, silent, looping demo (GIFs are what actually
                                    autoplay inline in GitHub's renderer; an mp4 embedded
                                    the same way will not)

For a longer walkthrough video, you have two options instead of/alongside the GIF:
  1. Record it, then drag-and-drop the video file into README.md while editing it in
     GitHub's web UI. GitHub hosts it and rewrites it into a
     `https://github.com/<owner>/<repo>/assets/...` URL you can drop straight into this
     file — GitHub renders that as an inline player automatically.
  2. Upload it to YouTube and use a clickable thumbnail instead:
     [![Watch the demo](docs/assets/thumbnail.png)](https://youtu.be/VIDEO_ID)
-->

![Approval-gate walkthrough screenshot](docs/assets/screenshot.png)

![Demo](docs/assets/demo.gif)

## Folder structure

```
.
├── CLAUDE.md                        # entry point — points to the core/adapter docs
├── SKILL.md                         # entry point for Claude Code skill/plugin use
├── LICENSE                          # MIT
├── docs/
│   ├── rules/                       # core — structural rules that survive a stack change
│   │   ├── branch-workflow.md       # the full 0–15 step workflow (includes the 🔌 hook table)
│   │   ├── progress-tracking.md     # "one at a time" + issue-checklist operating rules
│   │   ├── issue-template.md        # how to file issues
│   │   ├── pr-template.md           # how to open PRs
│   │   ├── commit-convention.md     # commit message rules
│   │   ├── api-design.md            # 6 endpoint design principles
│   │   ├── sentence-refinement.md   # writing rules for plans/comments/PR descriptions
│   │   ├── code-review-isolation.md # why/how self-review runs in an isolated agent
│   │   ├── code-review-template.md  # code review findings document format
│   │   └── project-setup.md         # first-run adapter interview (structured questions)
│   ├── adapters/                    # swap these for your project's actual stack (examples included)
│   │   ├── branch-strategy.md       # e.g. 3-tier (main/staging/dev) + trunk-based alternative
│   │   ├── code-style.md            # e.g. Java + Google Java Style
│   │   ├── test-convention.md       # e.g. JUnit5 + Mockito + AssertJ
│   │   ├── migration-convention.md  # e.g. Flyway timestamp versioning
│   │   ├── external-doc-sync.md     # optional — e.g. Notion API spec
│   │   ├── api-client-collection.md # optional — e.g. Postman collection structure
│   │   └── review-bot.md            # optional — e.g. CodeRabbit
│   ├── skills/                      # where project-specific procedures accumulate
│   ├── templates/
│   │   ├── issue-feature.md         # feature issue template
│   │   ├── issue-bug.md             # bug issue template
│   │   └── pull-request.md          # PR template
│   └── assets/                      # screenshot.png / demo.gif / thumbnail.png go here
```

## What this workflow assumes / doesn't assume

- **Assumes**: Git + GitHub (issues, PRs, the `gh` CLI). Branch strategy and build
  tooling are not assumed — they're left open as hooks under `docs/adapters/`.
- **Not yet (frontend)**: this version is written around backend (API) feature work. A
  frontend-specific version is planned separately.

## 🔌 Extending it further

The core harness is deliberately kept minimal. The three optional hooks under
`docs/adapters/` can be turned on or off per team. They usually fit naturally right
after QA (step 10) or after the PR (step 15).

- **API doc sync** ([`external-doc-sync.md`](docs/adapters/external-doc-sync.md)) — if
  your team keeps a shared API spec somewhere like Notion or Confluence, add a step to
  update it after the PR merges. This hook deliberately fixes the timing to "after
  merge," since updating it earlier risks teammates working off a spec that PR review
  might still change.
- **API client collection** ([`api-client-collection.md`](docs/adapters/api-client-collection.md)) —
  if your team shares a request collection via Postman/Insomnia, add a step right after
  QA to move the requests you already verified into the collection. Treating it as an
  extension of QA rather than separate work avoids duplicate effort.
- **Automated code review bot** ([`review-bot.md`](docs/adapters/review-bot.md)) — core
  step 9 (self-review) already covers "have something else look at the diff again" by
  using a context-isolated agent. Adding a CI-integrated review bot like CodeRabbit on
  top gives you one more filter before human review. Free tiers are often rate-limited
  per account, though — leaving it to auto-run on every push can burn through your quota
  fast, so it's worth narrowing the trigger to only run once a PR is ready.

## License / credits

MIT — see [LICENSE](LICENSE).

- `docs/rules/sentence-refinement.md` is adapted from the "Sentence Refinement" chapter
  of [toss/technical-writing](https://github.com/toss/technical-writing)
  (CC BY-NC-SA 4.0, Viva Republica, Inc.).
- The 6 principles in `docs/rules/api-design.md` are based on
  [Slack Engineering: How We Design Our APIs at Slack](https://slack.engineering/how-we-design-our-apis-at-slack/).
