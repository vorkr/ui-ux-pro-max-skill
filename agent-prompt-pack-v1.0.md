---
title: "Agent Prompt Pack"
subtitle: "How to audit, refactor and build partner.directory + Upsll iteratively with AI agents · 28 October 2026"
---

**Use with:** *Architecture v2.0* and *Architecture v1.1*. Attach both to every new session (or put them in the repository under `/docs/architecture/`).

# 1. How to use this pack

**The loop:** `Work Order → agent does one slice → tests and docs updated → STATE.md updated → human reviews → next Work Order.` Never ask an agent to "build everything". Give it one Work Order with a budget and a stop condition.

**Persistence:** AI sessions forget. The repository remembers. Keep these files current:

| File | Purpose | Updated by |
|---|---|---|
| `/docs/architecture/architecture-v2.0.md` and `v1.1.md` | The design and invariants | Humans through ADRs |
| `/docs/STATE.md` | What is done, in progress, blocked, next | Every agent, at the end of every session |
| `/docs/DECISIONS/ADR-xxxx.md` | One file per decision | Agents propose; human approves |
| `/docs/catalogue/entities.csv` | Entity Catalogue (all tables) | Audit and consolidation agents |
| `/docs/journeys/J-xx.md` | One Journey Spec per flow, with maturity level | Journey agent |
| `/docs/contracts/` | OpenAPI, event schemas, automation contracts | Build agents (generated where possible) |
| `/docs/BACKLOG.md` | Prioritised Work Orders | Human and planner agent |
| `/docs/context-packs/` | Short briefings for each module | Doc-keeper agent |

**Work Order format** (copy for every task):

```
WORK ORDER WO-0000
Objective: one sentence.
Inputs: files, folders, docs to read (and nothing else).
Out of scope: what not to touch.
Deliverables: exact files or changes.
Acceptance: tests that must pass; checks a human will run.
Budget: max files touched = N; max time or tokens = X; stop and report if exceeded.
Stop conditions: ambiguity, failing tests after 3 attempts, any invariant at risk (I-xx, SR-xx).
Ask-before list: schema changes, new dependencies, deleting code, anything touching auth, money or public data.
Report: update STATE.md; list decisions needing an ADR; list questions for the human.
```

**Suggested budgets [A]:** audit pass, one module: a few hours of agent time and under ~40 files read in depth; one journey to L3: one session; one build slice: under ~15 files changed and a full test run. Adjust by experience.

# 2. Agent roles and orchestration

| Role | Mission | Reads | Writes | Human gate |
|---|---|---|---|---|
| Planner (supervisor) | Turn the backlog into Work Orders; sequence them; detect conflicts | STATE, BACKLOG, architecture | BACKLOG, Work Orders | Approves order |
| Code Auditor | Inventory the codebase, entities, routes, endpoints, tests | Repository | catalogue, audit reports | Reviews findings |
| Data Architect | Consolidate entities into families; plan migrations | Catalogue, architecture §12 | consolidation plan, migrations (expand phase) | Approves each merge |
| Journey Specifier | Interview, read code, write L3 Journey Specs | Journeys, code, screens | `/docs/journeys` | Approves specs |
| Domain Builder | Implement a slice (domain, API, tests) | Journey Spec, contracts | code, tests | Reviews PR |
| Front-End Wirer | Connect existing screens to typed API clients | Route map, OpenAPI | front-end code, Storybook | Reviews visuals |
| Security Reviewer | Check matrix, tenant isolation, public projections | Code, permission catalogue | findings, tests | Approves |
| QA Agent | Generate and run tests, visual regression | Specs, code | tests | Reviews failures |
| Research Agent | Competitors, analysts, regulations, with sources | Web | research notes with citations | Verifies key facts |
| Doc-Keeper | Keep STATE, ADRs, context packs and catalogue true | Everything | docs | Spot checks |

**Patterns that work for a tiny team**

- **One supervisor, few workers.** The Planner writes Work Orders; workers never pick their own scope.
- **Directory ownership.** Each worker edits only its own folders in a Work Order, so parallel agents do not collide.
- **Review agent after every build agent.** A different agent (Security Reviewer or QA) reviews before a human does.
- **Human gates** at: schema merges, new dependencies, auth, money, public data, deleting code.
- **Shadow before switch.** For risky changes (entity merges, engine swaps), run old and new in parallel and compare.
- **Small PRs, feature flags, compatibility views.** Every change reversible.

