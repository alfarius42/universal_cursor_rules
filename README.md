# Universal Cursor Rules

A pack of Cursor rules you can use in any project.

This is a practical process skeleton:
- how to pick the right rules for the task type;
- which checks must happen before a final answer;
- how to reduce regressions and made-up solutions.

## Why these rules

- more predictable agent answers, not “whatever works today”;
- faster code review (shared quality checklist);
- fewer broken APIs, contracts, and migrations;
- weaker models still behave more consistently thanks to process.

## How this improves code quality

- encourages reviewing changes, not only “shipping code”;
- introduces a minimal Definition of Done;
- separates low/medium/high risk and depth of review;
- forces trade-offs and compatibility to be stated for architecture/API work.

## Contents (overview)

- `cursor-orchestrator.mdc` — entry point and rule routing.
- `task-intent-routing.mdc` — task classification (feature/bugfix/api/performance, etc.) and which rules to load.
- `definition-of-done-gates.mdc` — what must be done before finishing.
- `risk-scoring-and-review-depth.mdc` — risk level and required review depth.
- `api-contract-gate.mdc`, `architecture-planning-gate.mdc`, `anti-hallucination-verification.mdc` — focused guardrails.
- topic rules: i18n, migrations, tests, linter, documentation, and more.

## What to know upfront

These rules work best when:
- the project has **Skills** tailored to your stack/domain;
- the project is covered by up-to-date documentation;
- required **MCP** integrations are enabled where needed.

Rules alone do not replace documentation or domain expertise.

## What the orchestrator is

`cursor-orchestrator.mdc` is not a single “rule for everything”; it coordinates the whole set:
- which rules to read first;
- how routing (`task-intent-routing`) connects to gate rules;
- order: context → implementation → checks.

So: orchestrator = the rule set + order of application.

## Context budget (`alwaysApply`)

To reduce noise in every chat, the pack keeps a **portable eight-rule core** on `alwaysApply: true` (see `cursor-orchestrator.mdc`): orchestrator, bootstrap, intent routing, token economy, documentation index, primary source, tech stack, communication. **Everything else** is loaded by intent via `@.cursor/rules/…` and `task-intent-routing.mdc`.

Teams that need heavier always-on policy (e.g. concrete doc paths, CI commands) add **`*.local.mdc`** files in `<repo>/.cursor/rules/` and may turn off overlapping globals in that repo. **Executable source of truth** for a task is always the repo’s `.cursor/rules/`; copies under Cursor User rules are a **template** for syncing, not the live contract (see `project-rules-bootstrap.mdc`).

An example of a repo with full local overrides (paths, CI, high-risk machine-check) is [AI_Tender_Tool_Development](https://github.com/alfarius42/AI_Tender_Tool_Development) (optional reference, not required for this pack).

## What is included

- **`rules/global/*.mdc`** — reusable global rules. For installation on your machine **you only need these files** (see [Installation](#installation)).

- **`.github/workflows/`** — **GitHub only**: when you push to this repo, workflows run quality checks on the rules. **Do not copy or configure this for local Cursor setup** — it does not affect the agent on your computer.

## Installation

Single idea: **copy all `*.mdc` files from `rules/global/` in this repo into Cursor’s rules folder** (File Explorer, Finder, or any file manager — no terminal required).

1. **Get a copy of the repo:** open https://github.com/alfarius42/universal_cursor_rules → **Code → Download ZIP** and extract it, or use an existing clone.
2. In that copy, open **`rules/global/`** — all rule files (`*.mdc`) are there.
3. **Choose the destination:**
   - **Globally** (all projects on this machine) — copy every `.mdc` into Cursor’s global rules folder:

   | OS | Destination path |
   |----|------------------|
   | **Windows** | `C:\Users\<username>\.cursor\rules\` — paste into Explorer’s address bar (use your Windows username) and press Enter. Create `.cursor` and `rules` if missing. |
   | **macOS** | `~/.cursor/rules/` (Finder: **Go → Go to Folder…** → `~/.cursor/rules`). |
   | **Linux** | `~/.cursor/rules/` |

   - **Single project only** — copy the same files to **`<project_root>/.cursor/rules/`** (create `.cursor` and `rules` if needed).

4. Restart Cursor or run **Developer: Reload Window** so the files load.

**Updating rules:** download a fresh ZIP (or `git pull` your clone) and copy `rules/global/` again to the same destination, replacing files when prompted.

If you already use a git clone, after `git pull` copy `rules/global/*.mdc` to `~/.cursor/rules` or the project’s `.cursor/rules` the same way as above.

### Verify installation

Minimum expected files:

- `cursor-orchestrator.mdc`
- `task-intent-routing.mdc`
- `definition-of-done-gates.mdc`
- `risk-scoring-and-review-depth.mdc`
- `anti-hallucination-verification.mdc`

Quick check:

- ask Cursor to classify a task intent (`feature`, `bugfix`, `api`, etc.)
- confirm it references rules from `task-intent-routing.mdc`
- run one small task and verify the final answer follows DoD blocks

### Smoke test after installation (1 minute)

1. Ask Cursor: `Classify this task intent: "Add new API endpoint for billing usage".`
   - Expected: intent `api` and references to `api-contract-gate.mdc` + related gates.
2. Ask Cursor: `Optimize a slow query for tender search with large n.`
   - Expected: reference to `algorithm-complexity-selection.mdc` and complexity/evidence checklist.
3. Ask Cursor: `Implement a small frontend feature with new UI text.`
   - Expected: i18n rule usage (`i18n-bilingual-features.mdc`) and DoD-style final structure.

### Uninstall / rollback

- remove copied `.mdc` files from:
  - global install: `~/.cursor/rules` (Windows: `C:\Users\<username>\.cursor\rules`)
  - project install: `<repo>/.cursor/rules`
- reload Cursor

## Starter set for first adoption

Minimum process skeleton (already part of the eight-rule core unless you trim `alwaysApply`):

- `cursor-orchestrator.mdc`
- `task-intent-routing.mdc`
- `definition-of-done-gates.mdc` (via `@` when finishing work)
- `risk-scoring-and-review-depth.mdc`
- `anti-hallucination-verification.mdc`

## Troubleshooting

### Rules are not applied after installation

- reload the Cursor window or restart Cursor completely
- verify files exist in the active rules location:
  - global: `~/.cursor/rules` (Windows: `C:\Users\<username>\.cursor\rules`)
  - project: `<repo>/.cursor/rules`

### Global and project rules conflict

- keep one source of truth per repository when possible
- if both are used, ensure project-local rules intentionally override global behavior
- remove stale duplicates to avoid ambiguous guidance

### Wrong folder or path during install

- open the destination folder in Explorer / Finder and confirm it contains the same `.mdc` files as `rules/global/` (e.g. `cursor-orchestrator.mdc`, `task-intent-routing.mdc`).

## Recommended rollout in a new team

1. Enable orchestrator + routing first.
2. Add DoD/risk/API gates.
3. Add performance gate (`algorithm-complexity-selection.mdc`) for backend/data tasks.
4. Review the first 1–2 sprints and tune rule wording for your domain.

## Limitations

- this repository contains governance rules, not implementation code
- some rules may reference docs/skills patterns that need adaptation in your repo
- teams should localize wording to their workflow, language, and compliance constraints

## Contributing

Contributions are welcome from the community.

Please keep changes:

- portable across machines and OS
- clear for external users (not only original authors)
- free from secrets and private infrastructure references
- in `rules/global/*.mdc`, use repo-relative paths (no absolute paths to your machine); pushes are checked by `.github/workflows/`
