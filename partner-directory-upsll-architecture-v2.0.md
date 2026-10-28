---
title: "partner.directory + Upsll — Architecture v2.0"
subtitle: "Platform planes, workflow engine, versioned recipes, content system, network layer · 28 October 2026"
---

**Audience:** the founder, a team of fewer than five people, and AI agents that will research, audit, design and build this iteratively.
**Companion files:** *Architecture v1.1* (still normative unless marked superseded here) and the *Agent Prompt Pack* (the prompts, templates and orchestration plan that use this document).

# 0. Read order, precedence and my interpretations

**Read order for humans and agents:** this document first, then v1.1 for the base objects (A-01 to A-29), invariants (I-01 to I-14), security rules (SR-01 to SR-15), capabilities (C-01 to C-17) and journeys J-01 to J-12. Where v2.0 and v1.1 conflict, **v2.0 wins**.

**Precedence:** Invariants > Decisions > Principles > Examples. **Tags:** [V] verified externally on the date shown, [A] assumption to validate, [L] needs legal review.

**New ID families in v2.0:** R recommendation · Z plane · K workflow/automation contract rule · V recipe versioning rule · X access-matrix rule · N new feature · UC and J continue from v1.1.

**Things I interpreted (please correct me):**

1. *"A data [tier] independent of the tenant so it's easy to monitor or manage"* → a **control plane** (platform-owned metadata and operations) kept separate from each tenant's **data plane**, plus a tenant-agnostic **Platform Data Mart** for monitoring. [A]
2. *"Unyfyr / piepdrive"* → **Unifyr** (a PRM and through-channel marketing platform, formerly ZiftONE) and **Pipedrive** (a CRM, not a PRM, so an integration target rather than a head-on competitor). [A]
3. *"265 entities and tables"* → I have not seen them. This document gives the **method, target patterns and templates** to consolidate them; the Prompt Pack contains the scripts to extract and classify them from your code.
4. *"Enhanced social media for the enterprise"* → a **Network layer** built from structured primitives (section 11), not an open social network.

# 1. What v2.0 changes, and the top 20 recommendations

**In one paragraph.** v1.1 defined *what* the platform is made of. v2.0 defines *how it runs and evolves*: five planes with a tenant-independent control plane; a journey engine that owns business state, with Activepieces as a decoupled automation plane behind versioned contracts; recipes that can be upgraded after provisioning; a multi-hat identity model with a published access matrix; a clear content vocabulary (Fact, Card, Explorer, Page, Publication); a universal Inbox; a knowledge base powering RFP answers and matching; a Network layer; and a repeatable way to consolidate 265 entities and specify 30 journeys so AI can build them.

| ID | Recommendation | Why | Effort | Where |
|---|---|---|---|---|
| R-01 | Split the platform into five planes: Experience, Control, Data, Automation, AI. Keep the control plane free of tenant business data | Lets you monitor, upgrade and support all tenants without touching their data | M | 3 |
| R-02 | Keep the **journey engine as the system of record**. Use Activepieces only as the **automation plane** for integration recipes, behind versioned contracts | Avoids business state leaking into a no-code tool while still giving you its connectors and builder | M | 6 |
| R-03 | Make every channel a **Recipe**: blueprint + packs + tenant overlay, pinned by a lockfile, upgradable with three-way merge, dry run and rollback | Solves "change a channel after provisioning" and the "limited customization" complaint | L | 5 |
| R-04 | Model multi-hat firms as Organization Group → Organization → OrgUnit, with scoped **Role Facets** (vendor, reseller, provider) and Hats | Handles enterprises, franchises, umbrellas and divisions without duplicate companies | M | 4 |
| R-05 | One access-decision function over the matrix Person × Hat × Persona × Org × Vertical × Program, published as data with generated tests | Removes the cross-matrix confusion | M | 4 |
| R-06 | Fix the content vocabulary: **Fact → Card → Explorer → Page → Publication**, with metadata as typed attributes | Ends the cards/pages/explorers confusion | M | 8 |
| R-07 | A **Card Action registry** drives right-click and long-press menus by role, persona and hat | Customization without code and without permission leaks | S | 8 |
| R-08 | A **universal Inbox** for tasks, approvals, intros, RFP questions, mentions, expiries and AI proposals, plus favorites and saved views | One place to act; fights portal fatigue | M | 9 |
| R-09 | Treat the **knowledge base** as a first-class service (sources, chunks, anchors, ACLs, freshness, answer library) | Powers RFP answers, Q&A and matching | M | 10 |
| R-10 | Build an **RFP and questionnaire response agent** with mandatory human review in the Inbox | High-value, differentiating, safe if review-gated | M | 10 |
| R-11 | Build **deal matching** and **partner matching** on one shared matching core | One service, two products, explainable | M | 10 |
| R-12 | Build the **Network layer** from five structured primitives (Profile, Post, Group, Thread, Offer/Request) and start with platform-generated activity | Social engagement without moderation overload | L | 11 |
| R-13 | Launch the **Marketplace** as listings, requests and offers that open introductions and journeys, not payments | Real value without liability | M | 11 |
| R-14 | **Consolidate the 265 entities** into about a dozen table families using a catalogue, merge rules and compatibility views | Less code, fewer bugs, easier AI context | L | 12 |
| R-15 | Specify every journey to a standard **Journey Spec** with levels L0 to L3; only L3 is "AI-buildable" | Makes 30 flows buildable by agents | M | 13 |
| R-16 | Make onboarding **self-serve by default** through a tenant desired-state **reconciler** that also drives upgrades | One mechanism for provisioning, upgrades and repair | L | 5, 14 |
| R-17 | Put all third-party calls behind an **Integration Gateway** (vault, egress allowlist, schemas, kill switch, contract tests) | Safe, decoupled, versioned integrations | M | 6, 7 |
| R-18 | Create a tenant-agnostic **Platform Data Mart** for monitoring, cost and product analytics | Operate everything from one console | M | 7 |
| R-19 | Ship **vertical packs** as installable, versioned bundles (taxonomy, evidence types, blueprints, card templates, journeys, KB seeds, automation recipes) | New verticals become configuration | M | 5 |
| R-20 | Run development with **orchestrated AI agents** using persisted state files, budgets and checkpoints | Fewer than five people can still build everything | S | Prompt Pack |

Effort: S = days, M = weeks, L = a month or more for a small team. [A]

# 2. Evidence and competitive pain points

All items are secondary evidence retrieved on 28 Oct 2026 unless stated.

| Finding | Source type | Design response |
|---|---|---|
| On G2 comparison pages, reviewers tag Impartner PRM with "Feature limitations" (21 reviews) and "Limited customization" (19); Unifyr with "Feature limitations" (8) and "Limited customization" (6). Both are praised for "Ease of use" (44 and 18). These are review-tag counts, not rates | G2 comparison pages [secondary] | Customization must not require a vendor ticket: versioned recipes, overlays and a card/explorer studio (R-03, R-06). Keep ease of use through guided setup (R-16) |
| Unifyr (ZiftONE, now Unifyr One) is positioned as PRM plus through-channel marketing automation; entry pricing is "contact us" | G2, Capterra [secondary] | Compete on partner-first workspace, transparent packaging and self-serve, not on through-channel marketing breadth |
| Impartner lists an Emerge edition at $25,000 a year; Magentrix from $1,500 per user or account; PartnerPortal.io has a free trial (5 partners, 3 staff) | G2 [secondary] | A self-serve entry price far below enterprise PRM, with a free partner side |
| Activepieces: MIT-licensed community edition, self-hostable, all pieces and unlimited flows; the Embed SDK and JWT single sign-on are only in the Embed edition; enterprise features (SSO, SCIM, advanced RBAC, audit, piece management) are commercially licensed; pieces are TypeScript npm packages; pieces are also exposed as MCP servers | Activepieces docs and review sites [V, secondary] | Run community edition internally first; buy Embed only if customers must edit flows inside your product (K-10, section 6.5) |
| Temporal needs a separate server cluster (high operational complexity); Elsa v3 is embeddable with a visual designer (low to medium); simple in-process DAG libraries exist for .NET | Package comparison tables [secondary] | Build a thin Postgres-backed journey runtime behind an interface; revisit Elsa or Temporal Cloud only on the triggers in 6.4 |
| Pipedrive is a CRM, not a PRM | General knowledge [A] | Treat as a CRM connector candidate, not a competitor |

**Complaint pattern to design against:** tools that are easy at first but rigid later. Your answer is *easy start, safe change*: recipes that upgrade, overlays that survive upgrades, and automations that can be swapped without touching journeys.

# 3. Reference architecture v2: five planes

| Plane | ID | Owns | Does not own |
|---|---|---|---|
| Experience | Z-01 | Directory (SSR), Upsll app (SPA), admin console, embeddable widgets, public REST and MCP | Business rules |
| Control | Z-02 | Tenant registry, plans and entitlements, recipe/pack/connector releases, automation bindings, feature flags, policy bundles, billing, usage meters, support grants, platform audit | Any tenant business data |
| Data | Z-03 | Public graph (directory) and per-tenant domain data (all v1.1 modules), journey state, inbox, content, knowledge base metadata | Platform configuration |
| Automation | Z-04 | Activepieces flows, custom Upsll pieces, Integration Gateway, connector credentials | Journey state of record |
| AI | Z-05 | AI gateway, embeddings, knowledge-base retrieval, matching, RFP agent, evaluation | Decisions that bind |

