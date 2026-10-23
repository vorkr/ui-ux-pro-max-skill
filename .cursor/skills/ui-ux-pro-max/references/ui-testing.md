# UI testing & QA workflow

Use this when the user asks to test, review, harden, or verify UI — not only when building new screens. Pair with the search CLI (`--domain ux`, `style`, `web`, `react`, or `--stack`) so test cases track real design guidance.

## When this applies

| Ask | Do |
|-----|-----|
| “Write UI tests” / Playwright / RTL / Cypress | Derive cases from states + a11y outcomes below; match the project’s existing test runner |
| “Review this page” / visual QA | Run focused `ux` searches + Web Visual Checklist; file concrete defects |
| “Check accessibility” | One outcome per search (`keyboard focus modal`, `error summary validation`, …), then assert those behaviors |
| “Cover empty / loading / error” | Use State Matrix; require tests or manual steps for each applicable cell |
| “Regression after a redesign” | Snapshot critical routes + interaction smoke; re-check contrast and reduced-motion |

## Workflow (agent)

1. **Scope** — Identify surfaces (routes/components), stack (from `package.json`), and risk (auth, payments, forms).
2. **Design intel** — For each risk area, run one narrow search, e.g.:
   ```bash
   python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "inline-validation error-clarity" --domain ux
   python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "keyboard focus modal" --domain ux
   python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "form validation" --stack nextjs
   ```
3. **State matrix** — Mark which states exist; skip N/A, never invent states the product does not have.
4. **Tests** — Prefer behavior assertions (role, name, visible text, URL, network) over brittle CSS selectors. Add a11y checks where the stack already uses them (axe, Testing Library queries by role).
5. **Manual pass** — Responsive + keyboard + reduced-motion when automation is thin.
6. **Report** — List pass/fail with file:line or selector; no vague “looks fine.”

## State matrix (minimum)

For each interactive surface, cover what the product actually has:

| State | Assert |
|-------|--------|
| Default / happy path | Primary content and CTA usable |
| Loading | Skeleton/spinner; no layout jump that hides controls; button pending |
| Empty | Clear empty copy + recovery action |
| Error (inline) | Field errors linked to inputs; focus/summary when multi-error |
| Error (page/toast) | Recoverable message; retry if applicable |
| Disabled / unauthorized | Not focusable as active control; reason visible when needed |
| Success | Confirmation without trapping focus |
| Dark / light (if both) | Contrast and borders still readable |
| Reduced motion | No required information only in motion |

## Web visual & interaction checklist

Use for browser UI (desktop + responsive). Native/app rules stay in the main SKILL Pre-Delivery Checklist.

### Layout & responsive
- [ ] No horizontal scroll at 375px and common desktop widths
- [ ] Primary CTA visible without hunting on mobile
- [ ] Sticky headers/footers do not cover focusable content
- [ ] Images/media have intrinsic sizing (no CLS spikes)

### Interaction
- [ ] All clickable elements show pointer/hover/focus affordance
- [ ] Keyboard: Tab order matches reading order; Enter/Space activate controls
- [ ] Modals/dialogs: focus trap, Escape closes, restore focus on dismiss
- [ ] Forms: labels (not placeholder-only); errors announced / associated

### Content & polish
- [ ] No lorem or “Welcome to your app” placeholder copy in shipped UI
- [ ] Icons are vector/consistent; no emoji as structural icons
- [ ] Loading/empty/error copy is specific to the domain

### Accessibility smoke
- [ ] Page has one meaningful `h1` (or equivalent landmark structure)
- [ ] Interactive elements have accessible names
- [ ] Color is not the only error/success signal
- [ ] Contrast: body text ≥4.5:1; UI boundaries that convey state ≥3:1

## Suggested automated coverage (adapt to stack)

Keep tests aligned with repo conventions. Typical layers:

1. **Unit / component** — Testing Library: render states from the matrix; query by role/name.
2. **Interaction** — User-event: open dialog, submit invalid form, keyboard path.
3. **E2E** — Playwright/Cypress: critical journey smoke (load → act → assert URL/text).
4. **A11y** — axe (or equivalent) on key pages; fail on serious/critical when the project already gates on that.

Do **not** add a new test framework if one exists. Do **not** screenshot-diff everything by default — reserve visual baselines for stable marketing/shell chrome when the team already uses them.

## Mapping searches → test ideas

| UX concern | Example search | Example assertion |
|------------|----------------|-------------------|
| Form labels | `"form labels" --domain ux` | `getByLabelText('Email')` |
| Submit feedback | `"submit feedback" --domain ux` | Pending → success/error text |
| Focus modal | `"keyboard focus modal" --domain ux` | Focus inside dialog; restore on close |
| Touch targets | `"touch target" --domain ux` | Hit area ≥44px where touch matters |
| Reduced motion | `"reduced motion" --domain ux` | No required info only in animation |
| List performance | `"virtualize-lists" --domain ux` / `--stack react` | Large lists stay interactive |

## GitHub sync for this skill repo

When refining skills or tests in **this** repository:

1. Edit under `.cursor/skills/...` (and `README.md` / `SOURCE.md` if behavior changes).
2. Sanity-check search still runs: `python3 .cursor/skills/ui-ux-pro-max/scripts/search.py "saas" --domain style -n 1`
3. Commit with a message that states *why* (UI test workflow, checklist, etc.).
4. `git push -u origin <branch>` (default branch is `main` unless working on a feature branch).
5. Note material refinements in `SOURCE.md` → Local refinements.

Do not vendor the full upstream monorepo unless explicitly asked. Prefer re-running `uipro init --ai cursor --force` (see `SOURCE.md`) when pulling a new upstream skill version, then re-apply local reference files if the installer overwrites them.
