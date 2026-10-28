# PRM/CRM Ecosystem Audit & Remediation Playbook

A single-source-of-truth method and master prompt for retroactively auditing, fixing, and productizing an existing enterprise PRM/CRM (ASP.NET Core / .NET 10, React) as a configurable, self-service, intercompany ecosystem.

## 0. How to use this document

1. Paste **Section 1** as the standing system prompt for every audit session.
2. Run work in the **Section 8 cycle**, one slice at a time.
3. Every finding lives in the **Section 7 ledger**. Nothing is fixed without a ledger ID.
4. Docs are maintained per **Section 9**. This playbook is the index root.

Framing rule: everything is a *suspected gap* until evidence (code, schema, config, running behavior) confirms it. Competitor claims (Unifyr, Kiflo) are vendor marketing until verified in a demo or documentation.

## 1. Master prompt (paste-in)

**Role.** Act as a principal PRM/CRM product architect, channel-operations expert, and independent software auditor, with PhD-level depth in C#, ASP.NET Core on .NET 10, domain-driven design, and React front ends.

**Mission.** The product is already built. Retroactively audit it for production-release readiness across architecture, channel rules, configuration inheritance, intercompany collaboration, navigation and role design, user journeys, content publishing, RFQ/RFP handling, and AI features. Fix what is wrong, in small traceable increments, without drift.

**Rules of engagement.**

- Domain model first. Inspect and, where needed, correct aggregates, value objects, invariants, and bounded contexts before touching UI or endpoints.
- Evidence over assumption. Every finding cites a file, class, table, config key, route, or observed behavior. If evidence is missing, list what must be inspected. Do not declare a gap confirmed.
- Classify every source: *verified product behavior*, *vendor claim*, *industry practice*, *my recommendation*.
- Classify every remedy: *configuration*, *incremental development*, *integration work*, *foundational redesign*.
- One slice at a time. State scope, files in play, and acceptance tests before changing anything. Stop at the slice boundary.
- Never weaken tenant isolation, authorization, or audit logging to ship a feature.
- Update the ledger, the affected docs, and the lessons-learned log at the end of every slice.
- Prefer existing code and patterns. Refactor only when a finding justifies it.

**Technical standards.**

- Backend: .NET 10, ASP.NET Core, clean/hexagonal layering, rich domain models, EF Core with explicit configurations and migrations, policy-based and resource-based authorization, global tenant query filters, domain events, outbox pattern for integrations, ProblemDetails errors, OpenTelemetry, structured logging, idempotent commands.
- Frontend: React with TypeScript, a single design-token system, accessible (WCAG 2.2 AA) component library. Match the chosen reference system (Google Material 3, Salesforce Lightning, or Adobe Spectrum) for spacing, type scale, elevation, density, and states. Pick one and document it; do not blend.
- Contract: OpenAPI generated from the backend, typed client generated for React, so UI and API cannot drift.
- Testing: domain unit tests, API integration tests, authorization matrix tests, Playwright journey tests.

**Outputs per slice.** Findings (ledger format), proposed change set, tests, doc updates, residual risks, next slice.

## 2. Eight-channel baseline

These are **eight configurable operating-model templates**, not eight company types. One organization can hold several roles across different relationships. "Vendor/supplier" is a relationship role, not a sales channel.

| # | Channel template | Core motion | Rules the audit must find defined |
| --- | --- | --- | --- |
| 1 | Referral partners and agents | Refer, vendor closes | Lead submission, attribution window, referral fee, duplicate handling |
| 2 | Affiliates and advocates | Promote via tracked links or codes | Tracking, fraud controls, payout thresholds, content approval |
| 3 | Resellers and VARs | Buy and resell, often with services | Deal registration, pricing and discount authority, quoting, protection and expiry |
| 4 | Distributors and aggregators | Tiered distribution | Sub-partner hierarchy, stock or entitlement, rebates, territory |
| 5 | Service, implementation, agency partners | Deliver projects | Certification, capacity, SOW, delivery milestones, subcontracting |
| 6 | Managed service providers | Recurring operated service | Metered or tenant-level billing, SLAs, multi-customer administration |
| 7 | Technology, ISV, OEM partners | Integrate, bundle, embed | Technical onboarding, listing, licensing, joint solutions, revenue share |
| 8 | Strategic alliance and co-sell | Joint go-to-market | Multi-party opportunities, joint plans, contribution credit, executive sponsorship |

For each template, the audit fills one **capability and rule card**: eligibility, onboarding, personas, permissions, commercial terms, deal flow, incentives, delivery, renewals and expansion, reporting, and exit.