```
Browsers, partners, AI agents
   |
[Experience plane]  directory SSR | app SPA | admin | widgets | REST/MCP
   |  (BFF, policy decision point on every call)
[Control plane] <---- desired state / releases ----> [Data plane: modular monolith + Postgres]
   | tenant registry, recipes, flags, bindings          journeys, inbox, content, KB, domain modules
   |                                                          |  outbox events
   |                                                    [Automation plane]  Integration Gateway
   |                                                          |   Activepieces (flows via custom Upsll piece)
   |                                                    [AI plane] gateway | retrieval | matching | RFP agent
   |
[Platform Data Mart]  tenant-agnostic, pseudonymized telemetry  --> ops dashboards, cost, health
```

**Decisions**

| ID | Decision |
|---|---|
| D-15 | **Pooled multi-tenancy by default** (shared database, row-level security, tenant ID everywhere). Offer a dedicated schema or database to enterprise tenants through the same code and the tenant registry's routing |
| D-16 | Control-plane and data-plane code live in one repository and one deployable at first, but in **separate schemas with separate database roles**. The control plane never reads tenant business tables |
| D-17 | Activepieces runs as a **separate container with its own database and cache**; it is never given direct access to the application database |
| D-18 | Every cross-plane call is **signed, idempotent and versioned** |
| D-19 | A tenant's configuration is **desired state** held in the control plane and applied by a **reconciler** (section 5.4) |
| D-20 | The platform can **pause** any automation, connector, recipe release or tenant from the control plane (kill switch) |

# 4. Organizations, hats, personas and the access matrix

## 4.1 The problem

A large enterprise may be a vendor, a reseller and a solution provider at once, across divisions, brands or franchise locations. Modelling each as a separate company duplicates data and breaks reporting. Modelling it as one flat company breaks permissions.

## 4.2 The model (extends v1.1 A-02, A-05, A-06)

| ID | Object | Meaning | Example |
|---|---|---|---|
| A-30 | OrganizationGroup | An umbrella, holding or franchise system that links organizations. Holds shared policy, not shared data | Acme Holdings; a franchisor |
| A-02 | Organization | A legal or trading entity with its own workspace and data | Acme Software GmbH |
| A-31 | OrgUnit | A division, brand, region or franchise location inside an organization. A tree | Acme Cloud Division; Store 114 |
| A-32 | RoleFacet | A *declared way the organization or unit operates* in a context: Vendor, Reseller, SolutionProvider, Distributor, ServiceProvider, Buyer, Integrator. Scoped to an organization or unit, optionally to a vertical or program | Cloud Division is a Vendor; Services Division is a SolutionProvider and Reseller |
| A-05 | Hat | A person's authority: organization or unit + facet + capacity + scope + validity | Dana is Partner Manager for Cloud Division as Vendor |
| A-06 | Persona | UX profile. Never grants access | Channel Leader |
| A-33 | GroupPolicy | Rules a group sets for its members (templates, branding, required evidence, reporting roll-up) | Franchisor requires a standard onboarding program |

**Facet versus hat versus persona**

| Concept | Answers | Belongs to | Changes when |
|---|---|---|---|
| RoleFacet | What *kind of business role* does this entity play here? | Organization or OrgUnit | The business changes (rare) |
| Hat | What may *this person* do, for whom? | Person | Staffing changes (often) |
| Persona | How should the product *look and talk*? | Person | Preference or behaviour |

## 4.3 Rules

| ID | Rule |
|---|---|
| X-01 | An Organization MAY hold many facets at once. A facet is required for actions that only make sense in that role (a Reseller facet to register a deal; a Vendor facet to run a program) |
| X-02 | Sharing between sibling organizations or units is **never implicit**. It uses a CollaborationAgreement or a GroupPolicy with an explicit sharing scope (I-06) |
| X-03 | Group-level reporting reads an **aggregated projection** built from per-organization data with the sharing scope applied |
| X-04 | A person can hold hats in several organizations and units and switches between them; every action records the hat used |
| X-05 | Franchise model: the parent publishes program templates through GroupPolicy; each franchisee unit owns its data and may override only unlocked fields |
| X-06 | Vertical is a **context attribute** on programs, listings and facets, not a separate tenant. Verticals come from installed packs |
| X-07 | Persona is chosen from the hat and behaviour; it only filters presentation (I-04) |

## 4.4 The access matrix

**Dimensions:** Subject (Person → active Hat) × Action × Resource (owner org or unit, program, vertical, data class) × Context (relationship or agreement, facet, consent, entitlement, step-up).

**Decision order (the single policy function `Decide`)**

1. Authenticated and tenant resolved. 2. Organization boundary: same unit, same organization, same group with GroupPolicy, or an active Agreement. 3. Capacity permission: does the hat's capacity allow this action in the permission catalogue? 4. Facet requirement: does the owning entity hold the facet this action requires? 5. Scope: program, vertical and relationship match. 6. Data class and consent: public, agreement, private or restricted; purpose and consent valid. 7. Entitlement and plan. 8. Step-up needed? Then return the decision with reasons and the policy version.

**Published as data, not buried in code**

| Artifact | Contents |
|---|---|
| Permission catalogue (`permissions.yaml`) | Every action, resource type, required capacity, required facet, data class, step-up flag |
| Capacity matrix | Capacity × action table (generated view) |
| Persona matrix | Persona × navigation section and default card set (presentation only) |
| Policy bundle (versioned) | The above, released through the control plane |
| Generated test suite | One test per cell of the matrix, produced from the catalogue |
| "Who can do what" explorer (admin) | Pick a person, hat and resource; see the decision and the reasons |

**Example slice (illustrative)**

| Action | Capacity | Facet needed | Scope | Data class | Step-up |
|---|---|---|---|---|---|
| Submit deal registration | ProgramPartner | Reseller (or program-specific) | Enrolled program | Agreement | No |
| Approve deal registration | ProgramSponsor | Vendor | Own program | Agreement | Yes, for exceptions |
| Edit blueprint overlay | OrgAdmin | Vendor | Own program | Private | Yes |
| View earnings line | ProgramPartner or Finance | Any in program | Own lines | Restricted | No |
| Publish listing | OrgAdmin | Any | Own org | Public | Yes |

## 4.5 Onboarding a client with many hats

1. Ask "How do you work with partners?" and let the user tick **several**: we sell, we resell, we implement, we refer, we integrate, we buy.
2. Create the facets. For large clients, import or build the OrgUnit tree and attach facets to units.
3. Invite people; each invitation carries a hat (organization or unit, facet, capacity, scope).
4. Set the default hat and persona; show the hat switcher.
5. Apply GroupPolicy if an umbrella or franchisor exists.
6. Offer the matching recipe (section 5) for each facet.

# 5. Recipes: versioned, upgradable channels

## 5.1 Terms

| Term | Meaning |
|---|---|
| Capability module | Versioned code unit (C-01 and so on) with a configuration schema |
| Blueprint | A manifest that selects capability modules and defaults. Immutable per release |
| Vertical pack | An installable bundle: taxonomy, evidence types, card templates, explorers, journey definitions, automation recipes, KB seeds, vocabulary |
| Recipe | **Blueprint + packs + tenant overlay + program instance**, resolved and pinned |
| Lockfile | The exact versions and content hashes a program is running |
| Release channel | `stable` or `early`, per tenant |

**Recipe manifest (illustrative)**

```yaml
recipe: acme-implementation-network
blueprint: implementation-partner@4.2.0
packs:
  - ai-implementation@2.1.0
  - msp-common@1.4.0
overlay:                      # tenant-owned; never merged into the base
  deal_registration:
    approval_sla_hours: 48
  territory:
    mode: named_accounts
locked_fields: [security.sharing_policy]
release_channel: stable
lock:
  blueprint: sha256:ab12...
  packs: { ai-implementation: sha256:cd34..., msp-common: sha256:ef56... }
  resolved: sha256:9a8b...
```

## 5.2 Versioning rules

| ID | Rule |
|---|---|
| V-01 | Every release (blueprint, pack, capability, journey definition, connector) is **immutable** and semantically versioned |
| V-02 | A program runs a **lockfile**. Nothing changes without a new lock |
| V-03 | The tenant **overlay is stored separately** from the base. Upgrades never rewrite overlays |
| V-04 | **Patch** releases may auto-apply if the tenant enabled it; **minor** releases are opt-in with a preview; **major** releases need an explicit migration plan |
| V-05 | An upgrade runs a **three-way merge**: base at old version, base at new version, tenant overlay. Conflicts are listed per field; the tenant wins unless the field is locked |
| V-06 | Every upgrade starts with a **dry run** that produces an Upgrade Plan: diff, affected programs, in-flight journeys, data migrations, risks |
| V-07 | **In-flight journeys keep their pinned definition version** until they finish or an operator migrates them with a mapped state transition |
| V-08 | Keep the previous resolved snapshot for **one-click rollback**; data migrations must be reversible or have a forward-fix plan |
| V-09 | **Drift detection**: report when running configuration differs from its lock |
| V-10 | Releases carry a changelog, deprecation date and fixtures used in automated tests |

## 5.3 Upgrade flow

`Release published → eligible tenants identified (channel, version range) → dry run per tenant → Upgrade Plan sent to the tenant admin Inbox (or auto-applied for patches) → apply via reconciler → verify (health checks, smoke journeys) → success or automatic rollback`

## 5.4 The desired-state reconciler (one engine for provisioning, upgrades and repair)

A **TenantDesiredState** manifest (recipes, packs, programs, flows, pages, explorers, flags, entitlements) lives in the control plane. The reconciler compares it with what exists and runs **idempotent steps** until they match. It is used for:

- **Self-serve provisioning:** a new tenant is a desired-state manifest plus the plan below.
- **Upgrades:** change the lock, reconcile.
- **Repair:** drift is reconciled back.
- **Offboarding:** desired state becomes "archived."

**Provisioning plan for a new tenant (ordered, idempotent)**

1 Create tenant and workspace → 2 Apply entitlements → 3 Install packs (taxonomy, evidence types, vocabulary) → 4 Create programs from recipes → 5 Seed cards, explorers, pages and default views → 6 Install automation recipes and bindings → 7 Seed knowledge base → 8 Create roles, hats and invitations → 9 Run smoke checks → 10 Mark ready and start the first journey.

Each step records status, can be retried and emits events to the Platform Data Mart.

# 6. Journey and workflow engine

## 6.1 Layers

| Layer | Responsibility | Notes |
|---|---|---|
| L1 Journey definition | Declarative, versioned description of steps, gates, SLAs, participants | YAML in the recipe or pack; validated by schema |
| L2 Journey runtime | State machine over the database; durable timers; signals; process manager | **Stateless workers**; all state in Postgres |
| L3 Task layer | Human tasks and approvals appear in the Inbox | Section 9 |
| L4 Automation step | A step implemented by a native handler **or** by an Activepieces flow, selected by binding | Same contract either way |
| L5 Integration Gateway | The only path to third parties | Section 7.3 |

## 6.2 Rules

| ID | Rule |
|---|---|
| K-01 | The **journey database is the system of record**. Activepieces keeps run history only, never business state |
| K-02 | Flows change our data **only through signed API commands** (never direct database access) |
| K-03 | Every automation step has an **Automation Contract**: id, version, input schema, output schema, side-effect class (read, write-internal, write-external), idempotency key, timeout, retry policy, error taxonomy |
| K-04 | Every request and callback carries a **correlation ID** and an **idempotency key**; duplicates are ignored safely |
| K-05 | A step may be **bound** per tenant to `native`, `activepieces:{flow@version}` or `shadow` |
| K-06 | **Shadow mode** runs both implementations; only the native result is committed; differences are logged. Promote when the difference rate stays under a threshold |
| K-07 | Timeouts and dead letters create **Inbox tasks** for a human, not silent failures |
| K-08 | Compensation (undo) steps are defined for write-external contracts |
| K-09 | A journey instance **pins its definition version** (V-07) |
| K-10 | Tenants do not see the Activepieces builder in phase 1. Platform staff author flows and publish them as versioned recipes. Customer-editable flows are a later decision (section 6.5) |

## 6.3 Step types

| Type | Meaning |
|---|---|
| Task | Human work item with owner, SLA, form |
| Approval | Human decision bound to an exact action and content hash (SR-11) |
| Decision | Deterministic rule (policy as data) |
| Wait | For a signal, event or timer |
| Automation | Contracted native handler or flow |
| Notify | Email, chat or in-app message |
| AIPropose | AI drafts; a human reviews in the Inbox before anything is committed |
| Parallel / Join | Concurrent branches |
| SubJourney | Starts another journey and waits for its milestone |
| Publish | Releases content or data to targets |

## 6.4 Runtime choices

Build a **thin Postgres-backed runtime** behind an interface:

```csharp
public interface IJourneyRuntime
{
    Task<JourneyId> StartAsync(JourneyDefinitionRef def, StartContext ctx, CancellationToken ct);
    Task SignalAsync(JourneyId id, string signal, JsonElement payload, CancellationToken ct);
    Task CancelAsync(JourneyId id, string reason, CancellationToken ct);
    Task<JourneyState> QueryAsync(JourneyId id, CancellationToken ct);
}
```

It is a state machine, a timer table, an outbox and signal handling. Revisit Elsa Workflows v3 (embeddable, visual designer) or Temporal (managed cloud) **only if** one of these triggers is met: thousands of concurrent long-running instances; complex compensation across many external systems; customers must author workflows visually; or maintaining the runtime costs more than a person-week a month. [A]

## 6.5 Activepieces: how it fits and how the hand-off works

**Edition choice [V, secondary].** The community edition is MIT-licensed and self-hostable with unlimited flows and all pieces. The embed SDK and JWT single sign-on need the Embed edition, and enterprise governance features need a commercial licence. So: **phase 1 uses the community edition internally**; **buy Embed only if customers must edit flows inside your product.** Confirm current pricing before committing. [A]

**What you build**

- A custom **Upsll piece** (TypeScript) exposing **triggers** (domain events from the outbox, delivered by signed webhook) and **actions** (our API commands: create task, update opportunity, request approval). Flows then use domain verbs, not raw HTTP.
- A **flow registry**: each flow is exported as JSON, stored with its recipe release, and deployed by the reconciler through Activepieces' API. Never edit production flows by hand.
- **Tenant isolation:** one Activepieces project per tenant, or a shared project with tenant-scoped connections. Validate isolation before choosing. [A]
- **Credentials:** one owner per credential. If a recipe runs in Activepieces, its connection lives there and the control plane stores only a reference and a health status. If native, it lives in our vault.

**Hand-off protocol**

```
Journey step "Automation" -> AutomationRequested(correlationId, contractId, version, payload, idemKey)
   -> dispatcher reads the binding for this tenant:
        native            : in-process handler
        activepieces      : signed webhook -> flow -> actions via Upsll piece (API commands)
        shadow            : both; native result wins; diff logged
   -> result: POST /automation/callbacks (HMAC, idemKey)  ->  step Succeeded / Failed / TimedOut
   -> Failed or TimedOut -> retry policy -> Inbox task or ops dead-letter queue
```

**Upgrade path from stateless native steps to Activepieces:** keep the contract identical, add the flow, bind `shadow` for a few tenants, compare, promote to `activepieces`, and keep `native` as instant rollback. The journey definition never changes.

**What stays out of Activepieces:** approvals, money, permissions, publishing and anything that decides. Those are native steps.

# 7. Control plane, Platform Data Mart and Integration Gateway

## 7.1 Control-plane objects

Tenant · Plan and Entitlement · RecipeRelease · PackRelease · ConnectorRelease · AutomationContract · AutomationBinding · FlagDefinition · PolicyBundle · TenantDesiredState · HealthSnapshot · UsageMeter · SupportGrant · PlatformAuditEvent. It stores **no tenant business content**.

## 7.2 Platform Data Mart (tenant-agnostic monitoring)

| Source | Derived, pseudonymized signal | Used for |
|---|---|---|
| Outbox and journey events | Step counts, durations, stuck instances by definition version | Find stuck journeys; version health |
| Automation callbacks | Success, failure, latency per connector version | Connector quality; kill switch decisions |
| Reconciler | Provisioning and upgrade success, time to ready | Onboarding funnel |
| Usage meters | AI tokens, automation tasks, storage per tenant | Cost per tenant; plan limits |
| App telemetry | Feature use, setup score, hat switches | Product decisions |
| Search and matching | Queries, acceptance, relevance metrics | Quality gates |

Rules: pseudonymous tenant keys; no business payloads; platform staff access only; support access to tenant data needs a SupportGrant (SR-07); retention limits.

**Ops dashboards:** onboarding funnel · stuck journeys · dead-letter queue · connector health · release adoption and rollbacks · cost per tenant · SLOs · security events.

## 7.3 Integration Gateway: safe third-party integration inside workflows

| Control | Rule |
|---|---|
| Connector contract | Each connector has a versioned contract, schemas and a contract test suite; no ad-hoc HTTP from flows |
| Credentials | Vaulted per tenant; scoped, rotated; connection health visible |
| Egress | Allowlist of destinations per connector; everything else blocked |
| Data minimisation | Payload schemas drop fields not needed; **Restricted** data needs an explicit classification gate before it leaves |
| Consent and subprocessors | Enabling a connector updates the tenant's subprocessor list and requires admin consent |
| Resilience | Timeouts, retries with backoff, circuit breakers, rate limits |
| Kill switch | Platform can pause a connector, a flow or a tenant instantly (D-20) |
| Testing | Sandbox tenant and recorded fixtures; contract tests in CI |
| Cost | Metering per tenant and per connector |
| Versioning | Connector releases follow V-01 to V-10 |

# 8. Content system: cards, explorers, pages, metadata and publishing

## 8.1 The vocabulary (this resolves the confusion)

| Term | Definition | One-line test |
|---|---|---|
| **Fact** | A structured, sourced value about an entity (from v1.1) | "Is it data with a source?" |
| **Metadata** | Typed attributes attached to facts and cards: taxonomy terms, tags, facets, status, freshness, audience | "Can I filter or group by it?" |
| **Card** | A typed rendering unit bound to facts. Shows **one thing**. Has a schema, a template and **actions** | "Does it show one thing?" |
| **Collection** | A set of cards, static (hand-picked) or dynamic (a saved query) | "Is it a set?" |
| **Explorer** | An interactive, filterable view over a collection: facets, sorting, layout (grid, list, table, map), saved filters, permitted actions. **Finds many things** | "Can I search and filter it?" |
| **Page** | A layout of blocks (cards, collections, explorers, text) in a template. **Arranges** | "Is it a composed screen?" |
| **Dashboard** | A page made of metric cards, personalizable per user | |
| **Publication** | A versioned release of a page, card or explorer to targets (web, API, MCP, widget, email) | "Is it released to an audience?" |
| **SavedView** | A user's own filters, sort, layout and pins on an explorer | "Is it personal?" |

**Rule of thumb:** *Cards show one thing. Explorers find many things. Pages arrange. Publications release.*

## 8.2 Personalization layers (resolved in order)

Platform default → vertical pack → tenant → persona → hat → user (favorites, pins, saved views). Locked items stay locked. Every layer is data, versioned like a recipe (V-01 to V-10).

