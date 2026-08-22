# Architecture Principles

These principles govern all architecture decisions made under the ForkRoute market-expansion program. Each is written in the standard TOGAF principle format — Name, Statement, Rationale, Implications — and each is enforceable by the Architecture Review Board (see [governance-framework-setup.md](governance-framework-setup.md)). Principles are numbered for reference from ADRs and phase documents; the numbering is not a priority ranking.

---

### Principle 1 — Configuration Over Code for Market Variation

**Statement:** Market-specific business rules (dispatch radius, surge thresholds, payout timing, tax treatment, currency) are expressed as versioned configuration evaluated at runtime, not as code branches or per-market service forks.

**Rationale:** The single largest cost driver in the current platform's expansion economics is that every new city has required a code change to encode local rules. A configuration-driven model decouples "add a market" from "ship a deployment."

**Implications:** Requires a rules engine or policy layer with its own versioning, testing, and rollback discipline. Introduces a new failure mode (bad configuration) that must be governed as rigorously as bad code — configuration changes go through the same review gate as a schema migration, not a casual admin-panel edit.

---

### Principle 2 — Tenant Isolation Is Non-Negotiable at the Integration Boundary

**Statement:** Any system that integrates with an external party (restaurant partner, payment processor, local logistics provider) must contain the blast radius of that party's failure or traffic anomaly to that party alone.

**Rationale:** The current restaurant integration platform's shared-fate design has caused platform-wide degradation from single-partner incidents. This is unacceptable at 4x the partner count the expansion implies.

**Implications:** Per-tenant rate limiting, circuit breakers, and (where the vendor evaluation in Phase E supports it) per-tenant queue or topic partitioning are mandatory, not optional hardening. This adds infrastructure cost and operational surface area that must be budgeted, not treated as free.

---

### Principle 3 — Payments State Is Event-Sourced, Not Batch-Reconciled

**Statement:** The authoritative record of a payment's state (authorized, captured, split, paid out, disputed) is an append-only event log; nightly batch reconciliation is a downstream audit function, not the system of record.

**Rationale:** Real-time payout expectations in the new markets and dynamic surge pricing both require a live, queryable view of money movement that nightly batch cannot provide.

**Implications:** Requires the organization to adopt event-sourcing discipline (idempotency keys, replay tooling, event schema versioning) in a domain — payments — where correctness errors have direct financial and regulatory consequences. This is a materially higher engineering bar than the batch model it replaces.

---

### Principle 4 — Data Residency Follows the Market, Not the Platform

**Statement:** Personally identifiable customer, rider, and restaurant data is stored and processed within the legal jurisdiction the data subject operates in, by design, not as an exception applied after the fact.

**Rationale:** Three of the four target expansion markets have data localization requirements that the current single-region platform does not meet. Retrofitting residency after building a single global data plane is materially more expensive than designing for it from the outset.

**Implications:** Requires a data architecture with jurisdiction as a first-class partitioning dimension (see [data-architecture.md](../03-phase-c-information-systems-architecture/data-architecture.md)). Cross-region aggregate reporting (e.g., global GMV dashboards) must be built on de-identified or aggregated exports, not live cross-region queries.

---

### Principle 5 — Every New Market Is a Configuration, Not a Fork

**Statement:** Onboarding a new country or city is achieved by authoring a new configuration bundle (dispatch rules, tax rules, payout rules, supported languages/currencies) against the existing platform, never by branching or duplicating a service.

**Rationale:** This is the direct business objective of the program: cut expansion lead time without paying an ever-growing maintenance tax on divergent forks.

**Implications:** Any proposed solution building block that cannot be parameterized for a new market without a code change fails architecture review by default. Engineering teams must budget extra design time up front to generalize correctly — this principle trades faster market N+1 rollout for slower market 1 delivery.

---

### Principle 6 — Interfaces Are Contracts, Versioned and Backward-Compatible

**Statement:** All service-to-service and partner-facing interfaces are explicit, versioned contracts (schema-governed events or APIs). Breaking changes require a new version published alongside the old, with a deprecation window, never an in-place breaking change.

**Rationale:** With multiple markets and a growing partner integration surface, uncoordinated breaking changes become a leading cause of cross-team incidents as the organization scales past what informal coordination can manage.

**Implications:** Requires schema registry tooling and contract testing in CI. Slows down some releases in exchange for removing an entire class of cross-team incident.

---

### Principle 7 — Build Only What Differentiates ForkRoute

**Statement:** Undifferentiated capability (generic route optimization, payment gateway connectivity, KYC/AML checks) is bought or integrated from a qualified vendor; engineering effort is reserved for the dispatch logic, marketplace dynamics, and partner experience that are specific to ForkRoute's competitive position.

**Rationale:** Engineering capacity is the scarcest resource in the program. Building commodity capability in-house has historically consumed capacity that should have gone to the differentiating dispatch and reconciliation work.

**Implications:** Requires a disciplined build-vs-buy evaluation (see [vendor-evaluation.md](../05-phase-e-opportunities-and-solutions/vendor-evaluation.md)) for every major building block, and accepts vendor dependency risk in exchange for engineering focus.

---

### Principle 8 — Architecture Decisions Are Recorded, Not Tribal

**Statement:** Any decision that is expensive to reverse, affects more than one team, or trades off a named alternative is captured as a written Architecture Decision Record before implementation begins.

**Rationale:** The current platform's per-city drift happened partly because no record existed of *why* each city's rules diverged — reversing bad decisions required archaeology instead of reading a document.

**Implications:** Adds a small amount of process overhead ahead of implementation. In exchange, it gives future teams (and, not incidentally, future architects auditing the platform) a defensible trail of reasoning instead of folklore.

---

### Principle 9 — Design for Multi-Currency and Multi-Language From Day One

**Statement:** All customer-, rider-, and restaurant-facing systems and all financial calculations treat currency and locale as required parameters, never as an assumed constant.

**Rationale:** The home market's platform was built currency- and locale-single, and retrofitting multi-currency into the payments core was one of the more expensive line items uncovered in gap analysis. The four target markets span three currencies and two additional languages.

**Implications:** All monetary fields carry an explicit currency code; no financial code path assumes a single decimal precision or symbol placement. Increases initial implementation cost for markets 1 in exchange for near-zero currency-related rework for markets 2–5.

---

### Principle 10 — Resilience Is Scoped to Business Impact, Not Uniformly Maximized

**Statement:** Availability and disaster-recovery targets are set per capability based on the cost of an outage to the business, not applied uniformly at the highest tier across the platform.

**Rationale:** Not every capability carries the same cost of failure — a delayed promotional-banner update and a failed payment capture are not equivalent risks, and treating them as equivalent means either over-spending on low-stakes systems or under-spending on high-stakes ones.

**Implications:** Requires an explicit criticality tiering exercise (documented in [reference-architecture.md](../04-phase-d-technology-architecture/reference-architecture.md)) and disciplined resistance to "just make it all five-nines," which is a common but costly default.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