## 3. Configuration inheritance specification

Model **three separate structures** and link them without conflating them:

1. **Configuration hierarchy** (rule inheritance).
2. **Distribution hierarchy** (who sells through whom).
3. **Collaboration network** (who may work with whom, by consent).

**Precedence chain, lowest to highest authority:**

```
Platform defaults + mandatory controls (non-overridable)
  -> Tenant / enterprise policy
    -> Channel operating-model template
      -> Partner program
        -> Partner enrollment / agreement
          -> Approved exception (time-boxed)
```

Geography, industry, tier, product, and campaign are **ordered policy overlays**, not subclasses. Example: Reseller template + North America overlay + Gold tier + product-family pricing + one approved exception.

**Design requirements to test:**

- Every setting is classified *locked*, *tighten-only*, or *overridable*. Security, tenant isolation, audit, and consent settings are locked.
- Resolution is deterministic, with documented tie-break rules and a pure, unit-tested resolver in the domain layer.
- Every resolved value exposes origin layer, version, effective dates, override reason, and approver (the "why did this rule apply?" explanation).
- Template changes are versioned, with an impact preview of affected partners and a staged rollout. Existing agreements pin a version until migrated.
- Exceptions expire and are reported.

**Likely code smells to look for:** rules hard-coded in controllers or UI, `if (partnerType == ...)` branching, copied configuration per partner, enum-driven behavior, settings tables with no scope or version column.

## 4. Intercompany collaboration layer

Goal: partners create value with each other, not only with the vendor, through bounded self-service.

| Capability | Default posture |
| --- | --- |
| Partner discovery by capability, region, certification, industry | Opt-in profile fields; hidden by default |
| Consent-based introductions | Both sides approve; revocable |
| Capacity and capability requests (workforce) | Company-to-company, role/skill level; no identifiable employees or internal schedules unless the employer authorizes |
| Partner-to-partner referrals and co-sell | Shared opportunity with defined roles and split rules |
| Joint solutions and bundles | Co-authored listing, approved by both |
| Co-marketing and shared campaigns | Approved claims, shared assets, attribution to each party |
| Shared projects and deliverables | Scoped workspace, expiring access |
| Marketplace or catalog of capabilities, products, case studies | Publisher controls visibility and approval state |

Governance requirements: relationship agreement entity, consent records, scoped permissions, confidentiality terms, withdrawal and termination that revoke access without deleting history, dispute process, and full audit trail.

**Multi-party attribution:** keep separate records for *contribution* (sourced, influenced, transacted, implemented, renewed), *revenue credit*, *commission eligibility*, and *payment obligation*.

**Channel conflict:** duplicate registration, direct-sales overlap, competing partners, protection expiry, and arbitration must each be explicit rules.

## 5. Navigation, roles, personas, and journeys

**Principle:** navigation is *generated from capabilities*, not hand-built per role. Each menu item is a registered module with a required permission, entitlement (plan), channel applicability, and feature flag.

**Role model: "company hats."** A user holds one or more roles within each organization they belong to, and switches context explicitly. Roles are composed of permissions; permissions are enforced server-side first and mirrored in the UI.

Audit deliverables:

1. **Module registry:** every page and route, with permission, entitlement, channel, and owner.
2. **Role x permission x module matrix**, with tests that prove denied access is denied by the API, not only hidden in the UI.
3. **Persona catalog per channel** (for example: partner admin, sales rep, deal desk, delivery lead, marketing manager, finance contact, vendor channel manager, vendor admin, platform operator).
4. **End-to-end journey map per persona** from invitation to first value to renewal, covering: onboarding, profile and capability publishing, deal registration, quote, co-sell, delivery, campaign, payout, offboarding.
5. **Page-level reconnaissance:** orphan pages, dead links, duplicated screens, inconsistent patterns, missing empty/error/loading states, accessibility failures, and visual deviations from the design system (pixel-level comparison against tokens, not by eye).
6. **Journey tests:** one Playwright test per critical journey, per role.

## 6. Content, RFQ/RFP, and AI

**Publishing:** partners and customers publish capabilities, case studies, campaigns, and solutions through a draft, review, approve, publish, retire lifecycle, with versioning, claim approval, visibility scopes, and moderation.

**RFQ/RFP workflow:** intake (upload, email, or portal), parsing into requirements, qualification and bid/no-bid, assignment, answer drafting from an approved knowledge base, SME review, compliance check, submission, outcome capture, and lessons learned. Partners can be invited to respond jointly.

**AI controls to audit:**

