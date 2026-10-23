# Source / provenance

## Upstream

| Field | Value |
|-------|-------|
| Cloned from | https://github.com/vorkr/ui-ux-pro-max-skill.git |
| Upstream project | [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) |
| CLI package | `ui-ux-pro-max-cli` **2.5.0** |
| Git commit at install | `d14efff` (`docs: replace disabled Discussion links and the 404 stack clone URL`) |
| Install target | Cursor (`.cursor/skills/`) |
| Install command | `uipro init --ai cursor --force` (built from the cloned `cli/`) |

## What is checked in

Only the **Cursor-installed skill tree** under `.cursor/skills/`, plus this repo’s README/SOURCE. The full upstream monorepo (gallery, CLI source, CI, multi-language READMEs) is **not** vendored here.

### Lean pack (space)

Kept runtime data + scripts needed for search / design-system. **Removed** from the local install (reclaim ~2MB vs full `uipro init`):

- All `scripts/tests/` trees and fixtures
- `__pycache__` / `*.pyc`
- Upstream-only catalogs: `phosphor-icons-upstream.json`, `google-font-licenses.json`, `data-provenance.json`, `catalog-summary.json`
- `scripts/validate_data.py` (upstream CI validator)

`google-fonts.csv` and `icons.csv` stay — they back `--domain google-fonts` / `icons`. Re-running a full `uipro init --force` will restore the heavy catalogs; delete them again if you want to stay lean.

## Refresh / re-install

```bash
git clone https://github.com/vorkr/ui-ux-pro-max-skill.git /tmp/ui-ux-pro-max-skill
cd /tmp/ui-ux-pro-max-skill/cli && npm ci && npm run build
cd /path/to/this/repo
node /tmp/ui-ux-pro-max-skill/cli/dist/index.js init --ai cursor --force
```

Then update this file with the new commit SHA and CLI version, commit, and push.

## Local refinements

- Project README tailored for Cursor-only local use
- Provenance tracked in this file
- Paths in `ui-ux-pro-max/SKILL.md` use `.cursor/skills/...` (installer default)
- UI testing / QA workflow: `.cursor/skills/ui-ux-pro-max/references/ui-testing.md` (state matrix, web checklist, search→assertion mapping, GitHub sync notes)
- Skill frontmatter/description extended to trigger on UI tests, Playwright/RTL/Cypress, and empty/loading/error coverage
