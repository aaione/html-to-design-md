---
version: "alpha"
name: Atlas Editorial
description: A design system for a premium editorial product — high-contrast neutrals with a single interaction accent.
colors:
  primary: "#1A1C1E"
  secondary: "#6C7278"
  accent: "#B8422E"
  background: "#F7F5F2"
  on-primary: "#FFFFFF"
  on-accent: "#FFFFFF"
typography:
  h1:
    fontFamily: Public Sans
    fontSize: 48px
    fontWeight: 600
    lineHeight: 1.1
    letterSpacing: -0.02em
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
  card:
    backgroundColor: "{colors.background}"
    textColor: "{colors.primary}"
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
---

## Overview

Architectural minimalism meets journalistic gravitas. The UI reads as a premium matte broadsheet — confident ink-on-limestone neutrals with one disciplined accent that drives every interaction. Restraint is the system: a single accent per screen, generous whitespace, and two typefaces that never compete.

## Colors

The palette is rooted in high-contrast neutrals plus a single accent.

- **Primary (#1A1C1E):** Deep ink for headlines, body text, and the banner.
- **Secondary (#6C7278):** Sophisticated slate for captions, metadata, and dividers.
- **Accent (#B8422E):** "Boston Clay" — the sole driver for primary actions; use once per view.
- **Background (#F7F5F2):** Warm limestone foundation, softer than pure white.
- **On-primary / On-accent (#FFFFFF):** Text placed on the primary and accent fills; both clear WCAG AA.

## Typography

Two voices: **Public Sans** carries the narrative, **Space Grotesk** labels technical data.

- **Headlines:** Public Sans Semi-Bold (h1) at 48px with tight tracking for an institutional voice.
- **Body:** Public Sans Regular (body-md) at 16px / 1.6 for long-form readability.
- **Labels:** Space Grotesk (label-caps), uppercase with 0.1em tracking, reserved for buttons and taxonomy.

## Layout

An 8px spacing scale governs everything; content columns sit within a 1200px max width. Use `spacing.md` as the default gutter and `spacing.lg` to separate major regions. Cards and heroes align to the same 8px rhythm so density stays predictable across pages.

## Elevation & Depth

Depth is implied by contrast and surface, not heavy shadow. Keep surfaces flat on the limestone background; reserve a single level of separation (a 1px secondary-toned hairline or a subtle inset) for interactive cards. Avoid drop shadows — they break the matte editorial feel.

## Shapes

Corner radius follows a three-step scale. Buttons take `rounded.sm` (4px) to stay crisp; cards take `rounded.lg` (12px) to read as containers; `rounded.md` (8px) covers inputs and tags. Never mix radii within the same component group.

## Components

- **Buttons:** Primary actions use the accent fill with white label-caps text (≈5.4:1 contrast). The hover state darkens the accent to #9E3A28. There is no secondary button — restraint forces a single primary action per view.
- **Cards:** Content cards sit on the background fill with `primary` text, `body-md` type, `rounded.lg`, and `spacing.md` padding.
- **Hero:** Full-bleed headline block on the background, `h1` type in primary ink.
- **Banner:** High-contrast strip on the primary fill with `on-primary` text for announcements.
- **Caption:** Secondary slate text for metadata beneath images and cards.

## Do's and Don'ts

- **Do** use the accent for the single most important action per screen.
- **Do** keep text on the primary/accent fills at `on-primary`/`on-accent` to hold WCAG AA.
- **Don't** mix sharp and rounded corners within the same view.
- **Don't** introduce a second accent color — the system is intentionally monochromatic-plus-one.
- **Don't** apply drop shadows; separate surfaces with hairlines and whitespace instead.