- Retrieval respects tenant, relationship, and consent boundaries (no cross-tenant leakage in retrieval, prompts, caches, or logs).
- Answers cite approved sources; unsupported claims are flagged.
- Human approval gates before anything is sent externally.
- Prompt, source, model version, and approver are logged for every generated artifact.
- Evaluation set for RFP answer quality; PII and confidential-data handling; cost and rate limits per tenant; entitlement-gated features.
- Recommendations (partner matching, next-best-action) are explainable and suppressible.

## 7. Audit ledger (the traceability backbone)

One register (a table in the repo, or an `audit_findings` table with the same columns). Never delete rows; supersede them.

| Field | Meaning |
| --- | --- |
| ID | `AUD-<area>-<nnn>` (areas: ARCH, DOM, CFG, CHN, COL, NAV, JRN, SEC, DATA, INT, AI, UX, OPS) |
| Title, Area, Channel(s), Persona(s) | Scope |
| Condition | What exists today |
| Criteria | What should exist, and the source |
| Evidence | File paths, queries, screenshots, test output |
| Evidence class | Verified, suspected, vendor claim |
| Cause, Impact | Root cause; business and technical effect |
| Severity | Blocker, High, Medium, Low (with defined criteria) |
| Remedy type | Configuration, incremental dev, integration, redesign |
| Owner, Slice | Responsible person; slice where it is fixed |
| Acceptance test | Test ID or script that proves the fix |
| Status | Open, Confirmed, In progress, Fixed, Verified, Won't fix, Superseded |
| Lessons learned | Pattern worth carrying forward |
| Links | PR, migration, doc sections, related IDs |

**Runtime audit trail (product feature, not just process).** The application itself needs an append-only, tenant-scoped event log: actor, acting-as role/organization, action, target, before and after, correlation ID, reason, and source (UI, API, integration, AI). It must support trace, reconcile, replay, and troubleshooting, and must be queryable from an admin screen. Audit this as its own finding area.

## 8. Iterative, drift-free work cycle

**Phase A: Inventory (read-only).** Solution map, bounded contexts, entity and table catalog, route and page catalog, permission catalog, integration catalog, configuration catalog, test coverage. Output: baseline docs and an initial ledger.

**Phase B: Foundations.** Tenant isolation, authorization model, organization and relationship model, audit event log, configuration resolver, entitlements, design tokens, generated API client.

**Phase C: Core PRM.** Eight channel templates, onboarding, deal registration, quoting, incentives, conflict rules, reporting, CRM and ERP integration with systems of record defined.

**Phase D: Experience.** Module-driven navigation, role matrix, persona journeys, accessibility, visual fidelity.

**Phase E: Ecosystem differentiators.** Discovery, consent, capacity exchange, shared opportunities, joint solutions, co-marketing, content publishing, RFQ/RFP with AI.

**Phase F: Release hardening.** Performance, security review, backup and recovery, observability, migration rehearsal, documentation freeze, go-live checklist.

**Every slice follows the same eight steps:**

1. **Scope:** pick ledger IDs; define in/out of scope and acceptance tests.
2. **Re-read:** load the current docs and the relevant code (do not rely on memory of earlier sessions).
3. **Verify:** confirm or reject each suspected finding with evidence.
4. **Design:** domain model change first; note migration and backward compatibility.
5. **Implement:** smallest coherent change set.
6. **Prove:** run unit, integration, authorization, and journey tests.
7. **Record:** update ledger, docs, decision records, and lessons learned.
8. **Review for drift:** compare the result against the baseline principles in Section 1; open new findings rather than silently expanding scope.

**Anti-drift controls:** a slice cap (about one aggregate or one journey), no unrelated refactors, a "parking lot" for new ideas, a written decision record for anything architectural, and a periodic re-baseline (every few slices) to reconcile docs with code.

## 9. Documentation as a single source of truth

```
/docs
  00-index.md                  (this playbook's map, status dashboard)
  01-vision-and-scope/
  02-architecture/             (context map, layers, ADRs)
  03-domain-model/             (aggregates, invariants, glossary)
  04-channels/                 (eight capability and rule cards)
  05-configuration/            (inheritance spec, setting catalog)
  06-collaboration/            (consent, relationships, attribution)
  07-roles-nav-journeys/       (module registry, role matrix, personas)
  08-content-rfp-ai/
  09-integrations/             (systems of record, sync rules)
  10-audit/                    (ledger, findings, lessons-learned)
  11-quality/                  (test strategy, acceptance tests)
  12-operations/               (runbooks, release checklist)
  99-archive/                  (superseded, never deleted)
```

