# Repository Guidelines

## Project Structure & Module Organization

This repository is an Agent Skills package for generating lint-clean `DESIGN.md` files from observed HTML or websites.

- `SKILL.md` is the canonical skill implementation and operating contract. The skills CLI symlinks this single file into every target agent's skills dir — edit once, never fork per-agent adapters.
- `README.md` explains install, usage, validation, and compatibility.
- `references/design-md-format.md` documents the target `@google/design.md` schema and lint rules.
- `references/EXAMPLE.md` is the primary lint-clean fixture and structure example.
- `agents/openai.yaml` contains Codex/OpenAI catalog metadata.

There is no compiled source tree or bundled asset pipeline; changes are primarily Markdown and YAML.

## Build, Test, and Development Commands

- `npx -y @google/design.md lint references/EXAMPLE.md` validates the bundled example fixture.
- `npx -y @google/design.md lint <output-dir>/DESIGN.md` validates generated design-system output.
- `git status --short` checks that only intentional files changed.

No local `npm install` or build step is required for normal edits.

## Coding Style & Naming Conventions

Use concise Markdown with sentence-case prose and clear imperative instructions. Keep examples copy-pasteable and prefer fenced code blocks for commands. YAML files use two-space indentation and quoted strings where existing metadata does. Preserve the skill's source-faithful language: do not add speculative design behavior or unsupported extraction capabilities.

## Testing Guidelines

The main regression check is the `@google/design.md` linter against `references/EXAMPLE.md` and any newly generated `DESIGN.md`. Treat lint errors as blocking. Warnings are acceptable only when they reflect source-faithful output and should be mentioned in review notes. If you change schema guidance, update `references/EXAMPLE.md` when needed so the documented pattern remains lint-clean.

## Commit & Pull Request Guidelines

Commits follow the [Conventional Commits](https://www.conventionalcommits.org/) specification. Each commit message uses this format:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

- **type** (required, lowercase) — one of:
  - `feat` — a new feature or skill capability
  - `fix` — a bug fix or lint/correctness fix
  - `docs` — documentation-only changes (`README.md`, `SKILL.md` guidance, `references/*`)
  - `style` — formatting, whitespace, or wording with no behavior change
  - `refactor` — code/structure change that neither fixes a bug nor adds a feature
  - `perf` — a change that improves performance
  - `test` — adding or correcting tests/fixtures
  - `build` — changes to build tooling or dependencies
  - `ci` — changes to CI/automation configuration
  - `chore` — routine maintenance (e.g. `.gitignore`, dependency bumps)
- **scope** (optional) — a short noun in parentheses naming the affected area, e.g. `docs(skill)`, `fix(example)`, `fix(design-md)`.
- **description** (required) — imperative mood, lowercase, no trailing period, focused on user-visible intent. For example: `feat: enrich extraction methodology + example coverage`, `fix: cross-CLI docs + design.md correctness`, `chore: drop redundant tests/sample.html fixture`.
- **Breaking changes** — append `!` after the type/scope (`feat!:`) and/or add a footer line starting with `BREAKING CHANGE:` describing the change and migration notes.
- **body** (optional) — wrap at ~72 columns; explain the *why*, not the *what*. Omit if the description is self-explanatory.
- **footer** (optional) — reference issues/tickets (`Closes #12`) or add metadata like `BREAKING CHANGE:`.

### Examples

```
feat(skill): add lint-clean scalar dark variant to EXAMPLE
```

```
fix(design-md): correct token naming across sections

Token names drifted from the @google/design.md schema in the
typography section. Re-aligned with references/design-md-format.md.

Closes #8
```

```
feat!: rename `tone` field to `mood` in extracted palette

BREAKING CHANGE: consumers must rename `tone` → `mood`.
```

Pull requests should describe the changed guidance, list validation commands run, and call out any remaining lint warnings or unverified paths. The PR title should itself be a valid Conventional Commit subject. Link related issues when available. Include before/after snippets only for substantial documentation or schema changes.
