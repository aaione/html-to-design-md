# Worked Example: HTML → DESIGN.md

A complete, copy-pasteable walk from a real HTML snippet to a lint-clean `DESIGN.md`. Where [`EXAMPLE.md`](EXAMPLE.md) is the *destination* (a finished, lint-clean system), this file is the *path* — it shows the extraction reasoning so token granularity and prose choices stay reproducible across runs.

The output frontmatter below is independently verified lint-clean when saved as a standalone `DESIGN.md` (`npx -y @google/design.md lint` → exit 0, 0 errors, 0 warnings).

---

## Input

A small e-commerce listing built on the Tailwind CDN — two product cards, a masthead, and a button with a hover state.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Atlas Goods</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-stone-100 text-stone-900 font-sans antialiased">
  <header class="max-w-5xl mx-auto px-6 py-8">
    <h1 class="text-4xl font-semibold tracking-tight">Atlas Goods</h1>
    <p class="mt-2 text-stone-500 text-lg">Considered objects for everyday use.</p>
  </header>
  <main class="max-w-5xl mx-auto px-6 pb-16 grid grid-cols-2 gap-6">
    <article class="bg-white rounded-xl p-6 shadow-sm">
      <img src="mug.jpg" class="w-full h-40 object-cover rounded-lg mb-4" alt="Mug">
      <h2 class="text-xl font-semibold">Ceramic Mug</h2>
      <p class="text-stone-500 mt-1 text-lg">Hand-glazed, 300ml.</p>
      <button class="mt-4 inline-flex items-center px-4 py-2 rounded-md
                     bg-orange-700 text-white text-sm font-medium
                     hover:bg-orange-800 focus:ring-2 focus:ring-orange-700">
        Add to cart
      </button>
    </article>
    <article class="bg-white rounded-xl p-6 shadow-sm">
      <img src="book.jpg" class="w-full h-40 object-cover rounded-lg mb-4" alt="Book">
      <h2 class="text-xl font-semibold">Field Notes</h2>
      <p class="text-stone-500 mt-1 text-lg">Pocket journal, 48 pages.</p>
      <button class="mt-4 inline-flex items-center px-4 py-2 rounded-md
                     bg-orange-700 text-white text-sm font-medium
                     hover:bg-orange-800 focus:ring-2 focus:ring-orange-700">
        Add to cart
      </button>
    </article>
  </main>
</body>
</html>
```

---

## Step 1 — Collect evidence

The Tailwind **CDN** ships no `tailwind.config`, so intent tokens are unavailable. Per *Extraction Source Priority*, fall back to **framework defaults** (the CDN's built-in scale) and mark every value as `inferred` in the final response. No custom webfont is loaded, so `font-sans` resolves to the system stack. This is the lowest-confidence evidence path — say so explicitly rather than presenting defaults as the site's authored design.

## Step 2 — Resolve framework values

Map each utility class to its Tailwind v3 default (these are *defaults*, not authored tokens):

| Class | Resolves to | Token |
|:------|:------------|:------|
| `bg-stone-100` | `#F5F5F4` | `background` |
| `text-stone-900` | `#1C1917` | `primary` |
| `text-stone-500` | `#78716C` | `secondary` |
| `bg-white` | `#FFFFFF` | `surface` |
| `bg-orange-700` | `#C2410C` | `accent` |
| `hover:bg-orange-800` | `#9A3412` | `accent-hover` |
| `text-white` | `#FFFFFF` | `on-accent` |
| `text-4xl` / `font-semibold` / `tracking-tight` | 36px / 600 / −0.02em | `h1` |
| `text-xl` / `font-semibold` | 20px / 600 | `headline` |
| `text-lg` | 18px / 400 | `body` |
| `text-sm` / `font-medium` | 14px / 500 | `label` |
| `rounded-md` / `rounded-lg` / `rounded-xl` | 6 / 8 / 12px | `rounded.md/lg/xl` |
| `py-2` / `px-4` / `p-6` / `py-8` / `gap-6` | 8 / 16 / 24 / 32 / 24px | `spacing.sm/md/lg/xl` |

`rounded-lg` (8px) appears only on images; `rounded` orphans are not linted, but the scale is kept for completeness.

## Step 3 — Decide token granularity