Rules: one canonical home per fact; other docs link, never copy. Every doc has an owner, last-verified date, and status. Superseded documents move to the archive with a pointer. Glossary terms are defined once and used consistently (organization, relationship, program, enrollment, role, persona, overlay, exception).

## 10. Gaps and trends added to your original brief

- **Roles vs. company types:** an organization holds multiple roles across relationships; avoid a single "partner type" field.
- **Three separate structures** (configuration, distribution, collaboration) instead of one hierarchy.
- **Consent and revocation** as first-class domain concepts.
- **Contribution vs. credit vs. commission vs. payment** modeled separately.
- **Entitlement-driven packaging** so ecosystem features are sold as plans without forking code.
- **Ecosystem-led growth trends:** partner marketplaces, co-sell between partners, capability graphs, shared pipeline visibility, partner-influenced attribution, and AI for matching and response drafting.
- **Systems of record** declared per entity (customer, opportunity, product, invoice, commission), with conflict handling.
- **Verify competitors in demos:** check Unifyr and Kiflo for documented, partially documented, not found, or needs-demo status per capability; do not infer parity.

## 11. Definition of production-ready

- No open Blocker or High findings; Mediums have dated plans.
- All eight templates have rule cards and passing acceptance tests.
- Configuration resolver is tested and explains every resolved value.
- Authorization matrix tests pass; tenant isolation is verified with adversarial tests.
- Every nav item traces to a registered module, permission, and entitlement.
- Critical journeys pass end-to-end for every persona.
- Audit log answers "who did what, as whom, why, and what changed" for any record.
- Docs reconcile with code at the release tag.
- Rollback, backup/restore, and monitoring are rehearsed.

## 12. Three scenarios to run as acceptance journeys

1. **Reseller-led sale with implementation support:** reseller registers a deal, receives an overlay-driven price and discount, subcontracts delivery to a service partner via a shared opportunity, and attribution is split into sourced, transacted, and implemented contributions.
2. **Partner-to-partner workforce collaboration:** a service partner publishes capacity; another partner requests it; employers authorize, scope, and contract it; delivery is tracked; access is revoked at the end while history is retained.
3. **Joint campaign producing a shared upsell opportunity:** an ISV and an MSP co-author a campaign with approved claims, leads are attributed to both, and an expansion opportunity is created in the customer's lifecycle with a defined split.

# Addendum A: Reconciliation, Multi-Hat Organizations, Work Inbox, Data Model, and Integration

This addendum extends Sections 1 to 12. It adds a **Phase 0 (reconcile)** in front of Phase A and answers the open design questions: multi-hat companies, franchise and master-license structures, a unified work inbox, the 265+ table object model, governance, Activepieces alongside Stateless, and custom skills.

## 13. Phase 0: Discover, clean up, and reconcile documentation and features

Goal: one verified truth about what the product *actually does today*, what is documented, and what is differentiating.

**Steps (read-only until reconciled):**

1. **Harvest everything:** READMEs, wikis, old ADRs, tickets, slide decks, API docs, release notes, comments, seed data, config files, and UI copy.
2. **Extract the real feature inventory from code:** routes and controllers, handlers/commands, EF entities and configurations, permission constants, feature flags, background jobs, workflow definitions, integration endpoints, and React pages/components.
3. **Reconcile in a 3-way matrix:** *Documented* vs *Implemented* vs *Reachable by a user* (a nav path, a role that can use it, an entitlement that unlocks it).
4. **Classify each feature:** Working and documented, working but undocumented, documented but missing, partial, dead/orphaned, duplicate, experimental.
5. **Archive, do not delete:** move stale docs to `/docs/99-archive` with a pointer to the replacement.
6. **Publish the feature register** into the ledger area (`10-audit`) with a stable ID per feature (`FEA-<area>-<nnn>`).

**Innovation and differentiator register** (one page, owned by product leadership):

| Field | Purpose |
| --- | --- |
| Capability | What it is, in one sentence |
| Customer problem solved | Why it matters |
| Evidence it exists | Code path, demo script, test |
| Competitor status | Documented / partial / not found / needs demo (Unifyr, Kiflo, others) |
| Defensibility | Data, network effect, workflow depth, integration, AI |
| Packaging | Plan or entitlement that sells it |
| Maturity | Concept, built, hardened, production |

Likely differentiators to test (hypotheses, not claims): multi-hat organizations, relationship-scoped collaboration, consent-based partner network, unified work inbox, configuration inheritance with explainability, RFQ/RFP response with permission-aware AI, and master-license franchise structures.

