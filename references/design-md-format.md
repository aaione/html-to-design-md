# DESIGN.md Format Reference

Authoritative compact reference for generating or repairing `DESIGN.md` files validated by the `@google/design.md` CLI. Source of truth: the `@google/design.md` spec (`npx @google/design.md lint`).

## Structure

`DESIGN.md` has two layers:

1. YAML frontmatter (delimited by `---`) with machine-readable tokens.
2. Markdown body with human-readable design rationale.

```md
---
version: "alpha"
name: Example System
description: Short description of the visual identity.
colors:
  primary: "#1A1C1E"
  secondary: "#6C7278"
  background: "#F7F5F2"
typography:
  body-md:
    fontFamily: Inter
    fontSize: 1rem
    fontWeight: 400
    lineHeight: 1.5
    letterSpacing: 0em
rounded:
  sm: 4px
  md: 8px
spacing:
  sm: 8px
  md: 16px
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "#FFFFFF"
    typography: "{typography.body-md}"
    rounded: "{rounded.md}"
    padding: 12px
---

## Overview

...
```

## Token Schema

```yaml
version: <string>          # optional, current value "alpha"
name: <string>             # REQUIRED
description: <string>      # optional
colors:
  <token>: <Color>            # a single sRGB hex string, e.g. "#1A1C1E" (arrays are rejected)
typography:
  <token>:                  # keys are camelCase
    fontFamily: <string>
    fontSize: <Dimension>          # e.g. 16px, 1rem
    fontWeight: <number>           # e.g. 400
    lineHeight: <number>           # e.g. 1.6
    letterSpacing: <Dimension>     # e.g. -0.02em, 0.1em
rounded:
  <level>: <Dimension>            # sm/md/lg/full(9999px)
spacing:
  <level>: <Dimension | number>
components:
  <name>:
    <prop>: <string | token reference>   # backgroundColor textColor typography rounded padding size height width border-radius
```

### Token Rules

- **Colors** are single sRGB hex strings such as `"#1A1C1E"`. Do **not** use arrays (`surface: [light, dark]`) — the linter rejects them with `is not a valid color`. For dark/alternate themes, add separate scalar tokens (e.g. `surface-dark`) and describe the mapping in the Colors prose.
- **Dimensions** include units (`px`, `rem`, `em`).
- **Token references** use braces, e.g. `{colors.primary}`, `{typography.body-md}`, `{rounded.md}`, `{spacing.md}`. Unresolvable references are the one hard lint error.
- **Component variants** are separate entries, e.g. `button-primary-hover`.
- Every defined color token should be referenced by at least one component (unreferenced colors warn as orphaned).

## Section Order

Use `##` headings in this canonical order (out-of-order sections warn):

1. `Overview`
2. `Colors`
3. `Typography`
4. `Layout`
5. `Elevation & Depth`
6. `Shapes`
7. `Components`
8. `Do's and Don'ts`

## Validation

From the directory containing the file:

```bash
npx -y @google/design.md lint DESIGN.md          # non-interactive (use -y in agents/CI)
npx -y @google/design.md lint --format json DESIGN.md
```

Exit code is `1` only when errors are found, `0` otherwise.

### Lint Rules

Only `broken-ref` is a hard error. Everything else is a warning or info — resolve warnings when the choice is source-faithful; report any that remain.

| Rule | Severity | What it checks |
|:-----|:---------|:---------------|
| `broken-ref` | **error** | A token reference like `{colors.primary}` that resolves to no defined token |
| `missing-primary` | warning | Colors are defined but there is no `primary` color |
| `contrast-ratio` | warning | A component `backgroundColor`/`textColor` pair below WCAG AA (4.5:1) |
| `orphaned-tokens` | warning | A color token defined but never referenced by any component |
| `missing-typography` | warning | Colors are defined but no typography tokens exist |
| `section-order` | warning | Sections appear out of the canonical order above |
| `token-summary` | info | Per-section count of defined tokens |
| `missing-sections` | info | Optional sections (spacing, rounded) absent while other tokens exist |
| `unknown-key` | warning | A top-level YAML key that looks like a typo of a known key (e.g. `colours:` → `colors:`) |

Fix all errors before completion. Common real causes of failure: unresolvable token references, a missing `primary` color, low-contrast component pairs, out-of-order headings, and typo'd top-level keys (caught by `unknown-key`).
