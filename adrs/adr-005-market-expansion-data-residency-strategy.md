# ADR-005: Market-Expansion Data Residency Strategy

**Status:** Accepted

## Context

ForkRoute's current platform stores and processes all customer, rider, and restaurant data in a single region with no jurisdiction-aware partitioning, because until now a single-jurisdiction assumption has always held. Three of the four target expansion markets carry data localization requirements that this single-region model does not meet. Retrofitting residency controls after building the new payments event log and dispatch config architecture on a single global data plane would be a materially more expensive and riskier change than designing residency in from the start — moving live financial data between storage partitions after the fact is a much higher-risk operation than partitioning it correctly from day one.

## Decision

Treat **data residency zone as a first-class partitioning dimension** across the platform: each target market (or group of markets sharing compatible legal frameworks) is assigned to a residency zone at design time, with customer, rider, and restaurant PII, along with transactional payment data, stored and processed within that zone by infrastructure-level enforcement (network and IAM boundaries), not application-level filtering alone. Cross-market aggregate reporting is built on de-identified or aggregated exports pushed to a central analytics zone, rather than live cross-region queries.

## Alternatives Considered

**1. Single global data plane with application-level residency filtering.** Rejected because application-level filtering is a defense that can be bypassed by a single application bug — a query that forgets to apply the jurisdiction filter would leak cross-zone data with no infrastructure-level backstop. This was the lowest-cost option and was seriously considered for that reason, but the ARB and Head of Security & Compliance judged the risk of a residency violation (with real regulatory consequences in the affected markets) unacceptable to leave to application-layer discipline alone.

**2. Live, federated cross-region query layer for both operational and analytics use cases.** Rejected primarily on residency-compliance grounds — a live federation layer makes it easier, not harder, to accidentally construct a query that crosses a jurisdiction boundary, which is the opposite of what the residency requirement calls for — and secondarily on latency and operational-complexity grounds, since a cross-region live query pattern would also work against the reference architecture's general preference (see [reference-architecture.md](../04-phase-d-technology-architecture/reference-architecture.md)) for asynchronous, decoupled patterns where strong real-time cross-boundary consistency is not actually required.

**3. Jurisdiction-partitioned zones with infrastructure-level enforcement and batch/export-based cross-zone analytics (the selected option).** Provides a defense-in-depth residency guarantee (an application bug cannot leak data across a zone it has no network or IAM path to reach) at the cost of making cross-market analytics same-day rather than real-time.

## Consequences

**Positive:**
- Expansion markets are compliant with data localization requirements at launch, by design, rather than through a post-launch retrofit — directly addressing the success metric in [vision-and-scope.md](../01-phase-a-vision-and-scope/vision-and-scope.md).
- Infrastructure-level enforcement provides a defense-in-depth guarantee that does not depend on every engineer remembering to apply a jurisdiction filter correctly in every query.
- Establishes a repeatable pattern for onboarding a fifth or sixth market in the future (outside this program's scope, but a direct beneficiary of this decision) — assigning a new market to an existing or new residency zone is a provisioning exercise, not a data-model redesign.

**Negative (accepted trade-offs):**
- Cross-market aggregate reporting (e.g., a global GMV dashboard) becomes dependent on a daily export-and-aggregate pipeline rather than a live query, adding latency (same-day, not real-time) to global reporting — a real trade-off accepted deliberately in exchange for the compliance guarantee, and communicated explicitly to Finance and executive stakeholders who consume that reporting, rather than discovered as a surprise after launch.
- Every foundational building block (Market Config Store, Payments Event Log — see [solution-building-blocks.md](../05-phase-e-opportunities-and-solutions/solution-building-blocks.md)) has a hard dependency on the residency zone architecture being in place first, which is why it is sequenced earliest in the [migration roadmap](../06-phase-f-migration-planning/migration-roadmap.md) — this creates schedule risk concentrated at the very start of the program, where a delay has knock-on effects on everything downstream.
- Operating multiple isolated infrastructure zones is a genuinely higher operational cost than a single global deployment, reflected in the cloud infrastructure cost delta in the [business case](../01-phase-a-vision-and-scope/business-case.md).

## Governance

Approved by the full ARB; Head of Data is Responsible for the technical implementation and Regional Expansion Leads are Responsible for confirming local regulatory interpretation per market, per the contested-accountability resolution recorded in [stakeholder-map.md](../01-phase-a-vision-and-scope/stakeholder-map.md); Chief Architect is Accountable overall. This decision directly implements Principle 4.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