## 14. Multi-hat organizations: roles are relationships, not company types

**Answer:** do *not* segregate by channel. A single organization record can wear several hats at once (vendor, solution provider, reseller, customer of another company). The hat is a property of a **relationship**, not of the organization.

**Core domain concepts:**

- **Organization:** one legal/operating entity, one record, one identity, regardless of how many programs or roles it has.
- **Relationship:** a directed link between two organizations (`Provider -> Consumer`) with a *relationship type* (for example, vendor-to-reseller, reseller-to-customer, partner-to-partner, master-to-subsidiary), an agreement, a status, and effective dates.
- **Hat (organization role in a relationship):** what an organization is *in that relationship*: Vendor, Reseller, Distributor, Service Partner, MSP, ISV, Customer, and so on. Hats are rows, not enums on the organization.
- **Program enrollment:** the organization joins a program under a channel template; the enrollment is where inherited configuration is pinned.
- **Membership:** a user belongs to an organization with one or more *internal roles* (admin, sales, finance).
- **Acting context:** the user's current selection of *(organization, hat, relationship or program)*. Authorization, navigation, inbox, and reporting are evaluated against the acting context.

**Example:** Acme is a vendor to resellers (Hat: Vendor in relationship A), a customer of CloudCo (Hat: Customer in relationship B), and a services partner for Globex (Hat: Service Partner in relationship C). One Acme record; three relationships; the user switches context from a header selector, and every page, count, and permission follows it.

**Data boundary rules (non-negotiable):**

- Data is owned by an organization and *shared into* a relationship by explicit scope, never by hat alone.
- What Acme sees as a customer of CloudCo must not leak into what Acme shows its own resellers.
- A person who works for two organizations holds two memberships, and the acting context separates them. Never merge their data silently.
- The same record never needs duplicating to serve two hats; relationship-scoped views and permissions do the work.

**Audit tests:** duplicate organization detection and merge; same org in multiple programs; role escalation via hat switching; cross-relationship data leakage; reports that double count an org holding two hats; invitation flows that create a second record for an existing org.

## 15. Franchises, master licenses, and sub-company structures

This is a fourth structure, alongside configuration, distribution, and collaboration: the **ownership / licensing tree**.

```
Master organization (master license holder)
  -> Region or division (optional grouping)
    -> Sub-company / franchisee / affiliate
```

**Concepts:**

- **License pool:** the master purchases entitlements (plan, seats, modules, AI credits, storage, sub-company slots). Sub-companies *draw from* the pool; allocations and caps are explicit.
- **Delegated administration:** the master can administer, or can delegate to, sub-companies. Each delegated power is a named permission with scope (all descendants, one branch, one sub-company).
- **Policy inheritance:** the master sets a policy baseline (branding, templates, content, pricing guardrails, compliance). Sub-companies inherit through the same configuration chain, with *locked / tighten-only / overridable* classification.
- **Data visibility:** default is *sub-company data stays with the sub-company*; the master sees roll-ups and anything explicitly shared up. Define this per data class (reports, pipeline, customers, financials, personal data).
- **Join lifecycle:** invite or request, verify, accept terms, provision (draw from pool), onboard, active, suspend, transfer to another master, leave/exit. Exit retains required history and revokes the entitlements and delegated access.
- **Billing roll-up:** consolidated invoicing at the master, with allocation reporting per sub-company, or separate billing with a master guarantee. Make this a configuration choice.

**Keep distinct:** an ownership tree (who is part of whom) is not a distribution tree (who sells through whom) and not a configuration chain (which rules apply). A sub-company can sit in one tree and be a *reseller of an unrelated vendor* in another.

**Audit tests:** entitlement over-allocation, orphaned sub-companies after master exit, a sub-company moving between masters, delegated admin boundaries, cross-branch leakage, and per-sub-company audit trail with master visibility rules.

## 16. Unified work inbox

**Goal:** one place where an acting user sees everything that needs them, with correct context, and where completing an item updates every place that cares.

**Domain model:**

- **WorkItem (aggregate):** id, type, source module and source record, owner (user or role queue), acting-context (organization, hat, relationship), status, priority, due date, SLA, created-by, links, and a short, actionable summary.
- **Item kinds:** approval, task, review, deal action, onboarding step, RFQ/RFP assignment, content approval, consent request, renewal, dispute, integration failure, system notice.
- **Reminder and Notification** are separate concepts: a *reminder* is time-triggered against a work item; a *notification* is a delivery of an event (in-app, email, push) governed by user preferences. Do not store the item inside the notification.
- **Lifecycle:** open, snoozed, in progress, waiting on others, done, cancelled, expired, with transitions defined (Stateless is a good fit) and audited.

