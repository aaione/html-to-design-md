# DESIGN.md Format Reference

Use this compact reference when generating or repairing `DESIGN.md` files for the `@google/design.md` CLI.

## Structure

`DESIGN.md` has two layers:

1. YAML frontmatter with machine-readable tokens.
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
    rounded: "{rounded.md}"
    padding: 12px
---

## Overview

...
```

## Token Rules

- Colors use sRGB hex strings such as `"#1A1C1E"`.
- Dimensions include units such as `px`, `rem`, or `em`.
- Token references use braces, for example `{colors.primary}`.
- Component properties commonly include `backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, and `width`.
- Variants can be separate component entries, such as `button-primary-hover`.

## Section Order

Use `##` headings in this order when present:

1. `Overview`
2. `Colors`
3. `Typography`
4. `Layout`
5. `Elevation & Depth`
6. `Shapes`
7. `Components`
8. `Do's and Don'ts`

## Validation

Run from the directory containing the generated file:

```bash
npx @google/design.md lint DESIGN.md
```

If `npx` requires confirmation in a non-interactive environment, use:

```bash
npx -y @google/design.md lint DESIGN.md
```

Fix lint errors before completion. Common blocking issues are broken token references, duplicate section headings, invalid YAML, invalid color values, and malformed dimensions.
