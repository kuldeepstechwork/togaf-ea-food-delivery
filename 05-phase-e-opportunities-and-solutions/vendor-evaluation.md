# Vendor Evaluation: Route Optimization & Payments/Reconciliation Platforms

## Scope of This Evaluation

Per [solution-building-blocks.md](solution-building-blocks.md), two capabilities were designated buy/hybrid rather than build: the **Route Optimization / ETA Calculation Engine** and the underlying **Payments/Reconciliation Platform** beneath the in-house Payout Orchestrator. This document evaluates candidate vendors for both, using generic/fictional product names — no real, trademarked vendor or product is referenced or implied.

## Evaluation Criteria and Weighting

| Criterion | Weight | What It Measures |
|---|---|---|
| Multi-market / multi-currency support | 20% | Native support for the currencies and locales of all four target expansion markets |
| Integration effort with event-driven backbone | 15% | How cleanly the vendor's API/webhook model fits the reference architecture in [Phase D](../04-phase-d-technology-architecture/reference-architecture.md) |
| Real-time / low-latency capability | 15% | Whether the vendor can meet sub-30-minute payout and sub-2-second dispatch-adjacent latency targets |
| Data residency & compliance posture | 15% | Vendor's ability to process/store data within required jurisdictions and provide compliance attestations |
| Total cost of ownership (3-year) | 15% | License, integration, and ongoing operational cost |
| Vendor stability / support quality | 10% | Company maturity, SLA quality, reference customers at comparable scale |
| Extensibility / API openness | 10% | Ability to customize without vendor lock-in on core business logic |

## Route Optimization / ETA Engine — Candidates

| Vendor | Multi-Market (20%) | Integration Effort (15%) | Real-Time (15%) | Residency (15%) | 3yr TCO (15%) | Stability (10%) | Extensibility (10%) | Weighted Score |
|---|---|---|---|---|---|---|---|---|
| **RouteVantage** (fictional) | 4 | 4 | 5 | 3 | 3 | 4 | 4 | **3.85** |
| **PathForge Logistics** (fictional) | 5 | 3 | 4 | 5 | 3 | 3 | 3 | **3.80** |
| **NaviCore Fleet** (fictional) | 3 | 5 | 4 | 3 | 4 | 4 | 3 | **3.65** |

Scoring: 1 (poor) – 5 (excellent) against each criterion; weighted score is the sum of (score × weight).

**Recommendation: RouteVantage.** It narrowly outscores PathForge Logistics primarily on integration effort and vendor stability, and its real-time capability (native support for sub-second ETA recalculation under live traffic and driver-supply changes) is the strongest of the three, which matters directly for the surge-pricing feedback loop described in the [reference architecture](../04-phase-d-technology-architecture/reference-architecture.md). **Why PathForge Logistics lost despite the strongest residency score:** its integration model assumes a synchronous polling pattern rather than webhook/event delivery, which would have required a translation layer sitting awkwardly against Principle 6's contract-versioning standard — a meaningful, ongoing integration tax rather than a one-time cost. **Why NaviCore Fleet lost:** best integration fit, but weakest multi-market currency and locale support, which is a harder gap to work around than an integration adapter, given three of the four target markets have distinct currency and formatting requirements out of the gate.

## Payments / Reconciliation Platform — Candidates

| Vendor | Multi-Market (20%) | Integration Effort (15%) | Real-Time (15%) | Residency (15%) | 3yr TCO (15%) | Stability (10%) | Extensibility (10%) | Weighted Score |
|---|---|---|---|---|---|---|---|---|
| **LedgerBridge** (fictional) | 5 | 4 | 5 | 4 | 3 | 4 | 3 | **4.10** |
| **SettleWorks** (fictional) | 4 | 5 | 4 | 3 | 4 | 3 | 4 | **3.90** |
| **ClearPay Rails** (fictional) | 3 | 3 | 5 | 5 | 2 | 5 | 2 | **3.50** |
| **Meridian Settlement** (fictional) | 4 | 3 | 3 | 4 | 5 | 4 | 3 | **3.65** |

**Recommendation: LedgerBridge.** It scores highest overall and is the clear leader on the two criteria weighted highest for this program's specific problem — multi-market support and real-time capability — both directly load-bearing for the sub-30-minute payout target and the surge-pricing-to-payout split requirement. **Why SettleWorks lost:** genuinely the easiest integration of the four (native event-webhook model closely matching our reference architecture), but its weaker residency posture (data processing centralized in a single region regardless of transaction origin) conflicts directly with Principle 4 and would have required a costly custom data-routing layer to compensate. **Why ClearPay Rails lost despite the strongest residency and real-time scores:** its 3-year TCO is meaningfully higher (illustrative estimate ~40% above LedgerBridge over 3 years) and its extensibility score is the weakest of the four — its API does not expose enough control over commission-split logic to implement ForkRoute's specific surge-split rules without a support-ticket-driven customization cycle, which is an unacceptable dependency for a Tier 1 capability. **Why Meridian Settlement lost:** best 3-year TCO by a wide margin, but its real-time capability is the weakest of the four (batch-oriented core with a "near-real-time" add-on that still measures latency in tens of minutes, not seconds) — it is, in effect, a lower-cost version of the batch model this program exists to replace, and selecting it would have undermined the program's own stated objective for cost reasons alone.

## Decision Governance

Both recommendations were reviewed and approved by the full ARB given their budget impact exceeds the $150K threshold defined in the [governance framework](../00-preliminary/governance-framework-setup.md); Finance was a consulted stakeholder on the TCO scoring specifically. Contract terms (including exit/portability provisions, given Principle 7's acceptance of vendor dependency risk) are negotiated by Procurement with architecture input on the technical requirements, not by the ARB directly.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