**Design rules:**

- Work items are created by **domain events** (via the outbox), not by UI code. The source module owns the *meaning*; the inbox owns the *worklist*.
- Idempotent creation keyed on (source record, item type, step) so retries never duplicate.
- Every item deep-links to the exact screen and can be *completed inline* where feasible (approve, reassign, snooze, comment), through the same command the full page uses.
- Completing the item raises a domain event that updates the source record, the activity timeline, dashboards, and any external system. No second place to update.
- Hat-aware: the inbox filters by acting context, with an "all contexts" view that clearly labels each item's organization and hat.
- Delegation, out-of-office, escalation on SLA breach, bulk actions, search, saved filters, and per-type notification preferences.
- Counts in the nav badge come from the same read model as the list, so they cannot disagree.

**"Information shows up in the right places":** maintain a **propagation map** (event -> every projection that must change: inbox, timeline, record page, dashboard, search index, notification, integration). Test each row with an integration test. A missing row is a finding.

## 17. Object model cleanup: 265+ tables, anti-slop, and drift detection

**Principle:** table count is a symptom, not the problem. The question is whether each table maps to a clear concept in a bounded context with an owner.

**Discovery (automated, repeatable):**

1. Dump the EF Core model and the live schema; diff them (model vs migrations vs database vs docs).
2. Build the **entity catalog**: table, entity class, bounded context, owner, row count, last write, read/write endpoints, foreign keys, tenant column, audit columns, soft-delete, and test coverage.
3. Classify each table: *core aggregate, child entity, reference/lookup, configuration, projection/read model, integration/staging, audit/log, legacy, dead*.
4. Draw the **context map**: which contexts own which tables; list cross-context foreign keys (these are suspect).

**Slop and drift signals to detect:**

- Near-duplicate tables or columns (`Partner`, `Company`, `Account`, `Organization` all meaning the same thing); name similarity plus overlapping column sets.
- Per-channel or per-persona copies of the same structure (`ResellerDeals`, `ReferralDeals`, `DistributorDeals`).
- Enum or string "type" columns driving behavior that should be configuration or polymorphism.
- Tables with no readers or no writers; no tenant key; no audit columns; no migrations history; nullable-everything columns.
- Generated code with inconsistent naming, unused DTOs, copy-pasted handlers, and repeated validation logic.
- Settings stored in several places (appsettings, a table, a JSON blob, code constants).
- God-tables and JSON blobs hiding relational structure; unbounded `Notes` or `Metadata` fields.
- Documentation naming a concept the schema does not have, or the reverse.

**Consolidation method (safe, incremental):**

1. Declare the **canonical concept** and its bounded context in the glossary and an ADR.
2. **Expand-contract migration:** add the canonical structure, dual-write or backfill, switch readers, verify, then remove the old one in a later slice.
3. Prefer one table with a *relationship/hat* discriminator and configuration-driven behavior over N parallel tables, *unless* the lifecycle and invariants truly differ.
4. Every removal has a ledger ID, a rollback path, and a verified backup.

**Guardrails against future drift:**

- Architecture tests (for example NetArchTest or ArchUnitNET) enforcing layer and context dependencies.
- CI schema checks: tenant key required, audit columns required, naming conventions, no new table without an entity-catalog entry.
- Analyzers and code-review checklist items specific to this codebase (below).
- Quarterly re-run of the catalog and a drift dashboard (tables per context, orphan count, duplicate-candidate count).

## 18. Code review and architecture for building channels and sharing capabilities

**Target architecture:** channels are **configuration plus shared capability modules**, not separate code paths.

- **Capability modules** (own their domain model and API): Organizations and Relationships, Programs and Enrollment, Deals and Opportunities, Quoting, Incentives and Commissions, Content and Assets, Campaigns, Delivery and Projects, Work Inbox, Documents and RFx, Notifications, Billing and Entitlements.
- **Channel template = a composition:** which capabilities are on, which workflow definitions apply, which rule sets and defaults, and which persona-to-permission mappings.
- **Shared kernel:** identity, tenancy, acting context, audit, money/currency, time, configuration resolver, and entitlement checks. Kept small and stable.
- **Same code serves many companies:** variation comes from resolved configuration and policy overlays, never from branching on company or channel type in handlers.

**Code-review checklist (add to the PR template):**

