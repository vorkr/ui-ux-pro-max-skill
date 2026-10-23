# UI/UX Pro Max Skills (Cursor)

Local Cursor skill suite cloned from [vorkr/ui-ux-pro-max-skill](https://github.com/vorkr/ui-ux-pro-max-skill) (upstream UI/UX Pro Max v2.5.0).

Skills live under `.cursor/skills/` so Cursor loads them in this project. This repo keeps a **lean** install (runtime search data + skill docs only — no upstream test fixtures or icon/font license dump files). See `SOURCE.md`.

## Installed skills

| Skill | Purpose |
|-------|---------|
| `ui-ux-pro-max` | Searchable design intelligence (styles, palettes, fonts, UX, stacks, charts, motion) |
| `ui-styling` | Styling patterns and implementation guidance |
| `design` | Broader design workflow |
| `design-system` | Design-system generation and tokens |
| `brand` | Brand identity guidance |
| `banner-design` | Banner / promo layout |
| `slides` | Slide / presentation layouts |

## Requirements

- Python 3 (stdlib only) for the search scripts
- No npm install required to *use* the skills

## Quick use

From the project root:

```bash
# Design system for a product
python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "saas fintech dashboard" --design-system -p "Acme Ops"

# Domain search
python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "glassmorphism" --domain style -n 5

# Stack guidance
python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "form validation" --stack nextjs

# UX guidance that feeds UI tests / a11y cases
python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "keyboard focus modal" --domain ux
```

In Cursor, ask for UI/UX work (landing pages, dashboards, reviews, design systems) **or** UI testing/QA (Playwright, state coverage, a11y smoke). After a reload, the agent should load these skills. For test planning, see `.cursor/skills/ui-ux-pro-max/references/ui-testing.md`.

## Provenance

See [`SOURCE.md`](SOURCE.md) for upstream commit, install method, and how to refresh.

## License

Upstream content is under the MIT license from the original project. See skill headers / upstream repo for details.