## 8.3 Card Action registry (right-click and long-press menus)

Each **ActionDefinition** has: id, label, applicable card types, required permission and facet, persona or hat visibility, confirmation or step-up, and a handler (a command, a journey start, or an automation). Menus are assembled at runtime from **card × hat × persona × entitlement**.

| Customization | Who | Limit |
|---|---|---|
| Reorder or hide optional actions | Each user | Mandatory actions stay |
| Add or remove actions from approved sets | Tenant admin | Only registered actions; permission checks still apply |
| Define new action sets for a pack | Platform | Released as part of the pack |

Examples: *Favorite*, *Add to shortlist*, *Request introduction*, *Share to Inbox*, *Open journey*, *Create task*, *Draft RFP answer*, *Compare*, *Report a problem*. On mobile, long-press shows the same menu.

## 8.4 Authoring for power users

| Tool | Creates | Guardrails |
|---|---|---|
| Card Studio | Card types and templates | Only registered bindings; no raw HTML or script; accessibility checks |
| Explorer Builder | Filtered explorers | Only permitted facets; query cost limits |
| Page Composer | Pages and dashboards | Template slots; preview as persona and hat |
| Publication workflow | Release to targets | The lifecycle and gates from v1.1 section 5.2, plus permission lint (nothing confidential can be published) |

## 8.5 Data objects

ContentCard · CardType · CardTemplate · Collection · Explorer · Page · PageBlock · SavedView · Favorite · ActionDefinition · ActionBinding · Publication.

# 9. The universal Inbox and work surface

The Inbox is a **projection over work items and notifications** and the one place to act.

| Item type | Created by |
|---|---|
| Task, Approval | Journey steps |
| Introduction request | J-02 |
| RFP or questionnaire question | RFP agent (review queue) |
| AI proposal to review | AIPropose steps |
| Expiry or freshness reminder | J-10 |
| Upgrade plan to approve | Recipe upgrades (V-06) |
| Mention, reply, offer | Network layer |
| Alert | Ops, integrations |

**Features:** filter by hat and persona; assign, delegate, snooze; SLA timers; bulk actions; favorites and pins; saved views; action from email, Teams or Slack through scoped links; daily digest; keyboard shortcuts and mobile gestures.

**Data:** InboxItem (polymorphic reference to a subject), Assignment, Watch, Favorite, NotificationPreference. Rule: items never copy confidential data; they reference the subject and re-check permission on open.

# 10. Knowledge base and AI services

## 10.1 Knowledge base

| Stage | What happens |
|---|---|
| Sources | Program documents, past RFPs and questionnaires, security and compliance documents, case references, product sheets, URLs, approved Q&A |
| Ingest | Extract text, stable section and page anchors, hash and version |
| Index | Chunk with anchors; embed; attach **ACL tags**, data class, owner, freshness and expiry |
| Retrieve | Always through the policy decision point; citations required |
| Maintain | Stale detection, owner reconfirmation, duplicate detection |

**Answer Library:** approved answers with owner, tags, expiry, source anchors and reuse history. Approved answers from RFP reviews feed back into the library.

## 10.2 RFP and questionnaire response agent (R-10)

`Upload or paste → parse into questions → match to Answer Library → draft with citations and confidence → route low-confidence items to subject experts → review in the Inbox → approve → export (Word, Excel or paste-ready) → learn`

Guardrails: never auto-submit; customer-specific confidentiality classes; cite or abstain; show source and age of each answer; track who approved what; evaluate on a golden set of past RFPs.

## 10.3 Deal matching

Detect duplicates and conflicts between a new registration and existing opportunities, accounts and registrations using deterministic keys (domain, registry ID), fuzzy matching and embeddings. Output: a ranked list with reasons; a human adjudicates (I-07, I-09). Feeds the conflict policy (C-06).

## 10.4 Partner matching

The v1.1 pipeline (section 7.3) with modes M1 to M5. Deal matching and partner matching **share one matching core**: features, scoring configuration, explanations, evaluation harness and consent filtering.

# 11. Network layer and marketplace

**A correction to the framing.** Free-form social media is an engagement amplifier, not a wedge. It is expensive to moderate and hard to bootstrap. Build the **structured** version first, where every post can start a journey.

## 11.1 Five primitives

| Primitive | Meaning | Examples |
|---|---|---|
| Profile | Public face of an organization, unit or (with consent) a person-in-hat | Capabilities, evidence, programs |
| Post | A typed, schema-bound update | Announcement, win (approved), event, capacity available, program opening |
| Group | A community scoped by vertical, program or interest | "AI implementation partners" |
| Thread | Discussion or message scoped to a group, post or relationship | Q&A, direct messages |
| Offer or Request | Marketplace object | "Looking for a delivery partner", "We offer SOC 2 readiness" |

## 11.2 Rules

- Identity is company-scoped: people appear with their employer and hat. No anonymous posting.
- Posts have types with schemas, so they can **trigger journeys** (a Request becomes an Introduction, then a Journey).
- Feed ranking is explainable (relevance plus recency); no paid rank (I-08).
- Moderation: report, review queue, rate limits, anti-spam, retaliation protections for reviews.
- No contact-data export; introductions go through consent (J-02).
- **Employee enablement:** employees hold limited hats, can share approved cards, and publish skills and availability (C-12). Do not build individual performance surveillance.

## 11.3 Marketplace v1

Listings, requests and offers that open **introductions** and then journeys. **No payments, escrow or checkout** in year one. Hand off to CRM, marketplaces or the customer's own commerce. Add paid features later if outcomes can be observed (R-13).

## 11.4 Phasing

1. Network feed of **platform-generated events** (partner certified, program opened, evidence reconfirmed). 2. Typed posts and groups. 3. Requests and offers. 4. Threads and messaging. Gate each step on activity and moderation load.

# 12. Consolidating the 265 entities and tables

I have not seen the 265 entities. This is the method; the Prompt Pack contains the extraction script and prompts.

## 12.1 Target table families (patterns)

| Family | Absorbs | Technique |
|---|---|---|
| Party | Organizations, units, people, contacts, memberships, hats, facets | Class-per-concept for core; shared columns; no per-type duplicate tables |
| Relationship and Agreement | Partner links, agreements, consent grants, enrollments | One typed edge table plus agreement and consent tables |
| Term (lookup) | Every enumeration, taxonomy, status list, category | One Term table with a *namespace*, plus versioned vocabularies from packs |
| Document, Evidence, Asset | Files, certificates, case studies, attachments | One document table, one evidence table referencing it |
| Event and Audit | Activity logs, history, audit, analytics events | One append-only event store with typed payload and subject reference |
| Work | Tasks, approvals, inbox items, journey steps, reminders | WorkItem plus Journey tables |
| Configuration | Settings, policies, overlays, recipes, flags | Versioned config with scope and schema; JSON only here |
| Content | Cards, pages, explorers, templates, views | The 12 objects in 8.5 |
| Commercial | Opportunities, registrations, attribution, ledger | Small aggregates; ledger append-only |
| Communication | Messages, threads, notifications, posts | Thread, Message, Notification |
| Integration | Connections, mappings, sync state, webhooks | Connection, Mapping, SyncCursor, DeadLetter |
| Billing and Entitlement | Plans, subscriptions, usage, limits | Control-plane tables |

**Goal [A]:** reduce 265 to roughly 90–120 tables without losing capability. The real number comes from your catalogue.

## 12.2 Consolidation rules

| ID | Rule |
|---|---|
| E-01 | Merge tables with the same lifecycle, owner and access pattern into one table with a discriminator |
| E-02 | Keep tables separate when invariants, retention or access control differ |
| E-03 | All lookups become Terms; no new enum table without approval |
| E-04 | Polymorphic references use (SubjectType, SubjectId) with a registry and integrity checks |
| E-05 | JSON columns only for configuration and payloads; relational facts stay relational |
| E-06 | Standard columns everywhere: Id, TenantId (where applicable), CreatedAt, CreatedBy (hat), UpdatedAt, RowVersion, DataClass |
| E-07 | Existing screens and APIs keep working through **compatibility views** during migration |
| E-08 | Migrations use expand/contract; delete an old table only after zero reads for a full release cycle |
| E-09 | **Freeze:** no new table is added without naming its family in the catalogue |

## 12.3 Process

Extract (script) → classify each entity (family, module, owner, tenant scope, data class, lifecycle, read/write paths, screens that use it) → cluster duplicates → propose merges with risk rating → create compatibility views → migrate in small steps → delete. Output: the **Entity Catalogue** (template in the Prompt Pack) and a migration backlog.

# 13. User journeys: making 30 flows AI-buildable

## 13.1 Journey Spec standard

Each journey is one file with these sections: purpose and metric · actors and hats · trigger · preconditions · states and transitions · screens (route, template, cards) · entities read and written · API contracts · events emitted · automation contracts · rules and policies · permissions (from the matrix) · edge cases · analytics · acceptance tests (Gherkin) · non-functional needs.

**Maturity levels**

| Level | Meaning | Who can build it |
|---|---|---|
| L0 | Title and one line | Nobody |
| L1 | Steps and actors described in prose | A human with questions |
| L2 | States, entities, screens and rules specified | A human or an agent with review |
| L3 | **All sections complete, edge cases listed, tests written** | An agent end to end |

**Edge-case checklist (minimum 10 per journey):** timeout · duplicate submission · concurrent edits · permission revoked mid-flow · consent revoked · partial failure of an automation · retry after failure · abusive or spam input · counterparty never responds · tenant offboarding mid-flow · recipe upgrade mid-flow (V-07) · rollback · offline or mobile resume.

