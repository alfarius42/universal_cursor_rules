# Universal Cursor Rules (Community Edition)

Open, community-oriented rule pack for Cursor.  
This repository is designed for **any team or individual developer**, not a single internal team.

It provides reusable `.mdc` governance rules that help make AI-assisted coding more predictable, safer, and easier to review across different projects and models.

## Why this repository exists

- teams need a practical baseline for Cursor rules
- many private rule sets contain machine-specific paths and cannot be shared safely
- AI output quality improves when governance is explicit (routing, gates, risk checks)
- cross-project reuse is easier with globally-scoped, path-safe rules

## What is included

- `rules/global/*.mdc` - global reusable rules
- `.github/workflows/path-safety.yml` - blocks machine-specific absolute paths
- `.github/workflows/route-integrity.yml` - validates routing references in `task-intent-routing.mdc`

## Who should use this

- engineering teams adopting Cursor at scale
- solo developers who want consistent AI behavior
- tech leads/reviewers who want quality gates before merge
- OSS maintainers who need shared, portable governance files

## Core principles

- **portable**: no local machine paths
- **reviewable**: clear route/gate logic in plain text rules
- **safe by default**: anti-hallucination, contract checks, risk-aware depth
- **model-agnostic**: rules improve consistency across weaker and stronger models

## Quick start: install on your machine (Cursor)

### Option A: Use as personal global rules

1. Clone repository:
   - `git clone https://github.com/alfarius42/universal_ursor_rules`
2. Copy `rules/global/*.mdc` into your Cursor global rules folder:
   - Windows: `%USERPROFILE%\.cursor\rules\`
   - macOS/Linux: `~/.cursor/rules/`
3. Restart Cursor (or reload window).
4. Verify that files are visible in your global rules location.

### Option B: Use inside a specific project

1. Copy selected rules into `<your-repo>/.cursor/rules/`.
2. Keep all references repo-relative.
3. Start with:
   - `cursor-orchestrator.mdc`
   - `task-intent-routing.mdc`
   - `definition-of-done-gates.mdc`
   - `risk-scoring-and-review-depth.mdc`
   - `anti-hallucination-verification.mdc`

## Recommended rollout in a new team

1. Enable orchestrator + routing first.
2. Add DoD/risk/API gates.
3. Add performance gate (`algorithm-complexity-selection.mdc`) for backend/data tasks.
4. Review first 1-2 sprints and tune rule wording for your domain.

## CI validation in this repository

- **Path Safety Check**: fails on local machine absolute paths (`C:\...`, `/Users/...`, `%USERPROFILE%`, etc.).
- **Route Integrity Check**: fails when `task-intent-routing.mdc` references a missing `.mdc` rule.

## Path policy (important)

- use repo-relative paths where possible
- avoid user-specific absolute paths
- canonical complexity rule reference:
  - `./rules/global/algorithm-complexity-selection.mdc`

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
- compatible with CI checks in `.github/workflows/`
