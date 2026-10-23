# HGV Cursor Skills

Local Cursor Agent skills for Hilton Grand Vacations (HGV) app development. Skills live under `.cursor/skills/` and are discovered automatically.

This repo stays **lean**: `SKILL.md` + essential references/scripts only — no upstream monorepos, test fixtures, or large catalog dumps. Full catalog: [`SKILLS.md`](SKILLS.md). Provenance: [`SOURCE.md`](SOURCE.md).

## Suites

| Suite | Skills | Role |
|-------|--------|------|
| UI / UX Pro Max | `ui-ux-pro-max`, `ui-styling`, `design`, `design-system`, `brand`, `banner-design`, `slides` | Design intelligence, tokens, brand, slides |
| Engineering | `grill-me`, `brainstorming`, `writing-plans`, `executing-plans`, `tdd`, `codebase-design`, `improve-codebase-architecture`, `code-review`, `security-review`, `systematic-debugging`, `verification-before-completion`, `web-design-guidelines`, `frontend-design`, `react-best-practices`, `updating-npm-package`, `release-readiness` | Research → plan → build → audit → ship |

## Recommended workflow

**research → plan → implement → audit → polish → ship**

1. **Research** — `/grill-me` or `/brainstorming` to flush out requirements and design
2. **Plan** — `/writing-plans` (optionally `/improve-codebase-architecture` for seams)
3. **Implement** — `/executing-plans` with `/tdd`; apply `/react-best-practices` on React/Next work
4. **Audit** — `/code-review`, `/security-review`; `/systematic-debugging` when stuck
5. **Polish** — `ui-ux-pro-max` search + `/frontend-design` + `/web-design-guidelines`
6. **Ship** — `/verification-before-completion`, `/release-readiness`; `/updating-npm-package` for deps

## UI Pro Max quick use

Requires Python 3 (stdlib only). No npm install to *use* the search scripts.

```bash
python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "saas fintech dashboard" --design-system -p "Acme Ops"
python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "glassmorphism" --domain style -n 5
python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "form validation" --stack nextjs
python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "keyboard focus modal" --domain ux
```

UI testing / QA reference: `.cursor/skills/ui-ux-pro-max/references/ui-testing.md`.

## License

Mixed permissive licenses per upstream (MIT, Apache-2.0, CC0, OWASP CC BY-SA for security reference material). See each skill folder and `SKILLS.md` / `SOURCE.md`.