# 3. Master prompt (paste at the start of every session)

```
You are working on partner.directory (public B2B partner directory) and Upsll (enterprise
PRM/CRM workspace). One platform, two front doors. Team: fewer than five humans plus you.

Read first, in this order: /docs/STATE.md, Architecture v2.0, Architecture v1.1, the Work Order.
Precedence: Invariants (I-xx) > Decisions (D-xx) > Principles > Examples. v2.0 overrides v1.1.

Stack: C# .NET 10 modular monolith, EF Core, PostgreSQL (row-level security, full-text,
vectors, outbox), React front ends (SSR directory, SPA app). Managed services only. No
microservices, no Kubernetes, no Rust rewrite.

Core ideas you must respect:
- Five planes: Experience, Control, Data, Automation (Activepieces + Integration Gateway), AI.
- Journey database is the system of record. Automations use signed API commands only.
- Person wears Hats; Persona shapes UI and never grants access. One policy function decides.
- Channels are Recipes: blueprint + packs + tenant overlay, pinned by lockfile, upgradable.
- Content vocabulary: Fact -> Card -> Explorer -> Page -> Publication. Actions via registry.
- Inbox is the universal work surface. AI proposes; humans approve anything binding.
- Public projections contain only approved fields. Demo data never reaches production.

Rules: Do not invent facts, prices, laws, competitor details or contacts; mark [A] or [L].
Prefer the smallest reversible change. Add tests with every change. Respect the budget and
stop conditions in the Work Order. At the end: update /docs/STATE.md, list ADRs needed,
and list questions for the human. If anything conflicts with an invariant, stop and ask.
```

# 4. Audit prompts

## A1 Discover the current codebase

```
Goal: produce an accurate inventory of the existing solution without changing code.
Inputs: the repository (read-only).
Do:
1. List solution and projects, target frameworks, key packages (versions), build and test commands.
2. Identify layers and module boundaries as they actually exist; draw a dependency map.
3. List all DbContexts, entity classes, DbSets, migrations (count, latest), connection settings
   (do not print secrets).
4. List all API endpoints (controllers or minimal APIs) with route, verb, auth attribute,
   request and response types. Export OpenAPI if available.
5. List background jobs, hosted services, queues, schedulers, external integrations.
6. List authentication and authorization mechanisms (roles, policies, claims, tenant resolution).
7. List front-end framework, routing, state management, component library, styling approach,
   and how it calls the API.
8. List tests (unit, integration, e2e) and coverage if available.
Output: /docs/audit/01-inventory.md with tables; /docs/audit/01-questions.md with anything unclear.
Acceptance: every claim cites a file path; unknowns are listed, not guessed.
Budget: read-only; stop after the inventory; do not propose changes yet.
```

## A2 Extract and classify the entity catalogue

```
Goal: create /docs/catalogue/entities.csv covering ALL entities and tables (expected ~265).
Inputs: EF Core model (use the script in section 6), migrations, database schema.
For each entity record: Name, Table, Module (guess), Family (Party, Relationship, Term, Document,
Event, Work, Configuration, Content, Commercial, Communication, Integration, Billing, Other),
Pattern (core | lookup | join | log | config | projection | duplicate-suspect), TenantScoped (y/n),
DataClass (Public|Agreement|Private|Restricted), PII (y/n), Lifecycle (static|mutable|append-only),
Owner (module), KeyColumns, ForeignKeys (to), ReferencedBy (from), RowCountOrSize (if known),
ReadPaths (endpoints, jobs, screens), WritePaths, Notes.
Rules: do not delete or change anything. Mark uncertainty explicitly. Flag suspected duplicates and
tables with no readers or writers.
Output: entities.csv, /docs/catalogue/summary.md (counts by family, pattern, module; top 20 duplicates).
Acceptance: row count equals the model's entity count; spot-check 10 rows against code.
Budget: process in batches of ~40 entities; report progress in STATE.md after each batch.
```

## A3 Propose the consolidation

```
Goal: propose how to reduce and reuse entities, using Architecture v2.0 section 12 (families, rules E-01..E-09).
Inputs: entities.csv, summary.md, v2.0 section 12.
Do:
1. Cluster entities by family and by shared lifecycle, owner and access pattern.
2. For each cluster propose: keep | merge into X | convert to Term | convert to Event | split.
3. For each proposal give: rationale, rule cited (E-xx), risk (L/M/H), screens and APIs affected,
   migration steps (expand/contract), compatibility view definition, tests needed.
4. Produce a target table list (name, family, columns, tenant scope, data class).
5. Order the work as small migrations with rollback.
Output: /docs/catalogue/consolidation-plan.md, /docs/catalogue/target-model.md, BACKLOG entries.
Acceptance: every current entity is mapped to exactly one disposition; no capability is lost.
Gate: a human approves each High-risk merge before any migration is written.
```