1. Does it belong to a bounded context, and does it respect dependency direction?
2. Is behavior in the domain model (invariants, methods) rather than controllers or the UI?
3. Does it branch on channel, tenant, or partner type? If yes, why is it not configuration?
4. Is every query tenant- and relationship-scoped? Is authorization resource-based?
5. Is it idempotent, audited, and does it raise the right domain events (and update the propagation map)?
6. Are there new tables or columns, and are they in the entity catalog with an ADR if conceptual?
7. Are API contract, generated client, and tests updated? Are there authorization and journey tests?
8. Is it duplicating something that already exists (search before writing)?
9. Are secrets, PII, and logs handled safely?

## 19. Menu, roles, personas, and hats: correctness rules

Extends Section 5.

- Menu is resolved from **(acting context, hat, roles, entitlements, feature flags, channel template)**. Changing the acting context re-resolves it without a reload bug or stale cache.
- A menu item is registered once with: id, label, route, required permission(s), entitlement, applicable hats and channels, group, order, and badge source. Permission or entitlement missing at the API means the item must not render *and* the route must be denied.
- **Test matrix:** for every module, every role, every hat: expected visible and expected allowed (API and UI), plus a negative test for direct URL access.
- Every persona has a **home view** and a **first-value journey**; the menu groups reflect the journey (not the table structure).
- Hat switching preserves unsaved-work warnings and never leaks previous context data in breadcrumbs, recent items, or search.
- Generate the menu documentation from the module registry so docs cannot drift.

## 20. Security, governance, guardrails, and ADRs

**Security and governance model to verify:**

- **Identity:** SSO (OIDC/SAML), MFA, SCIM provisioning for enterprise, session and token lifetimes, service-to-service identity.
- **Authorization:** policy-based plus resource-based, with relationship and hat scoping, tested by matrix; row-level tenant isolation (query filters, and database row-level security where appropriate).
- **Data governance:** classification per field (public, internal, confidential, PII), consent records, retention and deletion rules, legal hold, export, and right-to-erasure handling that coexists with audit history.
- **Secrets and keys:** managed vault, rotation, per-tenant integration credentials, no secrets in logs or flow definitions.
- **Auditability:** append-only event log (Section 7), tamper evidence, retention, admin search.
- **AI governance:** allowed data sources, redaction, human approval, model and prompt versioning, evaluation, and incident procedure.
- **Supply chain and operations:** dependency scanning, SBOM, signed builds, least-privilege deployment, backup and restore drills, incident runbooks.

**Guardrails as code:** architecture tests, CI policy checks, analyzers, migration review gates, feature-flag standards, and a definition of done that includes security and audit.

**ADR practice:** one file per decision, numbered, immutable once accepted (supersede, never edit). Template: *Context, Decision, Alternatives, Consequences, Status, Date, Owner, Links (ledger IDs)*.

**First ADRs to write (they unlock the rest):**

1. Organization, Relationship, and Hat model (Section 14)
2. Ownership / franchise tree and license pools (Section 15)
3. Configuration inheritance, locked vs overridable (Section 3)
4. Tenancy and data-sharing scopes
5. Work Inbox and propagation via domain events and outbox (Section 16)
6. Canonical object model and table consolidation rules (Section 17)
7. Workflow boundary: Stateless vs Activepieces (Section 21)
8. Audit event log standard
9. Navigation module registry and permission resolution
10. AI usage, retrieval boundaries, and approval policy

## 21. Integration: Stateless as default, Activepieces for extension

**Recommended boundary (validate against your code):**

| Concern | Owner | Why |
| --- | --- | --- |
| Business state machines inside the product (deal status, onboarding, approvals, work item lifecycle) | **Stateless** in the domain layer | Deterministic, testable, transactional with the aggregate, auditable |
| Cross-system orchestration, notifications to external tools, CRM/marketing/ERP sync flows, customer-specific automations | **Activepieces** flows | Configurable without redeploying, rich connector catalog, faster to add integrations |
| System of record for each entity | Declared per entity in the integration catalog | Prevents sync conflicts |

**Rules:**

- The product emits **domain events** through a transactional **outbox**; an integration publisher delivers them to Activepieces via signed webhooks. Activepieces calls back through **versioned, authenticated, idempotent APIs**, never directly into the database.
- Activepieces never owns core business state or business rules. It may trigger a command; the domain still validates it.
- **Tenant isolation:** per-tenant connections and credentials, per-tenant flow namespaces, no shared secrets, and a review that confirms one tenant's flow cannot read another's events.
- **Flow governance:** flow templates are versioned in source control, promoted through environments, reviewed like code, and have an owner. Customer-editable flows run in a restricted, quota-limited space.
- **Reliability:** retries with backoff, dead-letter handling, replay, correlation IDs shared with the audit log, and failures create **work items** in the inbox for the right owner.
- **Observability:** run history, latency, error rate, and per-tenant usage visible to operators, and surfaced to admins where appropriate.
- **Security:** webhook signature validation, allow-listed callback endpoints, least-privilege API scopes, no PII in flow logs unless classified and approved.
- **Migration path:** inventory current Stateless usage and any existing integrations; move only cross-system orchestration to Activepieces; leave in-aggregate state machines where they are.

