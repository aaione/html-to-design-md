---
name: html-to-design-md
description: >-
  Generate a lint-clean DESIGN.md design-system spec from an observed web UI:
  website URL, localhost page, saved HTML, or rendered HTML/CSS. Use when the
  user wants @google/design.md frontmatter tokens plus design rationale
  extracted from source-faithful evidence, including colors, typography,
  spacing, radii, components, states, and usage rules.
argument-hint: <url-or-html-path> [output-dir]
allowed-tools: Read, Write, Bash(npx -y @google/design.md lint *)
---

# HTML To DESIGN.md

## Overview

Turn a live website, localhost page, or local `.html` file into a `DESIGN.md` that follows the `@google/design.md` format: YAML frontmatter with machine-readable tokens plus Markdown rationale. Preserve observed source truth; do not invent aesthetics.

Keep `references/design-md-format.md` open for the token schema, canonical section order, and the authoritative lint-rule table. Mirror `references/EXAMPLE.md` for a complete, lint-clean structure.

## Workflow

1. **Resolve inputs.** The source URL/localhost/local HTML and the optional output dir come from `$ARGUMENTS` (`<url-or-html-path> [output-dir]`). If `$ARGUMENTS` is empty, ask the user for a URL/path. Always write to `<output-dir>/DESIGN.md` (default: current working directory).

2. **Collect evidence.** Use the strongest inspection path available for the input:

   | Input | Required evidence path | Stop condition |
   |:--|:--|:--|
   | Local `.html` | Read the file, inline styles, and linked local CSS/assets that are accessible from the file path. | Enough CSS/markup exists to identify real tokens and components. |
   | Website URL | Use a browser/rendering or fetch tool when available; inspect HTML, CSS, computed styles, and screenshots if rendered. | The target page, not an interstitial/login, is observable. |
   | Localhost | Render with the active browser/tool if the server is already running; otherwise use reachable HTML/CSS only. | The page resolves with meaningful DOM and styles. |
   | SPA shell | Require rendered DOM/computed styles. | Halt if only an empty mount node is available and no browser/rendering tool exists. |
   | Multi-page input | Preserve input order, extract shared tokens first, then record page-specific variants. | Shared system plus named deviations are documented. |

3. **Inspect the design.** Capture token evidence — colors, typography, spacing scale, radii, shadows, component states, layout density, responsive behavior, viewport behavior, and domain-specific components. Inspect source and computed styles before relying on screenshot inference; mark any inferred value in prose, never in a token name. Prefer intent tokens (theme / CSS-variable files) over computed styles over screenshots — see *Extraction Source Priority* and *Reverse-engineering from Frameworks* in the reference.

4. **Extract tokens.** Pull exact values from CSS variables, computed styles, framework classes, or inline styles. Emit at minimum `name`, `colors` (including a `primary`), `typography`, `rounded`, `spacing`, and the observable `components`. Group colors by role (primary/accent/surface/`on-*`/functional states like `success`/`error`), deduplicate near-duplicates, and reference every color from a component to avoid orphaned-token warnings. Always emit 1-2 domain-specific components beyond generic primitives. For a dark/alternate theme, add separate scalar tokens (e.g. `surface-dark: "#1A1C1E"`) and document the mapping in the Colors section — color arrays (`[light, dark]`) are rejected by the current linter.

5. **Write `DESIGN.md`.** Frontmatter delimited by `---`; exact sRGB hex colors; dimensions with units; component tokens reference tokens like `{colors.primary}`. Follow the canonical section order (see the reference). Prose should state *why* tokens exist and *how* future UI applies them. Mirror `references/EXAMPLE.md`.

6. **Validate (blocking).** From the output directory run, and treat any error as blocking — fix and rerun until exit 0:

```bash
npx -y @google/design.md lint <output-dir>/DESIGN.md
```

Only `broken-ref` is a hard error (exit 1); the rest are warnings/info. Warnings (e.g. contrast-ratio, orphaned-tokens) are allowed only when source-faithful — mention remaining ones in the final response. The validator is unpinned (`@google/design.md`), so a pass is authoritative only for the currently-resolved version. If the environment cannot run the CLI (no network, registry blocked, etc.), report the exact failure and do a manual schema pass against the reference instead of claiming success.

## Capabilities & Fallbacks

- **Browser tool.** Rendered-page inspection needs a browser/Playwright-style tool. If none is available, fall back deterministically: (1) fetch raw HTML, (2) read inline + linked CSS, (3) mark all computed-style-dependent tokens as inferred. Never fabricate values a browser would have shown.
- **JS-rendered / SPA pages.** If fetched HTML is an empty shell (mount node only), require a real browser to render; if unavailable, halt with a clear report rather than emit an empty DESIGN.md. Re-render the same source the same way so re-runs stay reproducible.
- **Auth-gated pages.** If the page resolves to a login/consent/interstitial, ask the user to authenticate in the active session first. Never treat login chrome as the site's design system.
- **Multiple pages.** Synthesize shared tokens first; the first page wins for conflicting base tokens; record deviations as named variants under Components. Preserve input order across re-runs.

## Extraction Checklist

- **Identity:** product/site name, domain, page role, and any declared brand/theme metadata.
- **Colors:** semantic roles, `on-*` text pairs, functional states, hover/focus fills, near-duplicate consolidation, and contrast-sensitive pairs.
- **Typography:** font families, display/body/label scale, weights, line heights, letter spacing, and where each style appears.
- **Spacing & layout:** base unit, section rhythm, container widths, gutters, breakpoints, alignment patterns, and touch target sizing when visible.
- **Shapes & depth:** radius scale, borders, shadows, backdrop/blur behavior, elevation rules, and flat-vs-layered strategy.
- **Components:** buttons, cards/containers, navigation, inputs/forms when present, feedback/status elements, and 1-2 domain-specific components.
- **States:** default, hover, active, disabled, focus, selected, loading, and error states when observable; write unsupported states in prose, not unsupported component keys.

## Quality Gate

- The frontmatter has `name`, `colors.primary`, `typography`, `spacing`, `rounded`, and `components`.
- Component tokens use only recognized keys: `backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, `width`.
- Every token reference resolves; every important color is referenced by at least one component unless a source-faithful exception is documented.
- Prose explains intent and application, not just raw CSS values.
- The output names concrete source limitations: inaccessible CSS, no browser, auth wall, SPA shell, or linter/runtime failure.

## Output Contract

- Preserve observed source truth over aesthetic invention.
- Include enough tokens and prose for another agent to generate visually consistent screens, and validate with `npx -y @google/design.md lint --format json <output-dir>/DESIGN.md` asserting `summary.errors === 0` and that `colors`, `typography`, `spacing`, and `components` are present (a bare exit code is not sufficient).