## 13.2 Journey catalogue (30)

J-01 to J-12 are defined in v1.1. New in v2.0:

| ID | Journey | Notes |
|---|---|---|
| J-13 | Self-serve signup and provisioning plan | Uses the reconciler (5.4) |
| J-14 | Multi-hat setup: add facet, unit or hat | 4.5 |
| J-15 | Invite and onboard a partner by magic link | Free partner workspace |
| J-16 | Create a program from a recipe | 5.1 |
| J-17 | Recipe upgrade: dry run, apply, verify, rollback | 5.3 |
| J-18 | Automation binding upgrade: native → shadow → Activepieces | 6.5 |
| J-19 | Connect a CRM and first sync | Integration Gateway |
| J-20 | Author and publish a card, explorer or page | 8.4 |
| J-21 | Respond to an RFP or questionnaire | 10.2 |
| J-22 | Deal matching and conflict check | 10.3 |
| J-23 | Sponsor partner matching and outreach | M2 |
| J-24 | Knowledge base ingest and answer library curation | 10.1 |
| J-25 | Post or request → introduction | 11 |
| J-26 | Marketplace listing and request response | 11.3 |
| J-27 | Capacity and skills enablement for employees | C-12 |
| J-28 | Plan change, billing and entitlement update | Control plane |
| J-29 | Consented support session | SR-07 |
| J-30 | Ops: stuck-journey remediation | 7.2 |

## 13.3 Exemplar: J-05 "Register deal → decision" (L3 skeleton)

```yaml
journey: J-05-register-deal
version: 1.0.0
purpose: Let a partner register a pursuit and get an accountable, explainable decision.
metric: median time to decision; share of decisions inside SLA
actors:
  - hat: ProgramPartner (facet: Reseller)   # submits
  - hat: ProgramSponsor (facet: Vendor)     # decides
trigger: partner chooses "Register deal" on an opportunity card (action: deal.register)
preconditions:
  - enrollment approved and in good standing (C-01)
  - program has capability deal_registration (C-05)
states: [Draft, Submitted, ConflictCheck, InReview, Approved, Rejected, Appealed, Expired]
steps:
  - id: submit        type: Task      owner: partner      sla: none
  - id: conflict      type: Automation contract: deal.conflict-check@1  binding: native
  - id: review        type: Approval  owner: sponsor      sla_hours: "{{overlay.approval_sla_hours}}"
  - id: notify        type: Notify    channel: [inbox, email]
  - id: appeal-window type: Wait      timer_days: "{{overlay.appeal_window_days}}"
entities_write: [DealRegistration, AttributionDecision?(no), InboxItem, Event]
screens:
  - route: /app/work/registrations/new   template: T-L6   cards: [OpportunityCard, TermsCard]
  - route: /app/work/registrations/{id}  template: T-L6   cards: [RegistrationStatusCard, ConflictCard, TimelineCard]
events: [RegistrationSubmitted, ConflictDetected, RegistrationDecided, RegistrationExpired]
rules: [precedence per overlay.conflict_policy (I-07); decision must record reason code]
permissions: see 4.4 slice (submit, approve)
edge_cases:
  - duplicate submit for same account -> idempotency key + conflict step
  - sponsor never responds -> SLA breach creates Inbox escalation, then auto-reminder
  - partner loses enrollment mid-flow -> registration frozen, sponsor notified
  - recipe upgrade mid-flow -> instance keeps pinned definition (V-07)
  - CRM sync fails -> dead letter + Inbox ops task; decision still recorded
  - consent revoked -> stop new sharing; decision record retained (6.4 v1.1)
acceptance:
  - Given a partner with a Reseller facet in an enrolled program,
    When they submit a registration with no conflicts,
    Then it reaches InReview within 1 minute and the sponsor has an Inbox approval.
```

# 14. Self-serve design

| Principle | Implementation |
|---|---|
| Every setup step is a **checklist item with a Setup Score** | Progress bar, "next best action" card in the Inbox |
| **Sandbox first:** a flagged sample tenant that can be reset | Sample data marked and never indexed (I-14) |
| **Recipes, not blank pages:** the first program is created from a recipe | 5.1 |
| **Progressive disclosure:** advanced settings appear after the basics work | Persona-aware navigation |
| **Self-serve admin:** invites, roles, hats, billing, exports without tickets | J-14, J-28 |
| **Guardrails instead of gatekeepers:** automatic checks (permission lint, publication gates, upgrade dry run) | 5, 8 |
| **Proactive help:** stuck-detection nudges and cited in-app answers | Platform Data Mart (7.2), KB (10.1) |
| **Metric:** time to first value under seven days; onboarding labour per customer | D-08 |

# 15. Engineering practices and stack upgrades

Version numbers need checking when you start. [A]

| Practice | Why | Note |
|---|---|---|
| .NET 10 LTS with minimal APIs and **vertical slices** per feature | Fewer layers; AI-friendly units | Keep module boundaries (D-12) |
| EF Core 10 for writes; SQL or lightweight reads for projections | Speed and clarity | Check vector support for your database path |
| **Transactional outbox** with idempotent consumers | Reliable events | Foundation for the journey runtime |
| **Contract-first APIs:** OpenAPI → generated TypeScript client | Wiring the existing front end safely | Fails the build on drift |
| ProblemDetails errors, API versioning, rate limiting middleware | Consistent behaviour | |
| HybridCache and output caching for public pages | Performance with little code | Cache keys include audience and authorization version |
| Resilience pipelines (retries, timeouts, circuit breakers) | Safe integrations | Used by the Integration Gateway |
| OpenTelemetry end to end | One trace from click to automation | Correlation IDs (K-04) |
| .NET Aspire for local orchestration only | Easy dev setup | Not the domain architecture |
| **Testcontainers** for integration tests | Real Postgres in CI | |
| **Architecture tests** for module boundaries; **snapshot tests** for contracts | Prevent erosion | |
| **Playwright** end-to-end and visual regression | Protect the design you already like | Pin screenshots of key pages |
| Storybook for the shared component library | Design system as code | One token package for both sites |
| Feature flags with expiry | Safe rollout | Owned by the control plane |
| Trunk-based development, small pull requests, preview environments | Speed with few people | |
| Dependency and secret scanning | Security hygiene | Automated updates |
| `docs/` as the source of truth: ADRs, entity catalogue, journey specs, STATE.md | Persistence across AI sessions | Prompt Pack |

# 16. Master feature register (consolidated and regrouped)

M = mandatory (v1.1 M01–M15), O = optional (v1.1 O01–O50), N = new in v2.0. **Tier:** *Core* = build first, *Next* = after design-partner proof, *Later* = only on validated demand. Phases: P1 months 1–3, P2 months 4–6, P3 months 7–12.

## 16.1 New features in v2.0

| ID | Feature | Group | Tier | Phase |
|---|---|---|---|---|
| N01 | Control plane and tenant registry | L | Core | P1 |
| N02 | Tenant desired-state reconciler | L | Core | P1 |
| N03 | Recipe registry: releases, lockfiles | C | Core | P1 |
| N04 | Recipe upgrade planner: three-way merge, dry run, rollback | C | Next | P2 |
| N05 | Vertical pack installer | C | Core | P1 |
| N06 | Journey definition registry and versioning | D | Core | P1 |
| N07 | Journey runtime (Postgres, durable timers, signals) | D | Core | P1 |
| N08 | Automation contract registry | D | Core | P1 |
| N09 | Activepieces plane (community edition) and Upsll piece | D | Next | P2 |
| N10 | Automation binding and shadow mode | D | Next | P2 |
| N11 | Integration Gateway: vault, egress allowlist, schemas, kill switch | K | Core | P1 |
| N12 | Connector contract tests and sandbox | K | Next | P2 |
| N13 | Platform Data Mart and ops dashboards | L | Core | P1–P2 |
| N14 | Organization groups, units and role facets | A | Core | P1 |
| N15 | Hat and persona switcher | A | Core | P1 |
| N16 | Access matrix explorer and generated tests | A | Core | P1 |
| N17 | Permission catalogue and policy bundles | A | Core | P1 |
| N18 | Card type registry | H | Core | P1 |
| N19 | Card Studio | H | Next | P2 |
| N20 | Explorer builder (filtered explorers) | H | Next | P2 |
| N21 | Page composer | H | Next | P2 |
| N22 | Card Action registry (context menus by role and hat) | H | Core | P1 |
| N23 | Saved views and favorites | H | Core | P1 |
| N24 | Universal Inbox | H | Core | P1 |
| N25 | Notification digest and chat or email actions | H | Next | P2 |
| N26 | Knowledge base ingestion and ACL retrieval | I | Core | P2 |
| N27 | Answer library | I | Core | P2 |
| N28 | RFP and questionnaire response agent | I | Next | P2–P3 |
| N29 | Deal matching and conflict detection | E | Core | P2 |
| N30 | Partner matching service v2 (shared core) | I | Core | P1–P2 |
| N31 | Network feed of platform events | J | Next | P3 |
| N32 | Posts, groups and threads | J | Later | P3+ |
| N33 | Marketplace: listings, requests, offers | J | Next | P3 |
| N34 | Workforce enablement: skills, capacity, employee sharing | G | Next | P3 |
| N35 | Self-serve checklist and Setup Score | L | Core | P1 |
| N36 | Sandbox tenant with flagged sample data | L | Core | P1 |
| N37 | Entity catalogue and architecture fitness tests | L | Core | P1 |
| N38 | Agent-ready repository documents and STATE files | L | Core | P1 |
| N39 | Cost metering per tenant (AI, automation) | L | Next | P2 |
| N40 | Usage-based entitlements and plan upgrades | L | Core | P1–P2 |

