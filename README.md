# html-to-design-md

> Turn any website, localhost page, or local HTML file into a lint-clean [`DESIGN.md`](https://github.com/google-labs-code/design.md) design system.

An [Agent Skills](https://agentskills.io) skill that inspects an observed UI, extracts real design tokens (colors, typography, spacing, radii, components), and writes a `DESIGN.md` that passes `@google/design.md lint`. The output is source-faithful — it captures what the UI actually uses rather than inventing an aesthetic.

## Install

```bash
npx skills add aaione/html-to-design-md            # default agent
npx skills add aaione/html-to-design-md -a codex   # target a specific agent
npx skills add aaione/html-to-design-md -g         # global install
```

One canonical `SKILL.md` works across 80+ agents. The [skills CLI](https://skills.sh) (`vercel-labs/skills`) symlinks it into each agent's skills directory (`.claude/skills/`, `.agents/skills/`, `.codex/skills/`, …) — no per-agent adapters needed. (`agents/openai.yaml` carries optional Codex catalog metadata.)

## Use

Invoke the skill with a source and an optional output directory:

```text
/html-to-design-md https://example.com ./design
/html-to-design-md ./site/index.html
```

It writes `DESIGN.md` (frontmatter tokens + rationale prose) into the output directory, then validates it:

```bash
npx -y @google/design.md lint ./design/DESIGN.md
```

## What it produces

A `DESIGN.md` with machine-readable frontmatter and eight rationale sections:

```yaml
---
version: "alpha"
name: Atlas Editorial
colors:
  primary: "#1A1C1E"
  accent: "#B8422E"
  background: "#F7F5F2"
typography:
  h1:
    fontFamily: Public Sans
    fontSize: 48px
    fontWeight: 600
    lineHeight: 1.1
rounded:
  sm: 4px
  md: 8px
spacing:
  sm: 8px
  md: 16px
components:
  button-primary:
    backgroundColor: "{colors.accent}"
    textColor: "#FFFFFF"
    rounded: "{rounded.sm}"
---

## Overview
...
```

See [`references/EXAMPLE.md`](references/EXAMPLE.md) for a complete, lint-clean sample covering all eight sections.

## How it works

1. **Inspect** the rendered page (browser tool when available) or raw HTML/CSS, preferring computed styles over screenshot sampling.
2. **Extract** exact token values from CSS variables, computed styles, framework classes, and inline styles. For dark/alternate themes, add separate scalar tokens (e.g. `surface-dark`) and note the mapping in prose — never use color arrays, the linter rejects them.
3. **Write** `DESIGN.md` following the canonical section order, mirroring `EXAMPLE.md`.
4. **Validate** with `@google/design.md lint` until it exits 0; only `broken-ref` is a hard error.

Full operational rules, edge cases (SPA shells, auth-gated pages, multi-page synthesis), and the token schema live in [`SKILL.md`](SKILL.md) and [`references/design-md-format.md`](references/design-md-format.md).

## Self-check

The bundled example doubles as a fixture:

```bash
npx -y @google/design.md lint references/EXAMPLE.md   # expect exit 0
```

## Compatibility

| Agent | Status | Adapter |
|:------|:-------|:--------|
| Claude Code | Supported | `SKILL.md` |
| Codex / OpenAI | Supported | `agents/openai.yaml` |
| Cursor, Cline, Copilot, Gemini, +80 more | Standard `SKILL.md` via skills CLI | — |

## License

MIT — see [LICENSE](LICENSE).
