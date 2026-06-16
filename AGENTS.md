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

Recent history follows Conventional Commit-style subjects, for example `feat: enrich extraction methodology + example coverage`, `fix: cross-CLI docs + design.md correctness`, and `chore: drop redundant tests/sample.html fixture`. Keep subjects short, lower-case by type, and focused on the user-visible intent.

Pull requests should describe the changed guidance, list validation commands run, and call out any remaining lint warnings or unverified paths. Link related issues when available. Include before/after snippets only for substantial documentation or schema changes.