## 16.2 Regrouped register (all features by group)

| Group | Mandatory (M) | Optional (O) | New (N) |
|---|---|---|---|
| **A Identity, organizations and access** | M01 Org identity and claim; M02 Cross-company permissions; M03 Consent and publication | O42 Agent identity registry; O49 SSO, SCIM, residency | N14, N15, N16, N17 |
| **B Directory, discovery and trust** | M04 Company and program profiles; M05 Search and comparison; M06 Evidence and freshness; M07 Double opt-in introductions | O01 Saved-search alerts; O02 Fit recommendations; O03 RFP brief builder; O04 Shortlist rooms; O05 Multi-language; O06 Experience reviews; O07 Company passport; O08 Registry adapters; O09 Insurance monitoring; O10 Review appeals | — |
| **C Programs, recipes and packs** | M08 Blueprint composition; M09 Enrollment and terms | O11 Tier progression; O12 Territory; O13 Referral rewards; O14 Distributor hierarchy; O15 Pack marketplace | N03, N04, N05 |
| **D Journeys and workflow** | M12 Cross-channel journey | — | N06, N07, N08, N09, N10 |
| **E Revenue and co-sell** | M11 Deal registration and disputes | O21 Joint account plans; O22 Account overlap; O23 Private-set intersection pilot; O24 Buyer room; O25 Contribution evidence | N29 |
| **F Finance** | — | O26 MDF; O27 Rebates; O28 Earnings statements; O29 Payment provider; O30 Multi-currency | — |
| **G Delivery and workforce** | — | O17 Skills matrix; O31 Capacity availability; O32 Co-delivery work packages; O33 Subcontractor onboarding; O34 SOW handoff; O35 Delivery references | N34 |
| **H Content, experience and Inbox** | M14 Cited program summaries | O18 Change diffs; O19 Obligation extraction; O20 Grounded Q&A | N18–N25 |
| **I Knowledge base and AI** | — | O41 Read-only MCP; O43 Approval-bound agent actions; O44 A2A adapter; O45 Injection and evaluation harness | N26, N27, N28, N30 |
| **J Network and marketplace** | — | — | N31, N32, N33 |
| **K Integrations and platform** | M13 One production CRM connector | O16 Learning integration; O36 Second CRM; O37 Teams and Slack actions; O38 Signing; O39 Marketplace opportunity sync; O40 Customer integration marketplace; O50 API plans and sandbox | N11, N12 |
| **L Operations, analytics and self-serve** | M15 Billing, telemetry and recovery | O46 Activation funnel; O47 Partner health; O48 Attribution dashboards | N01, N02, N13, N35, N36, N37, N38, N39, N40 |

**Count:** 15 mandatory + 50 optional + 40 new = 105. Do not build them all. The **Core** tier is the first release; everything else needs a stop/go decision with evidence.

# 17. Gaps and risks

| ID | Gap or risk | Mitigation |
|---|---|---|
| G-13 | The 265-entity inventory and current code are unseen | Run the audit prompts (Prompt Pack A1–A3) before any refactor |
| G-14 | Existing front-end routes and components are unmapped to API contracts | Prompt A4 produces a route-to-contract map; generate typed clients |
| G-15 | Activepieces edition and pricing are unconfirmed; tenant isolation in a shared instance is unvalidated | Time-boxed spike: one tenant, one flow, one callback; decide edition after |
| G-16 | Three-way merge for recipe upgrades is complex | Start with field-level merge on typed config; forbid structural changes in minor releases |
| G-17 | AI RFP answers can leak confidential or stale content | ACL-tagged retrieval, expiry on answers, mandatory review, evaluation set [L] |
| G-18 | Network layer moderation and cold start | Phase 11.4; platform-generated events first |
| G-19 | Marketplace and payments liability | No payments in year one [L] |
| G-20 | Multi-hat modelling confuses users | Wizard, visible hat switcher, "acting as" banner, hat recorded on every action |
| G-21 | Single maintainers for runtime, reconciler and gateway | Keep each under a size budget; heavy tests; consider buying pieces if cost exceeds a person-week a month |
| G-22 | Entity consolidation breaks screens | Compatibility views, expand/contract, snapshot tests |
| G-23 | Too many features | Core tier only; kill criteria per feature (D-08) |
| G-24 | Platform Data Mart could become a privacy problem | Pseudonymize, no payloads, strict access, retention |
| G-25 | Price and competitor claims drift | Re-verify before publishing (G-12) |
| G-26 | Legal review of agent, exclusivity and data terms | Counsel before templates ship [L] |

# 18. A plan for fewer than five people (iterative, with budgets)

**Working method:** every cycle is *plan → build one vertical slice → test → document → decide*. Agents do the drafting and building; humans own priorities, reviews and legal or commercial judgement.

| Phase | Weeks | Goal | Deliverables | Exit check |
|---|---|---|---|---|
| 0 Baseline | 1–2 | Know what exists | Code and entity audit; route and API map; Entity Catalogue v0; Journey Spec backlog at L1 | Catalogue covers all 265; top 10 journeys at L2 |
| 1 Foundations | 3–8 | Safe core | Control plane lite; permission catalogue and generated tests; hats and facets; Inbox; journey runtime with 5 journeys; recipe registry v1; card action registry | Matrix tests pass; J-02, J-03, J-05, J-13, J-15 run end to end |
| 2 Self-serve and content | 9–16 | Customers can start alone | Reconciler; provisioning plan; setup score; sandbox; Card/Explorer/Page tooling v1; CRM connector through the gateway; billing | A new tenant is ready in under one day without staff |
| 3 Intelligence and automation | 17–26 | Differentiators | Knowledge base and answer library; RFP agent; deal and partner matching; Activepieces spike then plane; shadow mode | RFP draft accepted with edits; shadow diff rate under target |
| 4 Network and scale | 27–52 | Growth | Recipe upgrade planner; network feed; requests and offers; second CRM; pack installer for a second vertical | One recipe upgrade applied to live tenants with zero rollbacks needed |

**What to cut or delay first:** free-form social, payments, a second CRM, customer-editable flows, private-set intersection, a pack marketplace.

# 19. Open questions

1. Please paste or describe the **265 entities** (or run the extraction script in the Prompt Pack and paste its output).
2. Is my reading of "data independent of the tenant" (control plane plus Platform Data Mart) right?
3. Which of your 20 to 30 flows are most important to customers, and which already exist in code?
4. Do customers ever need to edit automations themselves, or can the platform team author them?
5. Which vertical comes second after the AI-implementation wedge?
6. What does the front end use today (framework, component library, routing)? The prompts need it.
7. Who is the fifth person: engineering, growth or operations?

# 20. Change log

v2.0 (28 Oct 2026): added five planes, control plane and Platform Data Mart, Activepieces automation plane and hand-off, versioned recipes with a reconciler, multi-hat organization model and access matrix, content vocabulary and Card Action registry, Inbox, knowledge base and AI services, Network layer, entity consolidation method, Journey Spec standard with 30 journeys and an exemplar, self-serve design, engineering practices, master feature register (105 features), gaps and a plan for fewer than five people.


I can't attach files directly, but everything below is written as self-contained, copy-pasteable documents with filenames. Save each fenced block as the named .md file. They are designed to be dropped into a repo together and fed to an AI in order.

Total artifact set: 15 documents. Reading order matters — each later document assumes the earlier ones.

---

ARTIFACT 00 — 00-README-artifact-index.md

```markdown
# Upsll / partner.directory — Architecture v2.0 Artifact Set

Version 2.0 | 28 October 2026 | Owner: Founder | Audience: 3–4 person team + AI agents

## What this set is
A complete, executable architecture specification. It supersedes v1.1 for
implementation purposes. v1.1 remains the source of strategy intent; this set
is the source of implementation truth.

## Authority and precedence
If documents conflict, resolve in this order:
1. `07-multi-hat-authorization-spec.md` (security invariants)
2. `03-entity-ledger.md` (domain truth)
3. `04-journey-state-machines.md` (process truth)
4. `05-workflow-engine-adr.md` (execution truth)
5. Everything else

Never resolve a conflict by inventing a rule. Log it in
`11-gap-register.md` and escalate.

## Reading order for a human
00 → 01 → 02 → 11 (gaps) → 03 → 07 → 04 → 05 → 06 → 08 → 09 → 10 → 12 → 14

## Reading order for an AI agent
The agent's role determines its inputs. See `12-agent-orchestration-protocol.md`.
No agent reads the full set. Each agent reads its declared inputs only.

## Artifact manifest
| File | Purpose | Owner role | Blocks |
|---|---|---|---|
| 00-README-artifact-index.md | This index | — | — |
| 01-code-reality-map.md | Factual audit of current code | Auditor | 02, 03 |
| 02-denial-test-suite-spec.md | Authorization test design | Auditor→Executor | 07 |
| 03-entity-ledger.md | 25 aggregates, dispositions, reconciliation | Planner | 04, 06, 08 |
| 04-journey-state-machines.md | 12 journeys as YAML | Planner | 05, 10 |
| 05-workflow-engine-adr.md | Stateless vs Windmill vs ActivePieces | Planner | 14 |
| 06-content-pipeline-spec.md | Card/Page/Explorer/Publication | Planner | 08 |
| 07-multi-hat-authorization-spec.md | Hat model, PDP, cross-matrix | Planner | 02, 04 |
| 08-channel-recipe-versioning.md | Versioned channel composition | Planner | 14 |
| 09-integration-adapter-contract.md | Adapters, Tools, isolation | Planner | 04 |
| 10-workflow-audit-plane.md | Platform-level audit schema | Planner | 04, 14 |
| 11-gap-register.md | 20 gaps found + resolutions | Auditor | all |
| 12-agent-orchestration-protocol.md | Agent roles, file handoff | Founder | all |
| 13-master-prompt.md | The orchestrator prompt | — | — |
| 14-roadmap-and-budget.md | 12-week plan, tiers | Founder | — |

## Definition of Done (applies to every artifact)
An artifact is Done when:
- Every table has no blank cells (use [A] explicitly if unknown)
- Every ID referenced exists in another artifact or is defined here
- Every invariant cited by ID matches 07
- A Reviewer agent can verify it against 03 + 07 without asking questions

## Version control
All artifacts live in `/docs/architecture/` in the repo. Changes go through PR.
A change to 03 or 07 requires founder approval. A change to 04 requires founder
approval plus a migration note in 10.
```