Resist over-tokenizing. The page shows **7 roles**, not 30 raw classes: primary/secondary/accent/accent-hover/background/surface/on-accent. Typography collapses to **4 steps** (h1 / headline / body / label), not one per text class. Functional hover is a **named token** (`accent-hover`), never an inline value. Every color is referenced by ≥1 component so none orphan.

## Step 4 — Write the frontmatter

```yaml
---
version: "alpha"
name: Atlas Goods
description: Worked-example output for an e-commerce listing built on Tailwind CDN defaults.
colors:
  primary: "#1C1917"
  secondary: "#78716C"
  accent: "#C2410C"
  accent-hover: "#9A3412"
  background: "#F5F5F4"
  surface: "#FFFFFF"
  on-accent: "#FFFFFF"
typography:
  h1:
    fontFamily: "ui-sans-serif, system-ui, sans-serif"
    fontSize: 36px
    fontWeight: 600
    lineHeight: 1.1
    letterSpacing: -0.02em
  headline:
    fontFamily: "ui-sans-serif, system-ui, sans-serif"
    fontSize: 20px
    fontWeight: 600
    lineHeight: 1.4
  body:
    fontFamily: "ui-sans-serif, system-ui, sans-serif"
    fontSize: 18px
    fontWeight: 400
    lineHeight: 1.6
  label:
    fontFamily: "ui-sans-serif, system-ui, sans-serif"
    fontSize: 14px
    fontWeight: 500
    lineHeight: 1.25
rounded:
  md: 6px
  lg: 8px
  xl: 12px
spacing:
  sm: 8px
  md: 16px
  lg: 24px
  xl: 32px
components:
  page:
    backgroundColor: "{colors.background}"
    textColor: "{colors.primary}"
    typography: "{typography.body}"
  headline:
    textColor: "{colors.primary}"
    typography: "{typography.h1}"
  caption:
    textColor: "{colors.secondary}"
    typography: "{typography.body}"
  card:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.primary}"
    typography: "{typography.headline}"
    rounded: "{rounded.xl}"
    padding: "{spacing.lg}"
  card-title:
    textColor: "{colors.primary}"
    typography: "{typography.headline}"
  button-primary:
    backgroundColor: "{colors.accent}"
    textColor: "{colors.on-accent}"
    typography: "{typography.label}"
    rounded: "{rounded.md}"
    padding: "{spacing.sm} {spacing.md}"
  button-primary-hover:
    backgroundColor: "{colors.accent-hover}"
    textColor: "{colors.on-accent}"
---
```

## Step 5 — Write the rationale prose

Prose carries everything the whitelist cannot. Two decisions to copy as a pattern:

- **Hover is a component variant, not prose.** `button-primary-hover` is a separate component entry (`backgroundColor: {colors.accent-hover}`) — variant states belong in tokens when they have a fill change.
- **Focus ring is prose, not a token.** `focus:ring-2 focus:ring-orange-700` has no whitelisted sub-token, so it lives in the Components section as description ("focus shows a 2px accent ring"), never as a `borderColor`/`ring` key (which would warn as unrecognized).
- **Responsive collapse is Layout prose.** `grid-cols-2` → single column under `md` is described in `## Layout`; breakpoints are not frontmatter tokens (no `layout` key; unitless spacing is ignored).
- **Image aspect ratio is prose.** The 4:3 framing (`h-40` full-width) is noted under Shapes, since `aspectRatio` is not a recognized sub-token.

The eight canonical sections follow in order: Overview, Colors, Typography, Layout, Elevation & Depth, Shapes, Components, Do's and Don'ts.

## Step 6 — Validate

```bash
npx -y @google/design.md lint DESIGN.md
# → exit 0, 0 errors, 0 warnings (1 info summary: 7 colors, 4 typography, 3 radius, 4 spacing, 7 components)
```

The `on-accent` / `accent` pair clears WCAG AA at ~5.2:1, so no `contrast-ratio` warning. Every color is referenced, so no `orphaned-tokens` warning.

---

## Key decisions, explained

- **Why a `page` component?** The `background` color must be referenced by a component or it warns as orphaned. The page canvas is a real component, so it earns the token rather than being a workaround.
- **Why `accent-hover` as a color, not just a component override?** Hover fills recur (buttons, links, nav); a named color token lets every hover variant reference one source of truth and keeps the palette auditable.
- **Why is the whole result marked `inferred`?** The CDN carries no authored config. The final response to the user must state that these are Tailwind defaults, not the site's own design tokens — source-faithful means naming the limitation.
