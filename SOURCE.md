# Source / provenance

## Lean-install rules (always)

1. Prefer a single skill folder: `SKILL.md` + essential `references/` / `scripts/` only.
2. Do **not** vendor entire upstream monorepos, galleries, CLI trees, CI, or multi-agent copies.
3. Strip before commit: `node_modules/`, `__pycache__/`, `*.pyc`, `scripts/tests/`, fixtures, screenshots, `*.zip`, unused license dump JSON.
4. Adapt Claude-only paths (`.claude/skills`) → `.cursor/skills/`; ensure frontmatter `name` matches the folder name.
5. Remap cross-skill refs (`superpowers:foo`) to local skill names installed in this repo.
6. Record source URL + commit/tag + license in this file and in `SKILLS.md`.
7. No secrets in skill content; example credentials in security refs must stay clearly fictional (e.g. AWS `AKIA…EXAMPLE`).

---

## Upstream A — UI/UX Pro Max (existing)

| Field | Value |
|-------|-------|
| Cloned from | https://github.com/vorkr/ui-ux-pro-max-skill.git |
| Upstream project | [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) |
| CLI package | `ui-ux-pro-max-cli` **2.5.0** |
| Git commit at install | `d14efff` |
| Install target | Cursor (`.cursor/skills/`) |
| Install command | `uipro init --ai cursor --force` (built from the cloned `cli/`) |
| Skills | `ui-ux-pro-max`, `ui-styling`, `design`, `design-system`, `brand`, `banner-design`, `slides` |

### Lean pack (space)

Kept runtime data + scripts needed for search / design-system. **Removed** vs full `uipro init` (~2MB reclaim):

- All `scripts/tests/` trees and fixtures
- `__pycache__` / `*.pyc`
- Upstream-only catalogs: `phosphor-icons-upstream.json`, `google-font-licenses.json`, `data-provenance.json`, `catalog-summary.json`
- `scripts/validate_data.py` (upstream CI validator)

### Refresh / re-install

```bash
git clone https://github.com/vorkr/ui-ux-pro-max-skill.git /tmp/ui-ux-pro-max-skill
cd /tmp/ui-ux-pro-max-skill/cli && npm ci && npm run build
cd /path/to/this-repo
node /tmp/ui-ux-pro-max-skill/cli/dist/index.js init --ai cursor --force
# then re-strip heavy catalogs / tests; update this file
```

---

## Upstream B — Engineering skill pack (2026-10-23 curation)

Sparse-cloned source repos, copied only selected skill folders into `.cursor/skills/`. Catalog: [`SKILLS.md`](SKILLS.md).

| Source repo | Commit | License | Skills installed |
|-------------|--------|---------|------------------|
| [mattpocock/skills](https://github.com/mattpocock/skills) | `5beb35b` | MIT | `grill-me` (from `grilling`), `tdd`, `codebase-design`, `improve-codebase-architecture` |
| [obra/superpowers](https://github.com/obra/superpowers) | `2ce8aa1` (v7.0.1) | MIT | `brainstorming`, `writing-plans`, `executing-plans`, `systematic-debugging`, `verification-before-completion` |
| [getsentry/skills](https://github.com/getsentry/skills) | `d12f250` | Apache-2.0 | `code-review`, `security-review` (OWASP cheat-sheet refs: CC BY-SA 4.0; keep skill `LICENSE`) |
| [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) | `063bee9` | MIT | `web-design-guidelines`, `react-best-practices` (lean: `SKILL.md` + `AGENTS.md` only) |
| [anthropics/skills](https://github.com/anthropics/skills) | `dbd4588` | Apache-2.0 | `frontend-design` (keep `LICENSE.txt`) |
| [spencerpauly/awesome-cursor-skills](https://github.com/spencerpauly/awesome-cursor-skills) | `99cd265` | CC0-1.0 | `updating-npm-package` |
| [Nordic-AI/production-readiness-skills](https://github.com/Nordic-AI/production-readiness-skills) | `0105f3d` | Apache-2.0 | `release-readiness` + `references/CONVENTIONS.md` |

### Local adaptations

- `grill-me`: content from mattpocock `grilling`; folder/frontmatter renamed for `/grill-me`.
- `brainstorming`: description softened (no global MUST) so it coexists with the UI suite; `superpowers:writing-plans` → `writing-plans`.
- `writing-plans` / `executing-plans` / `systematic-debugging`: remapped `superpowers:*` cross-refs to installed local names (`tdd`, `code-review`, `verification-before-completion`, etc.).
- `improve-codebase-architecture`: points at sibling `.cursor/skills/codebase-design/SKILL.md`.
- `react-best-practices`: renamed from `vercel-react-best-practices`; per-rule `rules/` tree omitted (~296K saved); use compiled `AGENTS.md`.
- `security-review`: omitted upstream `SPEC.md`; kept `references/`, `languages/`, `LICENSE`.
- `release-readiness`: conventions vendored under skill `references/`; GitNexus noted as optional.

### Approx. size

- Engineering suite added: **~748K**
- Full `.cursor/skills/` after install: **~3.9M**
- `systematic-debugging`: stripped upstream meta (`CREATION-LOG`, `test-pressure-*`, example TS)

---

## Local refinements (UI pack)

- Project README + this provenance file
- Paths in `ui-ux-pro-max/SKILL.md` use `.cursor/skills/...`
- UI testing / QA workflow: `.cursor/skills/ui-ux-pro-max/references/ui-testing.md`
- Skill frontmatter extended to trigger on UI tests / Playwright / state coverage
