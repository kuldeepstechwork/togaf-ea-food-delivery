# Architecture Vision and Scope

## Problem Statement

ForkRoute operates a food delivery marketplace serving roughly 2 million monthly active users, 15,000 restaurant partners, and 40,000 active riders in a single home market. Leadership has committed to launching in four additional countries within 18 months. Three structural properties of the current platform stand directly in the way:

1. Order-dispatch and driver-allocation rules are hardcoded per city, duplicated and drifting, with no authoritative source of truth and an estimated 8–10 week lead time to encode a new market's rules.
2. The restaurant partner integration platform is a shared-fate monolith with no per-tenant isolation, so a single partner's incident can degrade order intake platform-wide.
3. Payments reconciliation across customers, restaurants, and riders runs as nightly batch, which cannot support real-time payouts or dynamic surge pricing — both explicit requirements of the expansion markets.

Continuing to expand on the current architecture would mean repeating the current 8–10 week per-market dispatch build four more times, inheriting the integration platform's single-point-of-failure risk at roughly 2.5x the current partner count, and either delaying real-time payout capability in every new market or building it as another one-off.

## Target State Vision

ForkRoute's target architecture treats **market expansion as a configuration exercise, not an engineering project.** Concretely:

- A single, config-driven dispatch and allocation engine loads market-specific rule sets at runtime; a new market ships when its configuration bundle is authored and validated, not when new code is deployed.
- An event-driven payments backbone maintains payment and payout state as an append-only, replayable log, enabling real-time payout and live surge-pricing calculation in every market from day one.
- A multi-tenant restaurant integration platform isolates each partner's traffic and failure blast radius, so no single partner incident can degrade the platform for others.
- Data residency is a first-class architectural concern, with jurisdiction-aware storage and processing built in rather than retrofitted market by market.

The target state is described in detail in [Phase B — Business Architecture](../02-phase-b-business-architecture/to-be-business-architecture.md), [Phase C — Information Systems Architecture](../03-phase-c-information-systems-architecture/), and [Phase D — Technology Architecture](../04-phase-d-technology-architecture/reference-architecture.md).

## Scope

### In Scope

- Re-architecture of the order-dispatch and driver-allocation engine to a config-driven model (Principle 1, Principle 5).
- Re-architecture of payments and reconciliation to an event-sourced model supporting real-time payout and surge pricing (Principle 3).
- Re-architecture of the restaurant partner integration platform for multi-tenant isolation (Principle 2).
- Data architecture changes required to support jurisdiction-aware residency in the four target expansion markets (Principle 4).
- The technology, vendor, and migration decisions required to deliver the above across the current home market and the four expansion markets.
- Organizational change management for the engineering, operations, and partner-facing teams affected by the above.

### Explicitly Out of Scope

- **The rider/courier mobile application UX redesign.** Under active discussion by the Product organization but not a dependency of the dispatch, payments, or integration re-architecture; bundling it would extend timeline without changing the underlying architecture risk this program addresses.
- **Customer-facing loyalty and rewards program.** A business initiative with its own roadmap; it consumes the payments platform's APIs once rebuilt, but designing it is not part of this engagement.
- **In-house last-mile route optimization algorithm research.** The vendor evaluation (Phase E) treats route optimization as a build-vs-buy decision explicitly to avoid the program absorbing a multi-year algorithms research effort; see [vendor-evaluation.md](../05-phase-e-opportunities-and-solutions/vendor-evaluation.md) for the reasoning.
- **Full replatforming of the home-market payments core's legacy general ledger integration.** The GL integration is being wrapped, not replaced, in this program (see [ADR-002](../adrs/adr-002-event-driven-payments-reconciliation.md)); a full GL replacement is a separate, larger finance-systems initiative outside this program's charter and budget.
- **Restaurant partner-facing point-of-sale (POS) hardware.** ForkRoute integrates with partners' existing POS systems via API; it does not manufacture, resell, or architect POS hardware.
- **New markets beyond the four named in the expansion mandate.** The architecture is designed to generalize to further markets, but this engagement's scope, budget, and roadmap are bounded to the four markets named in the FY26 expansion mandate; a fifth market is a new business case, not an extension of this one.

Scope exclusions were agreed with the Executive Steering Committee at program kickoff specifically to keep the 18-month timeline credible; each excluded item was a candidate for inclusion that was assessed and rejected on the basis that it did not share the underlying architectural root cause (dispatch drift, integration coupling, batch reconciliation) driving this program.

## Success Metrics

| Metric | Baseline (as-is) | Target (to-be) |
|---|---|---|
| Engineering lead time to launch a new market's dispatch rules | 8–10 weeks | ≤ 3 weeks |
| Restaurant partner incidents causing platform-wide order-intake degradation | 6–8 per year (illustrative) | 0 tolerated as platform-wide; contained to single-tenant blast radius |
| Payout latency to riders/restaurants | Next business day (batch) | Under 30 minutes in supported markets |
| Time to calculate and apply a surge-pricing adjustment | Not supported dynamically today (manual city-level toggle) | Sub-5-second automated recalculation |
| New-market data residency compliance at launch | Retrofitted post-launch historically | Compliant at launch, by design |
| Cost per new market to reach dispatch-ready state | ~$420K illustrative (current model, from [business-case.md](business-case.md)) | ~$110K illustrative (configuration-driven model) |

These metrics are the basis on which the Executive Steering Committee will assess whether the program has delivered against its charter at the Phase F/G transition checkpoints.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
