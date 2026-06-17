# DESIGN.md Format Reference

Authoritative compact reference for generating or repairing `DESIGN.md` files validated by the `@google/design.md` CLI. Source of truth: the currently resolved `@google/design.md` linter (`npx @google/design.md lint`). If upstream docs and the CLI disagree, the CLI result wins and the final response should mention the version/runtime used. For a full HTML→DESIGN.md extraction walk, see [worked-example.md](worked-example.md); for a finished lint-clean system, see [EXAMPLE.md](EXAMPLE.md).

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
  <token>: <Color>            # any valid CSS color; hex "#1A1C1E" is the recommended default (arrays rejected)
typography:
  <token>:                  # camelCase keys recommended; kebab-case also accepted
    fontFamily: <string>
    fontSize: <Dimension>          # e.g. 16px, 1rem
    fontWeight: <number>           # e.g. 400
    lineHeight: <number>           # e.g. 1.6
    letterSpacing: <Dimension>     # e.g. -0.02em, 0.1em
    # optional: fontFeatureSettings / fontVariationSettings (variable fonts),
    # fontStyle, fontStretch — accepted by the linter but non-standard
    # consumption fields; use to record source-faithful type detail, not as
    # primary tokens.
rounded:
  <level>: <Dimension>            # sm/md/lg/full(9999px)
spacing:
  <level>: <Dimension | number>
components:
  <name>:
    <prop>: <string | token reference>   # ONLY recognized: backgroundColor, textColor, typography, rounded, padding, size, height, width
```

### Token Rules

- **Colors** accept valid CSS color values; `"#1A1C1E"`-style hex is the recommended default. `oklch()`, `hsl()`, 8-digit hex (`"#RRGGBBAA"`), and named colors are accepted by the current CLI; newer functions such as `color-mix()` may still fail, so validate unusual source-native colors before preserving them. Do **not** use arrays (`surface: [light, dark]`): the current CLI rejects them with `is not a valid color`, which is a **build-failing error** (see Validation), not a warning. For dark/alternate themes, add separate scalar tokens (e.g. `surface-dark`) and describe the mapping in the Colors prose.
- **Dimensions** include units (`px`, `rem`, `em`).
- **Token references** use braces, e.g. `{colors.primary}`, `{typography.body-md}`, `{rounded.md}`, `{spacing.md}`. Unresolvable references are the one hard lint error.
- **Component sub-tokens are whitelisted:** only `backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, `width` are recognized — any other key (e.g. `borderColor`, `aspectRatio`) triggers an "unrecognized sub-token" warning. Express anything else (focus rings, aspect ratios, motion, borders) in prose.
- **Component variants** are separate entries, e.g. `button-primary-hover`.
- Every defined color token should be referenced by at least one component (unreferenced colors warn as orphaned).

## Extraction Source Priority

Trust sources in this order; when they conflict, the higher one is the *declared intent* and wins, and you note the deviation in prose:

1. **Intent tokens** — `tailwind.config` / `theme.ts`, `:root` CSS custom properties, theme & font files. The developer *declared* these as the system. Prefer them.
2. **Computed styles** — the browser's resolved values on real elements. Use to confirm intent and fill gaps intent didn't name.
3. **Inline / framework classes** — per-element Tailwind / CSS-in-JS. Actual shipped values; use to verify and catch overrides.
4. **Screenshot sampling** — last resort, only when no source is reachable; always mark inferred.

Code comments and custom-property names (`--brand-primary`, `--warm-50`) are the developer telling you the design intent — read them.

## Color Organization

- **Group by role, not hue:** primary/secondary/accent, surface/background, `on-*` (text on a fill), and functional states (success/error/warning/info).
- **Deduplicate:** consolidate near-duplicates (`#333` and `#2C2C2C`) under one token — don't emit noise.
- **Name functional states semantically:** `success`, `error`, and reference each from a component (e.g. `badge-success`) so it isn't orphaned.
- **`on-*` convention:** pair every fill with its text token (`primary` / `on-primary`) so contrast is explicit and AA-checkable.

## Reverse-engineering Tokens from Frameworks

When the source uses a framework, map its primitives to DESIGN.md tokens — but **always resolve against the project's own config**, never framework defaults, or values won't be source-faithful.

| Stack | Where tokens live | Notes |
|:------|:------------------|:------|
| Tailwind | `tailwind.config.{js,ts}` → `theme.extend.colors/spacing/borderRadius/fontFamily` | `rounded-lg` etc. are classes; resolve to the project's `borderRadius.lg`, not the 0.5rem default. |
| shadcn/ui | `globals.css` `:root` HSL vars (`--primary`, `--radius`) | Already semantic tokens — lift directly. |
| CSS-in-JS | theme object (styled-components/Emotion `theme`, Chakra `extendTheme`, MUI `createTheme`) | Read the theme object, not rendered styles. |
| MUI / Material | `createTheme` palette & typography | Map palette slots to color tokens. |

Class-to-value rules must be verified in config: `rounded-sm/md/lg/xl/full`, the spacing base (4px vs 8px), and the font-size scale. If no config is reachable, fall back to computed styles and mark inferred.

### Per-stack recipes