---

ARTIFACT 01 — 01-code-reality-map.md

```markdown
# Code Reality Map
Status: TEMPLATE — Auditor must fill every cell. No blanks permitted.
Purpose: Establish what the code ACTUALLY does before designing on top of it.
Method: Static reading + targeted grep. No runtime testing in this artifact.

## Instruction to Auditor
You are read-only. You produce facts. You do NOT recommend, design, or fix.
For every cell you cannot determine, write `UNKNOWN — needs <specific action>`
and add a row to Section E6 (Unknown Register). Do not guess.

---

## E1. Application Inventory

| App | Project path | Purpose | Auth entry point | Shared auth lib? | Notes |
|---|---|---|---|---|---|
| Directory (SSR) | | | | | |
| Upsll web (BFF/SPA host) | | | | | |
| API / modular monolith | | | | | |
| Workers / hosted services | | | | | |
| Admin console | | | | | |
| Other: ______ | | | | | |

**Question E1.1:** Do Directory and Upsll reference the SAME auth assembly, or
two copies? Evidence: ______

## E2. Policy Decision Points

| # | Entry point (file:class:method) | Signature | Callers | Covers controllers? | Covers minimal APIs? | Covers workers? | Covers background jobs? |
|---|---|---|---|---|---|---|---|
| 1 | | | | | | | |
| 2 | | | | | | | |

**Count of distinct PDP entry points: ____**
If > 1, flag as TRAP-1 candidate in E4.

## E3. Endpoint Inventory and PDP Coverage

Every routable endpoint gets a row. This table is the input to Artifact 02.

| Route | Verb | Handler file | Calls PDP? (Y/N/UNKNOWN) | Resource check present? | Tenant source (server/client) | Notes |
|---|---|---|---|---|---|---|
| | | | | | | |

**Summary counts:** Total endpoints ___ | Call PDP ___ | Do not call PDP ___

## E4. Bypass Risk Register

| Trap | Present? (Y/N) | Evidence (file:line) | Severity | Fixes in artifact |
|---|---|---|---|---|
| T1 Attribute-only RBAC (`[Authorize(Roles=...)]` with no resource check) | | | | 07 |
| T2 Tenant filter is the only guard (EF global filter, no resource check) | | | | 07 |
| T3 Service-layer checks with direct-repo bypasses | | | | 02 |
| T4 Workers bypass PDP | | | | 02, 10 |
| T5 Per-app auth duplication (Directory vs Upsll drifted) | | | | 07 |
| T6 Client-supplied tenant/org/hat ID trusted by server | | | | 07 |
| T7 Public endpoint reads private table | | | | 06 |
| T8 Private field leaks into hydration/JSON-LD | | | | 06 |

Any T1–T8 = Y is a **P0 finding**. P0 findings block Phase 1 (see 14).

## E5. Isolation and Projection Mechanisms

| Mechanism | Present? | Where | Applied to which entities | Gaps |
|---|---|---|---|---|
| EF Core global query filter | | | | |
| Explicit resource-level authorization | | | | |
| Postgres row-level security (actually enabled?) | | | | |
| Server-derived tenant (from session/claims) | | | | |
| Public projection / DTO layer | | | | |
| JSON-LD generation (source of data) | | | | |
| SSR hydration payload construction | | | | |

## E6. Unknown Register

| # | Unknown | Why it matters | How to resolve | Owner | Blocking? |
|---|---|---|---|---|---|
| U1 | | | | | |

## E7. Worker / Hosted Service Inventory

| Worker | File | Trigger | Reads tenant data? | Calls PDP? | Idempotent? | DLQ? |
|---|---|---|---|---|---|---|
| | | | | | | |

## E8. Data Access Inventory

| DbContext | Owns which tables | Cross-module reads? | Module boundary respected? |
|---|---|---|---|
| | | | |

## Auditor Sign-off Block
- Endpoints inventoried: ____
- Workers inventoried: ____
- P0 findings: ____
- Unknowns: ____
- Statement: "This map contains no design recommendations." [ ] confirmed
```

---

ARTIFACT 02 — 02-denial-test-suite-spec.md

```markdown
# Authorization Denial Test Suite — Design and Generation Spec
Purpose: Prove that centralization is real BEFORE building the Hat model on it.
Status: Spec complete. Executor generates code from this.

## Why denial tests, not happy-path tests
A happy-path test passes when authorization is broken but permissive.
A denial test fails loudly when authorization is broken. Since you have zero
integration tests, denial tests are the highest-value first investment: they
are parameterized (cheap per endpoint) and they produce a regression net that
protects every subsequent refactor.

## Test taxonomy (7 classes — every endpoint gets at least classes 1, 2, 6)

| Class | Name | Asserts | Applies to |
|---|---|---|---|
| D1 | Unauthenticated denial | No identity → 401 | Every endpoint |
| D2 | Wrong-tenant denial | Valid identity, other tenant's resource ID → 403/404 | Every tenant-scoped endpoint |
| D3 | Wrong-hat denial | Valid identity, valid tenant, insufficient capacity → 403 | Every capability-gated endpoint |
| D4 | Expired-hat denial | Hat.valid_until in past → 403 | Every capability-gated endpoint |
| D5 | Consent-revoked denial | Valid hat, revoked ConsentGrant → 403 | Every consent-scoped read/write |
| D6 | Public-projection denial | Authenticated user requests private field via public route → field absent | Every public route |
| D7 | Worker-scope denial | Worker processes message outside its tenant scope → rejected + dead-lettered | Every worker |

## Test case generation rules

### Rule G1 — Endpoint table is the source
Generate one test per (endpoint × applicable class). Do not hand-write.
The parameterization source is `01-code-reality-map.md` §E3.

### Rule G2 — Fixtures are deterministic
Three tenants (T1, T2, T3), three persons, three hats per person, fixed GUIDs.
No random data. Every test uses the same seed so failures are reproducible.

### Rule G3 — Assert on status AND side-effect
For every denial, assert (a) the HTTP status, and (b) that no row was written
to the target table. A test that only checks status will miss write-then-forbid bugs.

### Rule G4 — Denial must not leak existence
For cross-tenant access, the response must be identical whether the resource
exists in another tenant or does not exist at all. Assert byte-equality of
the response body. This is the IDOR-leak test.

## C# skeleton (Executor expands this)

```csharp
[Collection("authorization")]
public class AuthorizationDenialTests(TestHost host, Fixtures fx) : IClassFixture<TestHost>
{
    public static IEnumerable<object[]> TenantScopedEndpoints => EndpointCatalog.TenantScoped();

    [Theory, MemberData(nameof(TenantScopedEndpoints))]
    public async Task D1_Unauthenticated_IsDenied(EndpointCase c)
    {
        var res = await host.Anonymous().SendAsync(c.Request(fx.TenantA));
        Assert.Equal(HttpStatusCode.Unauthorized, res.StatusCode);
    }

    [Theory, MemberData(nameof(TenantScopedEndpoints))]
    public async Task D2_CrossTenant_IsDenied_AndDoesNotLeakExistence(EndpointCase c)
    {
        var asA = host.As(fx.PersonA);
        var real = await asA.SendAsync(c.Request(fx.ResourceInTenantB));
        var fake = await asA.SendAsync(c.Request(fx.NonExistentResource));
        Assert.Equal(HttpStatusCode.Forbidden, real.StatusCode);
        Assert.Equal(await real.Content.ReadAsStringAsync(),
                     await fake.Content.ReadAsStringAsync());   // G4
        Assert.Null(await c.Db.FindAsync(fx.ResourceInTenantB)); // G3
    }

    [Theory, MemberData(nameof(CapabilityGatedEndpoints))]
    public async Task D3_WrongHat_IsDenied(EndpointCase c)
    {
        var asA = host.As(fx.PersonA).WithHat(fx.HatWithInsufficientCapacity);
        var res = await asA.SendAsync(c.Request(fx.OwnTenantResource));
        Assert.Equal(HttpStatusCode.Forbidden, res.StatusCode);
    }

    [Theory, MemberData(nameof(CapabilityGatedEndpoints))]
    public async Task D4_ExpiredHat_IsDenied(EndpointCase c)
    {
        var asA = host.As(fx.PersonA).WithHat(fx.ExpiredHat);
        var res = await asA.SendAsync(c.Request(fx.OwnTenantResource));
        Assert.Equal(HttpStatusCode.Forbidden, res.StatusCode);
    }

