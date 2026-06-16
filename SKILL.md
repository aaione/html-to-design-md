---
name: html-to-design-md
description: Generate a DESIGN.md design-system file from a website URL, localhost page, or local HTML file. Use when Codex needs to inspect rendered web UI or static HTML, extract visual identity, tokens, component rules, layout principles, and write DESIGN.md into the current directory or a user-specified output directory, then validate the result with the @google/design.md CLI using npx @google/design.md lint DESIGN.md.
---

# HTML To DESIGN.md

## Overview

Create a `DESIGN.md` design-system artifact from a live website, localhost page, or local `.html` file. The output must follow the `@google/design.md` format: YAML frontmatter with design tokens plus Markdown rationale.

Use `references/design-md-format.md` when you need the compact schema, section order, or lint rule reminders.

## Workflow

1. Resolve inputs:
   - Source: URL, localhost route, or local HTML path.
   - Output directory: use the user-specified directory when provided; otherwise use the current working directory.
   - Final path: always write `<output-directory>/DESIGN.md`.

2. Inspect the design:
   - For websites or localhost pages, open the rendered page with the available browser tool and capture desktop plus mobile screenshots when feasible.
   - For local HTML, open it directly when browser rendering works; otherwise read the HTML/CSS and inspect linked local styles.
   - Inspect source styles and computed styles before relying on screenshot inference.
   - Record concrete evidence: colors, fonts, spacing scale, radii, shadows, component states, layout density, navigation, forms, cards, buttons, and responsive behavior.

3. Extract tokens:
   - Prefer exact values from CSS variables, computed styles, Tailwind classes, inline styles, or stylesheet declarations.
   - Use screenshot sampling only when source styles are unavailable; mark inferred values in prose, not in token names.
   - Include at minimum `name`, `colors`, `typography`, `rounded`, `spacing`, and useful `components` tokens when observable.

4. Write `DESIGN.md`:
   - Start with YAML frontmatter delimited by `---`.
   - Use exact hex colors, dimensions with units, and token references such as `{colors.primary}` inside component tokens.
   - Follow canonical section order: `Overview`, `Colors`, `Typography`, `Layout`, `Elevation & Depth`, `Shapes`, `Components`, `Do's and Don'ts`.
   - Keep prose semantic and operational: describe why tokens exist and how future UI should apply them.

5. Validate:
   - Use `npx` so validation does not depend on a global `design.md` install.
   - From the output directory, run:

```bash
npx @google/design.md lint DESIGN.md
```

   - If `npx` prompts for package installation in a non-interactive shell, rerun with `npx -y @google/design.md lint DESIGN.md` and report the substituted command.
   - If validation fails because of environment issues such as no network, no disk space, npm cache corruption, or package registry access, do not claim the file passed. Report the exact failure and perform a manual schema pass against `references/design-md-format.md`.
   - Treat any lint error as blocking. Fix the file and rerun until the command exits successfully.
   - Warnings are allowed only when they reflect an intentional or source-faithful choice; mention remaining warnings in the final response.

## Output Contract

The generated file must:

- Be named exactly `DESIGN.md`.
- Live in the requested output directory, or the current working directory if none was specified.
- Preserve observed source truth over aesthetic invention.
- Include enough tokens and prose for another agent to generate visually consistent screens.
- Pass `npx @google/design.md lint DESIGN.md` before completion unless local environment failure prevents running the CLI; report that failure with the command output.

## Practical Notes

- If the website is authenticated, inspect only what the user has made accessible in the active browser/session.
- If assets or fonts are remote and unavailable, infer cautiously from rendered output and document uncertainty in prose.
- If multiple pages are provided, synthesize shared system tokens first, then note page-specific variants under `Components` or `Do's and Don'ts`.
- Do not create extra docs, reports, or screenshots unless they are needed for the task or requested by the user.