## A4 Front-end and API wiring audit (keep the design, rewire it)

```
Goal: map every existing screen to the data and actions it uses, so the UI can be wired to the
refactored back end without redesigning it.
Inputs: front-end repo, OpenAPI, v2.0 sections 2.4 (v1.1), 8, 9.
Do:
1. List all routes/pages with template type (T-L1..T-L8), components used, API calls, state stores.
2. List shared components and design tokens actually in use; note inconsistencies (spacing, colours,
   typography); do NOT restyle.
3. For each screen list: personas that see it, entities read/written, actions available, empty/error
   states present or missing.
4. Identify screens that are cards, explorers or pages in the v2.0 vocabulary; propose mapping.
5. Generate a typed API client from OpenAPI and list calls that do not match contracts.
6. Capture Playwright screenshots for key flows (desktop and mobile) as the visual baseline.
Output: /docs/audit/04-route-map.md, /docs/audit/04-component-map.md, /docs/audit/04-contract-gaps.md,
screenshots folder.
Acceptance: every route appears once; contract gaps are tied to endpoints.
```

## A5 Journey elicitation to L3 (run once per flow)

```
Goal: raise Journey J-xx from its current level to L3 using the Journey Spec standard (v2.0 section 13).
Inputs: the current description of J-xx, related code and screens, the permission catalogue, the entity
catalogue, the exemplar J-05.
Do:
1. Read the code paths and screens that already implement any part of this flow.
2. Ask the human at most 7 targeted questions (batch them) about missing decisions: actors, SLAs,
   rules, failure handling, data retention.
3. Write the full spec: purpose and metric, actors and hats, trigger, preconditions, states and
   transitions, steps with types, screens (route, template, cards), entities read/written, API
   contracts, events, automation contracts, rules, permissions, at least 10 edge cases (checklist in 13.1),
   analytics events, Gherkin acceptance tests, non-functional needs.
4. List what exists in code vs what is missing (gap list) and the implementation slices.
Output: /docs/journeys/J-xx.md (level declared at top), gap list, proposed Work Orders.
Acceptance: another agent could implement it with no further questions; edge-case checklist is complete.
```

# 5. Competitive and market research refresh (internet)

```
Goal: refresh competitor, analyst and regulation facts with sources and dates.
Scope: Unifyr (formerly ZiftONE), Impartner, Magentrix, ZINFI, Channelscaler, WorkSpan, Introw,
PartnerTap, Crossbeam/Reveal, PartnerStack/AppDirect, Clazar, Labra, Tackle, G2/Capterra/TrustRadius/
Clutch/Thomasnet/Kompass; Activepieces (editions, pricing, embed licence); Elsa Workflows; Temporal;
analysts: Gartner, Forrester, McKinsey, Bain, BCG, Deloitte, IDC, Omdia/Canalys, Techaisle.
Rules: primary sources first; label secondary; cite URL and retrieval date; separate fact,
assumption and judgment; flag paywalls; no private contact details; never invent people or quotes.
Output: /docs/research/YYYY-MM-DD-topic.md with a claims table (claim, source, date, confidence)
and "what would change this".
```
# 6. Build-slice prompt (reusable for every feature)

```
WORK ORDER: Build slice {SLICE_ID} - {NAME}
Read: Master prompt, Architecture v2.0 sections {SECTIONS}, Journey Spec {J-xx} (must be L3),
/docs/catalogue/target-model.md (relevant tables), /docs/contracts.
Plan first: write a short plan (files to add or change, migrations, events, tests). Wait for approval
only if the Ask-before list applies; otherwise proceed.
Build in this order:
1. Domain: aggregates, invariants, domain events (unit tests first).
2. Persistence: EF mappings, expand-phase migration, row-level security policy, compatibility views.
3. Application: command and query handlers (vertical slice), policy decision calls, outbox events.
4. API: endpoints, OpenAPI, ProblemDetails, idempotency keys; regenerate the TypeScript client.
5. Journey: definition file and any automation contracts; bind steps to native handlers.
6. Front end: wire existing screens to the typed client; add Card actions and Inbox items; Storybook story.
7. Tests: unit, integration (Testcontainers), authorization matrix cells, architecture tests,
   Playwright flow and visual baseline.
8. Docs: update the Journey Spec gap list, context pack, STATE.md, ADRs for any decision.
Definition of done: all tests green; architecture tests green; no invariant violated; feature flag
and owner named; metric and kill criterion stated; STATE.md updated.
Stop and ask if: you need a new dependency, a schema change beyond the plan, or any change to
authentication, money or public projections.
```