    [Theory, MemberData(nameof(ConsentScopedEndpoints))]
    public async Task D5_RevokedConsent_IsDenied(EndpointCase c)
    {
        await fx.RevokeConsentAsync(fx.ConsentGrant);
        var res = await host.As(fx.PersonA).SendAsync(c.Request(fx.ConsentScopedResource));
        Assert.Equal(HttpStatusCode.Forbidden, res.StatusCode);
    }

    [Theory, MemberData(nameof(PublicEndpoints))]
    public async Task D6_PublicRoute_LeaksNoPrivateField(EndpointCase c)
    {
        var res = await host.As(fx.PersonA).SendAsync(c.Request(fx.OwnTenantResource));
        var body = await res.Content.ReadAsStringAsync();
        foreach (var forbidden in c.PrivateFieldNames)
            Assert.DoesNotContain(forbidden, body, StringComparison.OrdinalIgnoreCase);
        // Also check hydration payload and JSON-LD block
    }

    [Theory, MemberData(nameof(Workers))]
    public async Task D7_Worker_CannotEscapeTenantScope(WorkerCase w)
    {
        var msg = w.BuildMessage(tenantId: fx.TenantB);
        var res = await host.RunWorkerAsync(w.WorkerType, msg, scope: fx.TenantA);
        Assert.False(res.Processed);
        Assert.True(res.DeadLettered);
    }
}
```

Exit criteria for this artifact

· Every endpoint from 01 §E3 has D1, D2, D6 tests
· Every capability-gated endpoint has D3, D4
· Every consent-scoped endpoint has D5
· Every worker has D7
· Zero P0 findings remain unfixed
· Suite runs in under 3 minutes in CI

Gap this closes

G-A1 (no authorization tests) and G-A17 (no idempotency spec) — the D-series
double-submit cases are added as class D8 in the next revision.

```

---

# ARTIFACT 03 — `03-entity-ledger.md`

```markdown
# Entity Ledger v2.0
Purpose: Single source of truth for every entity, its owner, its lifecycle,
and its disposition from v1.1. Target: 25 aggregates, ~70 total entities.

## 1. Aggregate Roots (25)

An aggregate root is the only entry point for transactional writes to its
cluster. No module reads another module's tables.

| New ID | Aggregate | Module | Owns | Concurrency | Key invariant |
|---|---|---|---|---|---|
| A-01 | Person | Identity | Login, passkeys, MFA, locale | Optimistic (rowversion) | — |
| A-02 | Organization | Org Graph | Legal identity, domains, registry IDs, status, parent/child via A-09 | Optimistic | One canonical record across both sites |
| A-03 | Workspace | Org Graph | Tenant boundary, region, billing owner, policy | Optimistic | Every private record has exactly one owning workspace (I-06) |
| A-04 | Membership | Identity | Person×Org standing, baseline rights | Optimistic | Baseline only; never grants scoped authority |
| A-05 | Hat | Identity | Person×Org×ChannelRecipeInstance×Capacity×Scope×Validity | Optimistic | Hat ≤ Agreement policy (I-04); Persona never grants |
| A-06 | ChannelRecipeInstance | Programs | Recipe version binding + org overrides + EffectiveConfig | Optimistic | Recipe version immutable once bound |
| A-07 | DirectoryListing | Directory | Public face, field provenance, freshness | Optimistic | Public listing ≠ permission to publish relationship (I-01) |
| A-08 | Program | Programs | Pinned BlueprintVersion, effective config, revision | Optimistic + revision | Blueprint immutable (I-02); change never silent (I-03) |
| A-09 | Relationship | Org Graph | Typed, directed, time-bound org link, disclosure setting | Optimistic | Typed: parent/division/franchise/partner/competitor |
| A-10 | CollaborationAgreement | Agreements | Parties, sharing policy, terms, retention | Optimistic | Sharing only through active Agreement (SR-03) |
| A-11 | EvidenceRecord | Evidence | Sourced claim + provenance + expiry + audience | Optimistic | Provenance and expiry mandatory |
| A-12 | PassportShare | Evidence | Evidence×Recipient×Purpose×Expiry | Optimistic | Revocation blocks new use immediately (I-05) |
| A-13 | ConsentGrant | Agreements | Subject×Scope×Audience×Purpose×Expiry×Revocation | Optimistic | Purpose-scoped, revocable (SR-04) |
| A-14 | Introduction | Journeys | Brief, status, SLA, participants | Optimistic | Double opt-in required (J-02) |
| A-15 | JourneyInstance | Journeys | Journey ref, version, state, participants, milestones | Optimistic + state machine | Engine-agnostic; every transition audited |
| A-16 | Opportunity | Revenue | Pursuit, participants, contributions | Optimistic | — |
| A-17 | DealRegistration | Revenue | Claim, policy version, decision, appeal | Optimistic | Precedence by policy, never hard-coded timestamp (I-07) |
| A-18 | AttributionDecision | Revenue | Versioned credit allocation + evidence | Optimistic | Separate from entitlement (I-09) |
| A-19 | FinancialJournal | Revenue | Balanced entries, reversals, reconciliation | Append-only + reversal | Decimal+currency, never float (I-10) |
| A-20 | ContentCard | Content | Type, schema version, data, locale, status, provenance | Optimistic | Card never owns placement |
| A-21 | ContentPage | Content | Layout, card refs, SEO metadata, locale | Optimistic | Page never owns card content |
| A-22 | Publication | Content | Target, state, schedule, version | Optimistic | SEO gates apply here only |
| A-23 | IntegrationConnection | Integrations | Credential ref, mappings, sync state | Optimistic | Credentials are refs, never rows (SR-08) |
| A-24 | Subscription | Billing | Plan, entitlement keys, usage | Optimistic | Entitlements checked in domain layer, not UI |
| A-25 | AgentPrincipal | Identity | Issuer, scopes, expiry, bound Hat, max scope | Optimistic | Delegated scopes only, never full session (SR-10) |

## 2. Associative Objects (first-class, own lifecycle, not aggregates)

| ID | Object | Links | Why first-class | Owner module |
|---|---|---|---|---|
| H-02 | Membership (=A-04) | Person × Org × Workspace | Multi-org membership is normal | Identity |
| H-01 | Hat (=A-05) | Person × Org × RecipeInstance × Capacity × Scope | Authorization subject; multi-hat = multiple rows | Identity |
| H-03 | Relationship (=A-09) | Org × Org (typed) | Org hierarchies without special-casing | Org Graph |
| H-04 | AgreementParty | Agreement × Org × sharing scope | Agreements may have >2 parties | Agreements |
| H-05 | ProgramEnrollment | Org × Program × RecipeInstance × tier × terms | One org, many programs | Programs |
| H-06 | HatDelegation | Hat × Hat (delegate) × expiry | Delegate-of-delegate needs a ceiling (see G-A4) | Identity |
| H-07 | CardPlacement | Card × Page × position | Cards are reused across pages | Content |

## 3. Immutable Entities (identified, never mutated; new version = new row)

| ID | Entity | Module | Why immutable |
|---|---|---|---|
| I-01 | BlueprintVersion | Programs | Programs pin a version (I-02) |
| I-02 | ChannelRecipe | Programs | Versioned composition (Artifact 08) |
| I-03 | JourneyDefinition | Journeys | YAML compiled; versioned by content hash |
| I-04 | PolicyVersion | Authorization | Every decision cites a policy version |
| I-05 | VerticalPack | Programs | Configuration bundle |
| I-06 | Explorer | Content | Query definition + presentation config |
| I-07 | ToolDefinition | Integrations | Named Tool + JSON schema + version (see G-A20) |

## 4. Derived / Projections (rebuildable, never authoritative)

| ID | Projection | Source of truth | Rebuild trigger |
|---|---|---|---|
| P-01 | InboxItem | JourneyInstance + WorkItem | Journey transition |
| P-02 | MatchRecommendation | Listing facts + scoring config | Fact change or config change |
| P-03 | EffectiveConfigView | ChannelRecipeInstance | Reconfiguration |
| P-04 | PublicProjection (per entity type) | Approved fields only | Publication event |
| P-05 | PartnerHealthSignal | Journey milestones | Milestone event |
| P-06 | SearchIndex | PublicProjection | Publication event |

Rule: a projection may be deleted and rebuilt at any time. If deleting it loses
information, it was not a projection.

## 5. Value Objects (no identity, immutable)

| VO | Used by | Definition |
|---|---|---|
| Money | A-19, A-24 | decimal(19,4) + ISO 4217 currency |
| Provenance | A-07, A-11, A-20 | {source, sourceDate, reviewer, trustLabel, verifiedAt} |
| TrustLabel | Provenance | Enum: CompanyAsserted, DocumentReviewed, RegistryChecked, CounterpartyConfirmed, Expired, Disputed |
| EffectiveConfig | A-06, A-08 | {resolvedJson, hash, layers[], lockedFields[]} |
| SlaTimer | A-14, A-15 | {duration, startedAt, pausedAt?, breachedAt?} |
| Scope | A-05, A-12, A-13, A-25 | Enum: WholeOrg, OneProgram, OneRelationship, OneJourney |
| Capacity | A-05 | Enum: OrgAdmin, ProgramSponsor, ProgramPartner, RelationshipParticipant, Buyer, Reviewer, Delegate |
| PurposeCode | A-12, A-13 | Controlled taxonomy — see G-A11 |
| Hash | A-15, A-22, I-03, I-04 | SHA-256 over canonical JSON |

## 6. Reconciliation Table — v1.1 IDs → v2.0 disposition

Every v1.1 entity is accounted for. Nothing silently dropped.

| v1.1 ID | v1.1 Name | v2.0 Disposition | Rationale |
|---|---|---|---|
| A-01 | Person | → A-01 Perso
