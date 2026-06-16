---
name: html-to-design-md
description: Generate a lint-clean DESIGN.md design-system spec (colors, typography, spacing, components, usage rules) from a website URL, localhost page, or local HTML. Use when the user wants a @google/design.md-formatted design system extracted from an observed UI.
argument-hint: <url-or-html-path> [output-dir]
allowed-tools: Read, Write, Bash(npx -y @google/design.md lint *)
---

# HTML To DESIGN.md

## Overview

Turn a live website, localhost page, or local `.html` file into a `DESIGN.md` that follows the `@google/design.md` format: YAML frontmatter with machine-readable tokens plus Markdown rationale. Preserve observed source truth; do not invent aesthetics.

Keep `references/design-md-format.md` open for the token schema, canonical section order, and the authoritative lint-rule table. Mirror `references/EXAMPLE.md` for a complete, lint-clean structure.

## Workflow

1. **Resolve inputs.** The source URL/localhost/local HTML and the optional output dir come from `$ARGUMENTS` (`<url-or-html-path> [output-dir]`). If `$ARGUMENTS` is empty, ask the user for a URL/path. Always write to `<output-dir>/DESIGN.md` (default: current working directory).

2. **Inspect the design.** Capture token evidence — colors, typography, spacing scale, radii, shadows, component states, layout density, responsive behavior. Inspect source and computed styles before relying on screenshot inference; mark any inferred value in prose, never in a token name.

3. **Extract tokens.** Pull exact values from CSS variables, computed styles, framework classes, or inline styles. Emit at minimum `name`, `colors` (including a `primary`), `typography`, `rounded`, `spacing`, and the observable `components`. When the source exposes a dark/alternate theme, capture those colors as two-element `[light, dark]` arrays and document the mapping in the Colors section.

4. **Write `DESIGN.md`.** Frontmatter delimited by `---`; exact sRGB hex colors; dimensions with units; component tokens reference tokens like `{colors.primary}`. Follow the canonical section order (see the reference). Prose should state *why* tokens exist and *how* future UI applies them. Mirror `references/EXAMPLE.md`.

5. **Validate (blocking).** From the output directory run, and treat any error as blocking — fix and rerun until exit 0:

```bash
npx -y @google/design.md lint <output-dir>/DESIGN.md
```

Only `broken-ref` is a hard error (exit 1); the rest are warnings/info. Warnings (e.g. contrast-ratio, orphaned-tokens) are allowed only when source-faithful — mention remaining ones in the final response. The validator is unpinned (`@google/design.md`), so a pass is authoritative only for the currently-resolved version. If the environment cannot run the CLI (no network, registry blocked, etc.), report the exact failure and do a manual schema pass against the reference instead of claiming success.

## Capabilities & Fallbacks

- **Browser tool.** Rendered-page inspection needs a browser/Playwright-style tool. If none is available, fall back deterministically: (1) fetch raw HTML, (2) read inline + linked CSS, (3) mark all computed-style-dependent tokens as inferred. Never fabricate values a browser would have shown.
- **JS-rendered / SPA pages.** If fetched HTML is an empty shell (mount node only), require a real browser to render; if unavailable, halt with a clear report rather than emit an empty DESIGN.md. Re-render the same source the same way so re-runs stay reproducible.
- **Auth-gated pages.** If the page resolves to a login/consent/interstitial, ask the user to authenticate in the active session first. Never treat login chrome as the site's design system.
- **Multiple pages.** Synthesize shared tokens first; the first page wins for conflicting base tokens; record deviations as named variants under Components. Preserve input order across re-runs.

## Output Contract

- Preserve observed source truth over aesthetic invention.
- Include enough tokens and prose for another agent to generate visually consistent screens, and exit `npx -y @google/design.md lint` with code 0.