**Slices (fill the template)**

| Slice | Name | Sections | Journey | Deliverables | Acceptance (short) |
|---|---|---|---|---|---|
| S-01 | Permission catalogue and generated matrix tests | v2 §4.4 | — | `permissions.yaml`, generator, admin "who can do what" view | One test per matrix cell; explorer shows reasons |
| S-02 | Hats, facets, org units and persona switcher | v2 §4 | J-14 | A-30..A-33 tables, switcher UI, "acting as" banner | Hat recorded on every command |
| S-03 | Universal Inbox and Card Action registry | v2 §8.3, §9 | J-02, J-05 | InboxItem, actions, context menus, favorites | Menu differs by hat and persona; email action link works |
| S-04 | Journey runtime v1 | v2 §6 | J-05, J-13 | `IJourneyRuntime`, timers, signals, outbox, definition registry | Instance survives restart; pinned versions |
| S-05 | Control plane lite and reconciler | v2 §3, §5.4, §7 | J-13 | Tenant registry, desired state, provisioning plan | New tenant ready without staff |
| S-06 | Recipe registry v1 and lockfile | v2 §5 | J-16 | Releases, manifests, resolve and pin | Program runs from a lock |
| S-07 | Recipe upgrade planner | v2 §5.2–5.3 | J-17 | Three-way merge, dry run, rollback | Upgrade plan accurate on fixtures |
| S-08 | Content system v1 | v2 §8 | J-20 | Card types, explorers, pages, publication | Preview as persona; permission lint blocks confidential fields |
| S-09 | Integration Gateway and first CRM connector | v2 §6.5, §7.3 | J-19 | Vault, egress allowlist, schemas, kill switch, contract tests | Failed sync creates Inbox task |
| S-10 | Activepieces spike | v2 §6.5 | J-18 | One flow via Upsll piece, callback, shadow binding | Native vs flow outputs compared |
| S-11 | Knowledge base and answer library | v2 §10.1 | J-24 | Ingest, ACL retrieval, citations | Cited answers; ACL test passes |
| S-12 | RFP agent | v2 §10.2 | J-21 | Parse, draft, review queue, export | Golden-set evaluation recorded |
| S-13 | Matching core: deal and partner | v2 §10.3–10.4 | J-22, J-23 | Shared features, scoring, explanations | Relevance metrics baseline |
| S-14 | Platform Data Mart and ops dashboards | v2 §7.2 | J-30 | Event ingest, dashboards | Stuck journey alert fires on fixture |
| S-15 | Network feed and marketplace requests | v2 §11 | J-25, J-26 | Typed posts, requests, introductions | Request opens an introduction journey |

# 7. Scripts the Code Auditor can use

**EF Core model dump (console tool added to the solution)**

```csharp
// Tools/ModelDump/Program.cs  (run with: dotnet run --project Tools/ModelDump)
using Microsoft.EntityFrameworkCore;
using System.Text.Json;

// Replace YourDbContext with each context; create via your design-time factory.
using var db = new YourDbContextFactory().CreateDbContext(Array.Empty<string>());
var rows = db.Model.GetEntityTypes().Select(e => new
{
    Name = e.ClrType.Name,
    Table = e.GetTableName(),
    Schema = e.GetSchema(),
    Keys = e.FindPrimaryKey()?.Properties.Select(p => p.Name),
    Properties = e.GetProperties().Select(p => new { p.Name, Type = p.ClrType.Name, p.IsNullable }),
    ForeignKeys = e.GetForeignKeys().Select(f => new {
        To = f.PrincipalEntityType.ClrType.Name, Columns = f.Properties.Select(p => p.Name) }),
    HasTenantId = e.GetProperties().Any(p => p.Name.Equals("TenantId", StringComparison.OrdinalIgnoreCase))
});
File.WriteAllText("docs/catalogue/model-dump.json",
    JsonSerializer.Serialize(rows, new JsonSerializerOptions { WriteIndented = true }));
```

