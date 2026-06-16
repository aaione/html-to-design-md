---
version: "alpha"
name: Atlas Editorial
description: A design system for a premium editorial product — high-contrast neutrals with a single interaction accent and explicit functional states.
colors:
  primary: "#1A1C1E"
  secondary: "#6C7278"
  accent: "#B8422E"
  background: "#F7F5F2"
  surface-dark: "#1A1C1E"
  success: "#1B5E20"
  error: "#B71C1C"
  on-primary: "#FFFFFF"
  on-accent: "#FFFFFF"
typography:
  h1:
    fontFamily: Public Sans
    fontSize: 48px
    fontWeight: 600
    lineHeight: 1.1
    letterSpacing: -0.02em
  headline-md:
    fontFamily: Public Sans
    fontSize: 32px
    fontWeight: 600
    lineHeight: 1.2
  body-md:
    fontFamily: Public Sans
    fontSize: 16px
    fontWeight: 400
    lineHeight: 1.6
  label-caps:
    fontFamily: Space Grotesk
    fontSize: 12px
    fontWeight: 500
    lineHeight: 1
    letterSpacing: 0.1em
rounded:
  sm: 4px
  md: 8px
  lg: 12px
  full: 9999px
spacing:
  xs: 4px
  sm: 8px
  md: 16px
  lg: 32px
components:
  button-primary:
    backgroundColor: "{colors.accent}"
    textColor: "{colors.on-accent}"
    typography: "{typography.label-caps}"
    rounded: "{rounded.sm}"
    padding: "{spacing.sm} {spacing.md}"
  button-primary-hover:
    backgroundColor: "#9E3A28"
  button-primary-focus:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.on-primary}"
  card:
    backgroundColor: "{colors.background}"
    textColor: "{colors.primary}"
    typography: "{typography.body-md}"
    rounded: "{rounded.lg}"
    padding: "{spacing.md}"
  card-dark:
    backgroundColor: "{colors.surface-dark}"
    textColor: "#FFFFFF"
    typography: "{typography.body-md}"
    rounded: "{rounded.lg}"
    padding: "{spacing.md}"
  hero:
    backgroundColor: "{colors.background}"
    textColor: "{colors.primary}"
    typography: "{typography.h1}"
  banner:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.on-primary}"
  caption:
    textColor: "{colors.secondary}"
    typography: "{typography.body-md}"
  badge-success:
    backgroundColor: "{colors.success}"
    textColor: "#FFFFFF"
    typography: "{typography.label-caps}"
    rounded: "{rounded.full}"
    padding: "{spacing.xs} {spacing.sm}"
  badge-error:
    backgroundColor: "{colors.error}"
    textColor: "#FFFFFF"
    typography: "{typography.label-caps}"
    rounded: "{rounded.full}"
    padding: "{spacing.xs} {spacing.sm}"
  product-card:
    backgroundColor: "{colors.background}"
    textColor: "{colors.primary}"
    typography: "{typography.body-md}"
    rounded: "{rounded.lg}"
    padding: "{spacing.md}"
---

## Overview

Architectural minimalism meets journalistic gravitas. The UI reads as a premium matte broadsheet — confident ink-on-limestone neutrals with one disciplined accent that drives every interaction. Restraint is the system: a single accent per screen, generous whitespace, and two typefaces that never compete. Functional states (success/error) are explicit, dedicated tokens — never ad-hoc greens and reds.

## Colors

The palette is rooted in high-contrast neutrals plus a single accent; functional states are first-class tokens.

- **Primary (#1A1C1E):** Deep ink for headlines, body text, and the banner.
- **Secondary (#6C7278):** Sophisticated slate for captions, metadata, and dividers.
- **Accent (#B8422E):** "Boston Clay" — the sole driver for primary actions; use once per view.
- **Background (#F7F5F2):** Warm limestone foundation, softer than pure white.
- **Surface-dark (#1A1C1E):** Dark-theme container fill (see `card-dark`). Alternate themes use a separate scalar token like this — never a `[light, dark]` array, which the linter rejects.
- **Success (#1B5E20) / Error (#B71C1C):** Functional-state fills for status badges; grouped by role, not hue.
- **On-primary / On-accent (#FFFFFF):** Text placed on the primary and accent fills; both clear WCAG AA.

## Typography

Two voices: **Public Sans** carries the narrative across a four-step scale, **Space Grotesk** labels technical data.

- **Display (h1):** Public Sans Semi-Bold at 48px, tight tracking, for institutional hero headlines.
- **Headline (headline-md):** Public Sans Semi-Bold at 32px for section titles.
- **Body (body-md):** Public Sans Regular at 16px / 1.6 for long-form readability.
- **Labels (label-caps):** Space Grotesk, uppercase with 0.1em tracking, reserved for buttons, badges, and taxonomy.

## Layout

An 8px spacing scale governs everything; content columns sit within a 1200px max width. Use `spacing.md` as the default gutter and `spacing.lg` to separate major regions. Cards and heroes align to the same 8px rhythm so density stays predictable across pages.

## Elevation & Depth

Depth is implied by contrast and surface, not heavy shadow. Keep surfaces flat on the limestone background; reserve a single level of separation (a 1px secondary-toned hairline or a subtle inset) for interactive cards. Avoid drop shadows — they break the matte editorial feel.

## Shapes

Corner radius follows a four-step scale. Buttons take `rounded.sm` (4px) to stay crisp; cards take `rounded.lg` (12px); `rounded.md` (8px) covers inputs; `rounded.full` (9999px) makes pills of status badges. Never mix radii within the same component group.

## Components

- **Buttons:** Primary actions use the accent fill with white label-caps text (≈5.4:1). The `hover` state darkens to #9E3A28; the `focus` state inverts to the primary fill for high visibility — states are modeled as separate component entries using recognized sub-tokens.
- **Cards:** Content cards on the background fill with `primary` text, `body-md`, `rounded.lg`, `spacing.md`. The `card-dark` variant inverts to `surface-dark` — alternate themes via scalar tokens, not arrays.
- **Hero:** Full-bleed headline block, `h1` type in primary ink.
- **Banner:** High-contrast strip on the primary fill with `on-primary` text.
- **Caption:** Secondary slate text for metadata beneath images and cards.
- **Badges:** Pill-shaped (`rounded.full`) status indicators — `badge-success` and `badge-error` show how functional-state colors are bound to components.
- **Product card:** A domain-specific component named for the listing it represents — same surface treatment as a card (its 4:3 image ratio is noted in prose, since `aspectRatio` is not a recognized token slot). Always emit 1-2 components unique to the site beyond generic primitives.

## Do's and Don'ts

- **Do** use the accent for the single most important action per screen.
- **Do** model functional states (success/error) as dedicated, named tokens — not ad-hoc hex values.
- **Do** keep text on fills at the `on-*` tokens to hold WCAG AA.
- **Don't** mix sharp and rounded corners, or introduce a second accent — the system is intentionally monochromatic-plus-one.
- **Don't** apply drop shadows; separate surfaces with hairlines and whitespace instead.
