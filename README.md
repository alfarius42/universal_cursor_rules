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

Инструкция одна: **все файлы `*.mdc` из папки репозитория `rules/global/` скопировать в папку правил Cursor** (проводником, Finder или любым файловым менеджером — консоль не нужна).

1. **Получите копию репозитория:** на GitHub откройте https://github.com/alfarius42/universal_cursor_rules → **Code → Download ZIP**, распакуйте архив. Либо используйте уже клонированную папку — суть та же.
2. В распакованной (или клонированной) копии откройте каталог **`rules/global/`** — в нём лежат все правила (`*.mdc`).
3. **Выберите назначение:**
   - **Глобально** (все проекты на этом компьютере) — скопируйте все `.mdc` в папку глобальных правил Cursor:

   | Система | Путь назначения |
   |--------|------------------|
   | **Windows** | `C:\Users\<логин>\.cursor\rules\` — вставьте в адресную строку Проводника (подставьте свой логин Windows) и нажмите Enter. Папки `.cursor` и `rules` создайте при отсутствии. |
   | **macOS** | `~/.cursor/rules/` (Finder: **Переход → Переход к папке…** → `~/.cursor/rules`). |
   | **Linux** | `~/.cursor/rules/` |

   - **Только один репозиторий** — скопируйте те же файлы в **`<корень_проекта>/.cursor/rules/`** (создайте вложенные папки `.cursor` и `rules`, если их нет).

4. Перезапустите Cursor или **Developer: Reload Window**, чтобы подхватились файлы.

**Обновление правил:** снова скачайте свежий ZIP (или обновите клон) и перекопируйте содержимое `rules/global/` в ту же папку назначения, подтверждая замену файлов.

Если репозиторий уже лежит у вас в виде git-клона, достаточно после `git pull` снова скопировать `rules/global/*.mdc` в `~/.cursor/rules` или в `.cursor/rules` проекта — тем же способом, что в шагах выше.

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

### Uninstall / rollback

- remove copied `.mdc` files from:
  - global install: `~/.cursor/rules` (Windows: `C:\Users\<логин>\.cursor\rules`)
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
  - global: `~/.cursor/rules` (Windows: `C:\Users\<логин>\.cursor\rules`)
  - project: `<repo>/.cursor/rules`

### Global and project rules conflict

- keep one source of truth per repository when possible
- if both are used, ensure project-local rules are intentionally overriding global behavior
- remove stale duplicates to avoid ambiguous guidance

### Wrong folder/path used during install

- откройте целевую папку в Проводнике / Finder и убедитесь, что там лежат те же `.mdc`, что и в `rules/global/` (например `cursor-orchestrator.mdc`, `task-intent-routing.mdc`).

### CI fails on path safety check

- remove machine-specific absolute paths (`C:\...`, `/Users/...`, Windows USERPROFILE-style paths)
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

- **Path Safety Check**: fails on local machine absolute paths (`C:\...`, `/Users/...`, Windows USERPROFILE-style paths, etc.).
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