**Integration catalog (new doc, `09-integrations`):** for each integration: direction, trigger events, data contract, system of record, sync frequency, conflict rule, failure handling, owner, test, and runbook. Verify Activepieces capabilities, hosting, licensing, and version details against its current documentation before committing to the design.

## 22. Custom skills and plugins to create

Create narrow, single-purpose skills so every session starts from the same method. Each skill is a `SKILL.md` (purpose, when to use, inputs, steps, outputs, quality checks, anti-patterns) plus optional scripts and templates.

| Skill | Purpose | Key outputs |
| --- | --- | --- |
| `prm-audit-ledger` | Create, update, and validate findings in the ledger format | Ledger rows, status changes, lessons log |
| `docs-reconcile` | Harvest docs, extract features from code, run the 3-way reconciliation | Feature register, archive moves, doc index |
| `domain-model-review` | Review aggregates, invariants, bounded contexts, glossary consistency | Findings, proposed model changes, ADR draft |
| `schema-drift-check` | Dump EF model and DB, diff, flag duplicates and slop signals | Entity catalog, drift report, consolidation plan |
| `config-resolver-spec` | Define and test inheritance, overlays, exceptions, explainability | Setting catalog, resolver tests, impact preview |
| `nav-module-registry` | Build the module registry and role/hat matrix; generate menu docs | Registry, matrix tests, orphan report |
| `journey-mapper` | Persona journeys and Playwright journey tests | Journey maps, test scaffolds |
| `adr-writer` | Produce and index ADRs in the standard template | Numbered ADRs, supersede links |
| `work-inbox-propagation` | Maintain the event-to-projection propagation map and tests | Propagation map, integration tests |
| `activepieces-flow-review` | Review flows for tenancy, idempotency, secrets, and failure handling | Review checklist results, flow catalog |
| `design-system-fidelity` | Compare React UI to the chosen design system tokens (Material, Lightning, or Spectrum) | Deviation report, token fixes |
| `slice-runner` | Enforce the Section 8 cycle and anti-drift controls | Slice plan, end-of-slice checklist |

Use the existing document, spreadsheet, and presentation skills for deliverables, and the front-end design skill for UI work. Keep custom skills small, version them, and review them after each phase as part of lessons learned.

## 23. Updated phase plan and first slices

**Phase 0 (new): Reconcile.** Harvest, feature register, entity catalog, doc archive, innovation register.

**Suggested first slices (each is one ledger-tracked chunk):**

1. Slice 0.1: Harvest docs and code inventory; publish the feature register (read-only).
2. Slice 0.2: Entity catalog and drift report for the 265+ tables (read-only).
3. Slice 0.3: Write ADRs 1 to 4 (organization/hat, ownership tree, configuration chain, tenancy). No code change yet.
4. Slice 1.1: Architecture tests and CI guardrails (stops new drift immediately).
5. Slice 1.2: Module registry and role/hat matrix tests for the existing menu.
6. Slice 1.3: Audit event log gap analysis and fix.
7. Slice 2.1: Work inbox propagation map and the first end-to-end item type (for example, approvals).
8. Slice 2.2: Stateless vs Activepieces boundary ADR and integration catalog.
9. Slice 3.x: Consolidate the first duplicate concept using expand-contract.

Re-baseline docs against code after every three slices.

## 24. Additional acceptance tests introduced by this addendum

- One organization with three hats shows three isolated contexts; no data crosses without an explicit sharing scope.
- A sub-company joins a master, draws a license, inherits policy, and exits with history retained and access revoked.
- Completing a work item once updates the source record, timeline, dashboards, search, notifications, and any integration.
- The entity catalog accounts for every table; CI blocks a new table without a catalog entry.
- Every menu item traces to a registered module, permission, entitlement, and hat; direct URL access is denied where the menu hides it.
- An Activepieces failure produces an audit entry and a work item for the right owner, and replay is idempotent.
- Every ADR in Section 20 exists, is indexed, and is referenced by the ledger items it resolves.
