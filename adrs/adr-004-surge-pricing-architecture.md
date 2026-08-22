# ADR-004: Surge Pricing Architecture

**Status:** Accepted

## Context

Today, surge pricing (where it exists at all) is a manual operational toggle a city operations manager flips during a perceived demand spike, applying a flat citywide multiplier with no live view of actual driver supply and no automated feed into rider or restaurant payout splits. This does not meet the expansion markets' competitive requirement for dynamic, calculated surge pricing, and it does not integrate cleanly with the event-driven payments backbone ([ADR-002](adr-002-event-driven-payments-reconciliation.md)), which needs to know the exact pricing decision behind every order to calculate correct payout splits.

## Decision

Implement surge pricing as an **automated Dynamic Surge Pricing Calculator**, a component of the dispatch engine domain that continuously evaluates live driver-supply and order-demand signals within each market's configured bounds (minimum/maximum multiplier, evaluation frequency, and triggering thresholds — all expressed in the market's config bundle per [ADR-001](adr-001-config-driven-dispatch-engine.md)) and emits a `SurgeApplied` event as a first-class, auditable part of the order's event trail, feeding directly into the Payments Event Log for correct payout-split calculation.

## Alternatives Considered

**1. Retain the manual toggle, but make it more granular (neighborhood-level rather than citywide).** Rejected because it does not solve the core problem — it is still a human-judgment-driven decision with no live data feed, and it does not produce an auditable, structured signal the payments backbone can consume for split calculation. It was considered as a lower-risk incremental step, and rejected because it would still require rebuilding this capability properly for the expansion markets, effectively deferring the real decision rather than making it.

**2. Fully external, vendor-provided dynamic pricing engine.** Rejected under the same reasoning applied in [solution-building-blocks.md](../05-phase-e-opportunities-and-solutions/solution-building-blocks.md): surge pricing logic is a direct expression of ForkRoute's specific marketplace economics and competitive positioning — the thresholds, caps, and fairness constraints (e.g., a maximum multiplier the business is willing to charge riders, or the point at which surge should instead trigger a driver-supply incentive rather than a price increase) are strategic business logic, not a commodity capability. Building this in-house was judged the correct application of Principle 7, not an exception to it.

**3. In-house, config-driven surge calculator integrated with the dispatch engine and payments event backbone (the selected option).** Keeps the strategically differentiating pricing logic in-house and directly wired into both the dispatch engine (which needs live supply/demand signals) and the payments backbone (which needs the pricing decision as an auditable event).

## Consequences

**Positive:**
- Enables genuinely dynamic, data-driven surge pricing rather than a coarse manual toggle, which the business case treats as a competitive requirement in the expansion markets.
- Every surge decision is auditable and traceable to the exact market config version and supply/demand signal that produced it — directly satisfying the same audit traceability requirement that shaped the dispatch engine's design.
- Surge splits flow correctly and automatically into rider and restaurant payouts via the event backbone, eliminating a category of manual reconciliation error that would otherwise arise from trying to bolt dynamic pricing onto the old batch reconciliation model.

**Negative (accepted trade-offs):**
- City Operations Managers lose direct manual control over surge, which is a genuine authority change (see [change-management-plan.md](../08-phase-h-change-management/change-management-plan.md)) and carries real adoption risk if not managed deliberately — this is not merely a technical trade-off, and the program treats it as a change-management risk requiring its own mitigation plan, not a side effect to be absorbed silently.
- An automated pricing engine making real-time pricing decisions raises new governance questions (fairness bounds, regulatory scrutiny of algorithmic pricing in some markets) that a manual toggle, for all its crudeness, did not surface as sharply. The config bundle's explicit minimum/maximum multiplier bounds are the primary control the ARB relies on to keep this governable, but this is an area the ARB has flagged for continued attention as expansion markets' regulatory posture on algorithmic pricing becomes clearer.
- Building this in-house (rather than buying) means ForkRoute bears full engineering cost and ongoing maintenance for the pricing algorithm itself, accepted as a deliberate differentiation investment per Principle 7.

## Governance

Approved by the ARB with VP Engineering, Platform as Responsible for the dispatch-side integration and VP Engineering, Payments as Responsible for the payout-split integration; Chief Architect Accountable. This decision extends Principle 1 and Principle 3 into a specific, jointly-owned capability.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
