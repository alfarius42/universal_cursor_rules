# Universal Cursor Rules Mirror

Public, reusable mirror of Cursor rule sets prepared for open-source sharing.

This repository is intended to make rule-based agent governance portable between teams and machines. It contains no application source code, only policy/rules in `.mdc` format.

## Who this is for

- Teams adopting Cursor rules and skills governance
- Developers who need a starter rules baseline
- Reviewers who want predictable quality gates for AI-assisted coding
- Maintainers who need a public-safe mirror of private/local rule sets

## What is mirrored

- Project rules: `rules/project/*.mdc`
- Global user rules: `rules/global/*.mdc`

## Why this exists

- Original rules often contain machine-specific absolute paths
- Private repos frequently mix local conventions with reusable policy
- Sharing a sanitized mirror helps onboard external contributors faster
- Stable rule references improve consistency across different AI models

## Repository structure

- `rules/project/` - project-scoped governance rules
- `rules/global/` - user/global rules reused across projects
- `.github/workflows/` - validation workflow for path safety

## Path safety policy

- Rules in this mirror must use repo-relative or generic paths
- Machine-specific absolute paths are not allowed, including:
  - Windows drive paths (`C:\...`, `D:\...`)
  - User-home absolute paths (`C:/Users/...`, `/Users/...`, `/home/...`)
  - `%USERPROFILE%`-based absolute references
- Global complexity rule should be referenced as:
  - `./rules/global/algorithm-complexity-selection.mdc`

## CI validation

GitHub Actions validates mirror safety on every push/PR:

- scans all `rules/**/*.mdc`
- fails if absolute local path patterns are detected
- prevents accidental reintroduction of private machine references

## How to use in another project

1. Copy required rules from `rules/project/` and `rules/global/`
2. Keep references repo-relative for your target repository
3. Add/update your orchestrator rule to point to local mirror paths
4. Keep gate rules (`DoD`, `risk`, `api-contract`, `anti-hallucination`) enabled by default

## Syncing from source repositories

When updating this mirror:

1. Pull latest source rules
2. Normalize paths to mirror-relative form
3. Run path-safety validation
4. Commit with a clear changelog-style message

## Scope and limitations

- This repository is policy-only; it does not include code implementation examples
- Some mirrored rules may reference docs/skills that exist only in source projects
- Consumers should adapt rule links to their own repository layout

## License and contribution notes

Treat this repository as shared governance content. If you contribute:

- keep wording clear for external teams
- avoid private/project-secrets in examples
- preserve path portability and CI pass status
