# Universal Cursor Rules

Набор правил для Cursor, который можно использовать в любом проекте.

Это не “магия для нейросети”, а практичный каркас процесса:
- как выбирать нужные правила под тип задачи;
- какие проверки обязательны перед финальным ответом;
- как снизить риск регрессий и выдуманных решений.

## Зачем нужны эти правила

- чтобы ответы агента были предсказуемыми, а не “как повезёт”;
- чтобы код-ревью было быстрее (есть единый чеклист качества);
- чтобы меньше ломались API/контракты/миграции;
- чтобы слабые модели давали более стабильный результат за счёт процесса.

## Как это улучшает качество кода

- заставляет проверять изменения, а не просто “написать код”;
- вводит минимальный Definition of Done;
- разделяет low/medium/high риск и глубину проверок;
- заставляет фиксировать trade-offs и совместимость при архитектурных/API решениях.

## Что внутри (верхнеуровнево)

- `cursor-orchestrator.mdc` — точка входа, маршрут по правилам.
- `task-intent-routing.mdc` — классификация задачи (feature/bugfix/api/performance и т.д.) и какие правила включать.
- `definition-of-done-gates.mdc` — что обязательно должно быть сделано перед завершением.
- `risk-scoring-and-review-depth.mdc` — уровень риска и обязательная глубина проверки.
- `api-contract-gate.mdc`, `architecture-planning-gate.mdc`, `anti-hallucination-verification.mdc` — профильные guardrails.
- тематические правила: i18n, миграции, тесты, линтер, документация и др.

## Что важно понимать заранее

- Эти правила работают лучше всего, если:
  - в проекте есть профильные **Skills** под конкретный стек/домен;
  - проект хорошо покрыт актуальной документацией;
  - подключены нужные проекту **MCP** (где это требуется).
- Сами по себе правила не заменяют ни документацию, ни доменные навыки.

## Что такое оркестратор

`cursor-orchestrator.mdc` — это не одно “правило про всё”, а слой координации всего набора:
- определяет, какие правила читать первыми;
- связывает routing (`task-intent-routing`) и gate-правила;
- задаёт последовательность: контекст -> реализация -> проверки.

То есть оркестратор = комплекс правил + порядок их применения.

## What is included

- `rules/global/*.mdc` - global reusable rules
- `.github/workflows/path-safety.yml` - blocks machine-specific absolute paths
- `.github/workflows/route-integrity.yml` - validates routing references in `task-intent-routing.mdc`

## Installation

### Option A (recommended): install as personal global rules

This applies the rules to all your projects on the current machine.

#### Windows (PowerShell)

```powershell
$repoPath = "$env:USERPROFILE\universal_cursor_rules"
if (Test-Path $repoPath) {
  git -C $repoPath pull
} else {
  git clone https://github.com/alfarius42/universal_cursor_rules $repoPath
}
New-Item -ItemType Directory -Force "$env:USERPROFILE\.cursor\rules" | Out-Null
Copy-Item "$env:USERPROFILE\universal_cursor_rules\rules\global\*.mdc" "$env:USERPROFILE\.cursor\rules\" -Force
Get-ChildItem "$env:USERPROFILE\.cursor\rules" -Filter *.mdc | Select-Object -ExpandProperty Name
```

#### macOS/Linux (bash/zsh)

```bash
REPO_PATH="$HOME/universal_cursor_rules"
if [ -d "$REPO_PATH/.git" ]; then
  git -C "$REPO_PATH" pull
else
  git clone https://github.com/alfarius42/universal_cursor_rules "$REPO_PATH"
fi
mkdir -p "$HOME/.cursor/rules"
cp "$HOME/universal_cursor_rules/rules/global/"*.mdc "$HOME/.cursor/rules/"
ls -1 "$HOME/.cursor/rules/"*.mdc
```

Then reload Cursor window (or restart Cursor) so new rules are picked up.

### Option B: install per project

Use this if you want rules only in one repository.

#### Windows (PowerShell)

```powershell
New-Item -ItemType Directory -Force ".cursor\rules" | Out-Null
Copy-Item "$env:USERPROFILE\universal_cursor_rules\rules\global\*.mdc" ".cursor\rules\" -Force
Get-ChildItem ".cursor\rules" -Filter *.mdc | Select-Object -ExpandProperty Name
```

#### macOS/Linux (bash/zsh)

```bash
mkdir -p .cursor/rules
cp "$HOME/universal_cursor_rules/rules/global/"*.mdc .cursor/rules/
ls -1 .cursor/rules/*.mdc
```

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
- run one small task and verify final answer follows DoD blocks

### Smoke test after installation (1 minute)

1. Ask Cursor: `Classify this task intent: "Add new API endpoint for billing usage".`
   - Expected: intent `api` and references to `api-contract-gate.mdc` + related gates.
2. Ask Cursor: `Optimize a slow query for tender search with large n.`
   - Expected: reference to `algorithm-complexity-selection.mdc` and complexity/evidence checklist.
3. Ask Cursor: `Implement a small frontend feature with new UI text.`
   - Expected: i18n rule usage (`i18n-bilingual-features.mdc`) and DoD-style final structure.

### Update rules later

```bash
# run in cloned universal_cursor_rules folder
git pull
```

Then copy updated `.mdc` files again to your target rules directory.

### Uninstall / rollback

- remove copied `.mdc` files from:
  - global install: `~/.cursor/rules` (or `%USERPROFILE%\.cursor\rules`)
  - project install: `<repo>/.cursor/rules`
- reload Cursor

## Starter set for first adoption

Start with:

- `cursor-orchestrator.mdc`
- `task-intent-routing.mdc`
- `definition-of-done-gates.mdc`
- `risk-scoring-and-review-depth.mdc`
- `anti-hallucination-verification.mdc`

## Troubleshooting

### Rules are not applied after installation

- reload Cursor window or restart Cursor completely
- verify files exist in active rules location:
  - global: `~/.cursor/rules` (Windows: `%USERPROFILE%\.cursor\rules`)
  - project: `<repo>/.cursor/rules`

### Global and project rules conflict

- keep one source of truth per repository when possible
- if both are used, ensure project-local rules are intentionally overriding global behavior
- remove stale duplicates to avoid ambiguous guidance

### Wrong folder/path used during install

- confirm your shell expanded env variables correctly
- run file listing command after copy (`Get-ChildItem` / `ls`) and verify `.mdc` files are present

### CI fails on path safety check

- remove machine-specific absolute paths (`C:\...`, `/Users/...`, `%USERPROFILE%`)
- replace with repo-relative references (for example `./rules/global/...`)

### CI fails on route integrity check

- open `rules/global/task-intent-routing.mdc`
- ensure every referenced `.mdc` file exists in `rules/global/`
- fix typo/rename mismatches and re-run CI

### Broken/non-UTF8 text in rules

- save files as UTF-8
- avoid tooling that silently rewrites encoding during copy
- re-copy from source and re-apply path normalization if needed

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
