# HGV Cursor Skills Catalog

Project skills for Hilton Grand Vacations (HGV) app development. Cursor loads folders under `.cursor/skills/` that contain a `SKILL.md` with `name` + `description` frontmatter.

**Lean policy:** install `SKILL.md` plus essential references/scripts only. Do not vendor upstream monorepos, test fixtures, screenshots, `node_modules`, or large catalog dumps. See `SOURCE.md`.

## Workflow (recommended)

`research → plan → implement → audit → polish → ship`

| Phase | Skills |
|-------|--------|
| Research / flush-out | `grill-me`, `brainstorming` |
| Plan | `writing-plans`, (`improve-codebase-architecture` + `codebase-design` when shaping seams) |
| Implement | `executing-plans`, `tdd`, `react-best-practices` |
| Audit | `code-review`, `security-review`, `systematic-debugging` |
| Polish (UI) | Existing `ui-ux-pro-max` suite + `frontend-design` + `web-design-guidelines` |
| Ship | `verification-before-completion`, `release-readiness`; deps: `updating-npm-package` |

Invoke explicitly with `/skill-name` in Agent chat when you want a specific playbook.

---

## A) UI / design suite (pre-existing)

| Skill | Category | Source | License | When to use | Path | Size notes |
|-------|----------|--------|---------|-------------|------|------------|
| `ui-ux-pro-max` | polish / UI | [vorkr/ui-ux-pro-max-skill](https://github.com/vorkr/ui-ux-pro-max-skill) @ `d14efff` (upstream UI/UX Pro Max 2.5.0) | MIT | Design intelligence search, stacks, UX guidelines, UI testing reference | `.cursor/skills/ui-ux-pro-max/` | Lean: runtime CSV + scripts; no test fixtures / license dumps |
| `ui-styling` | polish / UI | same pack | MIT | Tailwind / shadcn styling patterns | `.cursor/skills/ui-styling/` | Lean |
| `design` | polish / UI | same pack | MIT | Design routing (logo, CIP, slides, banners) | `.cursor/skills/design/` | Lean |
| `design-system` | polish / UI | same pack | MIT | Tokens / slide DS generation | `.cursor/skills/design-system/` | Lean |
| `brand` | polish / UI | same pack | MIT | Brand voice, assets, consistency | `.cursor/skills/brand/` | Lean |
| `banner-design` | polish / UI | same pack | MIT | Promo / banner layouts | `.cursor/skills/banner-design/` | Lean |
| `slides` | polish / UI | same pack | MIT | Presentation layouts | `.cursor/skills/slides/` | Lean |

---

## B) Engineering suite (added this curation)

| Skill | Category | Source | Commit / tag | License | When to use | Path | Size notes |
|-------|----------|--------|--------------|---------|-------------|------|------------|
| `grill-me` | research / product discovery | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/productivity/grilling` | `5beb35b` | MIT | Stress-test a fuzzy idea or plan before writing specs/code | `.cursor/skills/grill-me/` | ~8K; adapted from `grilling` |
| `brainstorming` | research / design flush-out | [obra/superpowers](https://github.com/obra/superpowers) `skills/brainstorming` | `2ce8aa1` (v7.0.1) | MIT | Clarify requirements, pick an approach, write a design doc | `.cursor/skills/brainstorming/` | ~92K; description softened so it does not MUST-fire on every UI tweak |
| `writing-plans` | planning / task breakdown | [obra/superpowers](https://github.com/obra/superpowers) `skills/writing-plans` | `2ce8aa1` | MIT | Turn an approved spec into bite-sized TDD tasks | `.cursor/skills/writing-plans/` | ~16K; plans default under `docs/superpowers/plans/` |
| `executing-plans` | planning / implementation | [obra/superpowers](https://github.com/obra/superpowers) `skills/executing-plans` | `2ce8aa1` | MIT | Execute a written plan task-by-task in-session | `.cursor/skills/executing-plans/` | ~36K; cross-refs remapped to local skill names |
| `tdd` | coding / TDD | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/engineering/tdd` | `5beb35b` | MIT | Vertical-slice red→green→refactor (not bulk test-first) | `.cursor/skills/tdd/` | ~16K + `tests.md` / `mocking.md` |
| `codebase-design` | coding / architecture | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/engineering/codebase-design` | `5beb35b` | MIT | Shared vocabulary: module depth, seams, adapters | `.cursor/skills/codebase-design/` | ~20K; companion to improve-codebase-architecture |
| `improve-codebase-architecture` | coding / architecture | [mattpocock/skills](https://github.com/mattpocock/skills) `skills/engineering/improve-codebase-architecture` | `5beb35b` | MIT | Find deepening opportunities; HTML report then grill | `.cursor/skills/improve-codebase-architecture/` | ~20K |
| `code-review` | auditing / code review | [getsentry/skills](https://github.com/getsentry/skills) `skills/code-review` | `d12f250` | Apache-2.0 | PR / diff review: correctness, perf, security, tests | `.cursor/skills/code-review/` | ~8K |
| `security-review` | auditing / security | [getsentry/skills](https://github.com/getsentry/skills) `skills/security-review` | `d12f250` | Apache-2.0 (+ OWASP refs CC BY-SA 4.0) | High-confidence vulnerability review (injection, XSS, authZ, …) | `.cursor/skills/security-review/` | ~280K with language/reference guides; `SPEC.md` omitted |
| `systematic-debugging` | testing / QA | [obra/superpowers](https://github.com/obra/superpowers) `skills/systematic-debugging` | `2ce8aa1` | MIT | Reproduce → isolate → hypothesize → verify | `.cursor/skills/systematic-debugging/` | ~36K lean (meta test-pressure docs stripped) |
| `verification-before-completion` | testing / production readiness | [obra/superpowers](https://github.com/obra/superpowers) `skills/verification-before-completion` | `2ce8aa1` | MIT | Evidence gate before claiming done | `.cursor/skills/verification-before-completion/` | ~8K |
| `web-design-guidelines` | polish / UI (audit) | [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) `skills/web-design-guidelines` | `063bee9` | MIT | Audit UI vs Vercel Web Interface Guidelines (a11y/UX/perf) | `.cursor/skills/web-design-guidelines/` | ~8K; fetches live guidelines URL |
| `frontend-design` | polish / UI (create) | [anthropics/skills](https://github.com/anthropics/skills) `skills/frontend-design` | `dbd4588` | Apache-2.0 | Distinctive production UI that avoids generic AI aesthetics | `.cursor/skills/frontend-design/` | ~28K; complementary to ui-ux-pro-max (not a duplicate catalog) |
| `react-best-practices` | coding / production React | [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) `skills/react-best-practices` | `063bee9` | MIT | React/Next performance rules (waterfalls, bundles, RSC) | `.cursor/skills/react-best-practices/` | ~120K lean: `SKILL.md` + compiled `AGENTS.md` (no per-rule tree) |
| `updating-npm-package` | upgrade / migration | [spencerpauly/awesome-cursor-skills](https://github.com/spencerpauly/awesome-cursor-skills) `resources/updating-npm-package` | `99cd265` | CC0-1.0 | Safe npm bump; major versions require migration report | `.cursor/skills/updating-npm-package/` | ~8K |
| `release-readiness` | testing / production readiness | [Nordic-AI/production-readiness-skills](https://github.com/Nordic-AI/production-readiness-skills) `skills/release-readiness` | `0105f3d` | Apache-2.0 | CI/CD, deploy strategy, rollback, migrations, flags | `.cursor/skills/release-readiness/` | ~44K incl. vendored `references/CONVENTIONS.md`; GitNexus optional |

Approx. **+748K** for the engineering suite (whole `.cursor/skills/` tree ~3.9M with the UI pack).

---

## Research sources (citations)

Curated lists / marketplaces consulted (not all vendored):

- https://github.com/spencerpauly/awesome-cursor-skills
- https://github.com/VoltAgent/awesome-agent-skills
- https://www.skills.sh/ and https://www.skills.sh/docs (`npx skills add …`; CLI at vercel-labs/skills)
- https://cursor.com/docs/skills
- https://github.com/mattpocock/skills
- https://github.com/obra/superpowers
- https://github.com/vercel-labs/agent-skills
- https://github.com/anthropics/skills
- https://github.com/getsentry/skills
- https://github.com/Nordic-AI/production-readiness-skills
- https://github.com/anthropics/knowledge-work-plugins (engineering code-review noted; Sentry skill preferred for lean install)

## Skills needing API keys / paid services

| Skill | Needs | Notes |
|-------|-------|-------|
| All installed skills above | None for core workflow | Instruction/Markdown (+ local references). No SaaS keys required. |
| `web-design-guidelines` | Network fetch | Pulls https://raw.githubusercontent.com/vercel-labs/web-interface-guidelines/main/command.md — if offline, skip or paste cached guidelines. |
| `release-readiness` | Optional GitNexus MCP | Degrades to Grep/Glob; do not block. |
| Not installed: PostHog / Stripe / Auth.js / Sentry-for-AI setup skills | Provider accounts | Skipped to stay lean and key-free. |
| Not installed: `anthropics/claude-code-security-review` Action | `CLAUDE_API_KEY` | GitHub Action, not a local `SKILL.md` pack. |

## Rejected notables (short)

| Candidate | Why not installed |
|-----------|-------------------|
| Full `obra/superpowers` pack | Too many cross-linked skills; took the SDLC core only |
| Full Nordic production-readiness (12 skills) | Heavy + GitNexus-centric; took `release-readiness` + conventions only |
| Nordic `security-audit` | Overlaps Sentry `security-review`; Sentry pack is self-contained |
| `mattpocock` `to-spec` / `to-tickets` | Needs issue-tracker setup skill; prefer `writing-plans` + local markdown |
| `vercel` `composition-patterns` / `deploy-to-vercel` / `vercel-optimize` | Nice-to-have; React perf + web guidelines cover HGV needs |
| `anthropics` docx/pptx/pdf/xlsx | Document binary generation; out of HGV app focus |
| `Bodymobarez/cursor-skills-master` (440-skill hub) | Too large / noisy for lean project |
| `charlieviettq/awesome-agent-skill` (598 marketplace) | Catalog only; cherry-pick sources instead |
| Paid / account skills (Stripe, PostHog analytics, image gen) | Require API keys; skip until product needs them |