- **Tailwind** — resolve every utility against `tailwind.config`, never the framework defaults. `rounded-lg` → `theme.extend.borderRadius.lg` (framework default is `0.5rem`; use it only if the project hasn't overridden); `text-xl` → `theme.fontSize.xl`, carrying its `lineHeight`; `bg-brand-500` → `theme.extend.colors.brand[500]`, named by role not scale-step. The spacing base (`theme.spacing` / `--spacing`) governs every `p-*` / `m-*` / `gap-*` class.
- **shadcn/ui** — `globals.css` `:root` holds HSL channel triples that are already semantic. `--primary: 10 70% 45%` → emit `hsl(10 70% 45%)` as the `primary` token (the linter accepts `hsl()` directly — no hex conversion needed); `--radius: 0.5rem` → `rounded.md`. Lift `--background`, `--foreground`, `--muted`, `--border` straight by role.
- **CSS-in-JS** (styled-components / Emotion / Chakra / MUI) — read the exported theme object (`theme.ts`, `extendTheme(...)`, `createTheme(...)`), and lift `colors` / `fontSizes` / `radii` / `space` directly into DESIGN.md tokens. Component-level `sx` / `css` props override the theme — spot-check 2-3 real components for shipped deviations.

## HTML / Website Extraction

Use the richest evidence path available and state gaps in the final response.

| Source | Strongest evidence | Notes |
|:--|:--|:--|
| Saved HTML | Local markup, inline CSS, linked local CSS/assets | Resolve relative paths from the HTML file before inferring values. |
| Rendered URL / localhost | DOM, linked stylesheets, computed styles, screenshots across at least desktop and mobile when possible | Computed styles prove shipped behavior; screenshots help confirm visual hierarchy. |
| Framework source present | Theme files, Tailwind config, CSS variables, component styles | Intent tokens beat framework defaults. |
| Screenshot-only | Pixel sampling and visual inference | Last resort; keep inferred values in prose and avoid over-tokenizing. |

Reject low-evidence output. If the page is an auth wall, consent page, error page, or empty SPA shell without rendering access, report the blocker instead of generating a misleading DESIGN.md.

## Minimum Output Coverage

- `colors.primary` plus surface/background, text/on-* pairs, accent/interactive colors, and functional states when present.
- At least one body typography token and the visible heading/label scale.
- A spacing scale and radius scale that match observed layout primitives.
- Components for real UI primitives: primary button, card/container or equivalent, navigation/input/status when present, plus 1-2 domain-specific components.
- Markdown prose for non-whitelisted properties such as borders, shadows, motion, aspect ratios, blur, and responsive behavior.
- **Responsive & layout stay in prose.** Breakpoints, gutters, max-width, and grid columns are not frontmatter tokens — there is no `layout` top-level key, and unitless `spacing` numbers are silently ignored by the linter. Describe them in the `## Layout` section instead.

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

## Prose-only Concerns

Several design dimensions have no frontmatter slot — the component sub-token whitelist is fixed at eight keys — so capture them in prose under the matching section:

- **Motion:** transition `duration` scale, `easing` curves, and `prefers-reduced-motion` behavior → `## Components` or `## Elevation & Depth`.
- **Iconography:** icon set, stroke width, optical sizing → `## Components`.
- **Stacking / z-index:** elevation layering order → `## Elevation & Depth`.
- **Focus & affordance:** focus-ring width, color, and offset → `## Components`.

Never model these as component sub-tokens — `borderColor`, `transition`, `zIndex`, `ring` all warn as unrecognized.

## Validation

From the directory containing the file:

```bash
npx -y @google/design.md lint DESIGN.md          # non-interactive (use -y in agents/CI)
npx -y @google/design.md lint --format json DESIGN.md
```

Exit code is `1` only when errors are found, `0` otherwise.

### Lint Rules

Hard errors (exit 1) are `broken-ref` plus schema-validation failures — most notably an invalid color value (a color array serializes to `#000,#fff` and fails with `is not a valid color`). Everything else is a warning or info — resolve warnings when the choice is source-faithful; report any that remain.

| Rule | Severity | What it checks |
|:-----|:---------|:---------------|
| `broken-ref` | **error** | A token reference like `{colors.primary}` that resolves to no defined token |
| `invalid-color` | **error** | A color value the CSS parser rejects — e.g. an array `["#000", "#fff"]` serialized as `#000,#fff`. Build-failing. |
| `missing-primary` | warning | Colors are defined but there is no `primary` color |
| `contrast-ratio` | warning | A component `backgroundColor`/`textColor` pair below WCAG AA (4.5:1) |
| `orphaned-tokens` | warning | A color token defined but never referenced by any component |
| `missing-typography` | warning | Colors are defined but no typography tokens exist |
| `section-order` | warning | Sections appear out of the canonical order above |
| `token-summary` | info | Per-section count of defined tokens |
| `missing-sections` | info | Optional sections (spacing, rounded) absent while other tokens exist |
| `unknown-key` | warning | A top-level YAML key that looks like a typo of a known key (e.g. `colours:` → `colors:`) |

Fix all errors before completion. Common real causes of failure: unresolvable token references, a missing `primary` color, low-contrast component pairs, out-of-order headings, and typo'd top-level keys (caught by `unknown-key`).

**Debug cascade:** if lint reports `broken-ref` for a reference whose name looks correct, first scan for an `unknown-key` warning. A typo'd top-level key (e.g. `colours:` instead of `colors:`) silently fails to register its tokens, which then cascades into `broken-ref` errors on every `{colors.*}` reference. Fix the typo at the root; the broken-refs resolve with it.