**PostgreSQL schema and relationship listing**

```sql
select c.table_schema, c.table_name, count(*) as columns
from information_schema.columns c
where c.table_schema not in ('pg_catalog','information_schema')
group by 1,2 order by 1,2;

select tc.table_schema, tc.table_name, kcu.column_name,
       ccu.table_name as references_table
from information_schema.table_constraints tc
join information_schema.key_column_usage kcu on tc.constraint_name = kcu.constraint_name
join information_schema.constraint_column_usage ccu on ccu.constraint_name = tc.constraint_name
where tc.constraint_type = 'FOREIGN KEY';
```

**Quick inventories**

```bash
dotnet sln list; dotnet list package --outdated
rg -n "DbSet<" --glob "*.cs" > docs/audit/dbsets.txt
rg -n "\[Http(Get|Post|Put|Delete|Patch)" --glob "*.cs" > docs/audit/endpoints.txt
rg -n "createBrowserRouter|<Route|path:" --glob "*.{ts,tsx,js,jsx}" > docs/audit/routes.txt
dotnet ef migrations list
```

# 8. Templates

**Entity Catalogue header (CSV)**

```
Name,Table,Module,Family,Pattern,TenantScoped,DataClass,PII,Lifecycle,Owner,KeyColumns,ForeignKeysTo,ReferencedBy,Size,ReadPaths,WritePaths,Disposition,TargetTable,Risk,Notes
```

**Journey Spec skeleton**

```
# J-xx Name   (level: L0|L1|L2|L3)
Purpose and metric:
Actors and hats:
Trigger and preconditions:
States and transitions:
Steps (id, type, owner, SLA, contract/binding):
Screens (route, template, cards, actions):
Entities read / written:
API contracts and events:
Automation contracts (id@version, side-effect class, idempotency, timeout):
Rules and policies (policy-as-data keys):
Permissions (matrix rows):
Edge cases (10+ from checklist):
Analytics events:
Acceptance tests (Gherkin):
Non-functional needs:
Gaps vs current code, and implementation slices:
```

**Card spec**

```
CardType: key, purpose (shows one thing), bound facts, schema version, template, empty/error states,
actions (ActionDefinition ids), required permissions, audiences, data class, accessibility notes.
```

**Recipe manifest** — see v2.0 section 5.1. **Automation contract** — see v2.0 K-03.

**STATE.md skeleton**

```
# STATE (updated YYYY-MM-DD by <agent/human>)
Done: ...
In progress (owner, WO): ...
Blocked (why, who): ...
Next three Work Orders: ...
Decisions needing an ADR: ...
Open questions for the human: ...
Risks and surprises: ...
Test status: unit/integration/e2e/architecture: ...
```

**ADR skeleton**

```
# ADR-xxxx Title   Status: proposed|accepted|superseded
Context: Decision: Options considered: Consequences: Operational cost for a tiny team: Rollback:
```

# 9. First ten Work Orders (suggested order)

| WO | Task | Prompt | Output |
|---|---|---|---|
| 1 | Discover current codebase | A1 | Inventory and questions |
| 2 | Extract entity catalogue | A2 | entities.csv (all 265) |
| 3 | Front-end and API wiring audit | A4 | Route map, contract gaps, baselines |
| 4 | Consolidation proposal | A3 | Plan and target model (human approves) |
| 5 | Elicit the five most important journeys to L3 | A5 ×5 | J-02, J-03, J-05, J-13, J-15 |
| 6 | Permission catalogue and matrix tests | S-01 | Generated tests |
| 7 | Hats, facets, persona switcher | S-02 | Multi-hat working |
| 8 | Inbox and Card Action registry | S-03 | Menus and tasks |
| 9 | Journey runtime v1 | S-04 | Five journeys running |
| 10 | Control plane lite and reconciler | S-05 | Self-serve provisioning |

After WO 10, review STATE.md with the human, retire anything that did not earn its place, and plan Phase 2.

# 10. Guardrails for agent work

- Never let an agent change authentication, money handling or public projections without a human gate.
- Never give an agent production credentials; use sandbox tenants and recorded fixtures.
- Keep each agent's context small: give it the Work Order, the relevant context pack and the specific files, not the whole repository.
- Require every agent to leave the repository in a green state, with STATE.md updated.
- Evaluate AI features with golden sets before enabling them for customers.
- Review costs weekly: tokens, automation tasks, hosting.
