# Business Case

All figures in this document are illustrative, invented for this fictional case study, and are stated in a single notional currency unit ("$") for simplicity. They are constructed to be internally consistent and plausible for a marketplace of ForkRoute's stated size, not to represent any real company's financials.

## Cost Basis and Assumptions

- Blended fully-loaded engineering cost: **$18,000 per person-month** (covers senior/staff engineers, EMs, and architects at a blended rate typical for a mid-size tech organization).
- Program duration: **18 months**, aligned to the expansion mandate.
- Home market platform is fully amortized; capex below is incremental to the re-architecture program only.
- Cloud infrastructure costs are modeled as an incremental delta over current run-rate, not total infrastructure spend.

## Capex / One-Time Investment

| Line Item | Effort (person-months) | Cost |
|---|---|---|
| Config-driven dispatch engine (design, build, migrate home market) | 42 PM | $756,000 |
| Event-driven payments/reconciliation backbone (design, build, migrate home market) | 55 PM | $990,000 |
| Multi-tenant restaurant integration platform re-architecture | 38 PM | $684,000 |
| Data residency architecture & jurisdiction-aware storage layer | 24 PM | $432,000 |
| Vendor evaluation, procurement, and integration (routing/reconciliation platforms) | 10 PM | $180,000 + $250,000 licensing/integration fee (illustrative) |
| Migration tooling & dual-run infrastructure (Phase F transition support) | 16 PM | $288,000 |
| Change management, training, and documentation (Phase H) | 8 PM | $144,000 |
| Program management & architecture governance overhead (18 months) | 20 PM | $360,000 |
| **Subtotal — Capex** | **213 PM** | **$4,084,000** |
| Contingency (15%, standard for programs of this complexity and duration) | — | $612,600 |
| **Total Capex** | | **$4,696,600** |

## New-Market Integration Cost (Per Market, To-Be Model)

| Line Item | Cost (illustrative) |
|---|---|
| Configuration authoring & validation (dispatch, tax, payout rules) | $45,000 |
| Local payment rail / payout provider integration | $35,000 |
| Data residency setup (regional storage provisioning, compliance review) | $18,000 |
| Local regulatory/compliance review & sign-off | $12,000 |
| **Total per new market (to-be)** | **~$110,000** |

Compare to the as-is cost per market, reconstructed from the home market's historical per-city rollout pattern:

| Line Item | Cost (illustrative, as-is) |
|---|---|
| Dispatch logic fork & city-specific rule coding | $210,000 |
| Payments batch job adaptation for local currency/rails | $95,000 |
| Restaurant integration onboarding at shared-platform risk premium | $65,000 |
| Regulatory/compliance retrofit (residency not designed in) | $50,000 |
| **Total per new market (as-is)** | **~$420,000** |

Across four target markets, the to-be model saves an estimated **$1,240,000** in direct new-market rollout cost alone (4 × $420,000 = $1,680,000 as-is vs. 4 × $110,000 = $440,000 to-be), before accounting for the lead-time and incident-cost savings below.

## Opex Delta (Annual, Post-Migration, Steady State)

| Line Item | As-Is (Annual) | To-Be (Annual) | Delta |
|---|---|---|---|
| Cloud infrastructure (compute, event streaming, storage) | $1,800,000 | $2,650,000 | +$850,000 |
| Vendor licensing (routing/reconciliation platform) | $0 | $620,000 | +$620,000 |
| Incident-response cost attributable to shared-fate integration platform | $480,000 (6–8 incidents/yr, ~$65K avg cost) | $60,000 (isolated, contained incidents) | −$420,000 |
| Engineering maintenance of per-city forked dispatch logic | $1,050,000 (est. 58 PM/yr across markets) | $270,000 (est. 15 PM/yr, config maintenance only) | −$780,000 |
| Manual payments reconciliation labor | $340,000 | $95,000 (event log + automated audit) | −$245,000 |
| **Net Annual Opex Delta** | | | **+$25,000** |

The to-be model is very nearly opex-neutral at steady state — higher infrastructure and licensing cost is almost fully offset by lower incident cost and lower per-market maintenance burden. The financial case rests primarily on capex-to-savings payback and avoided per-market rollout cost, not on an opex reduction story.

## 3-Year TCO Comparison

| | As-Is (continue current model, expand into 4 markets) | To-Be (re-architected platform) |
|---|---|---|
| Year 1 capex/program cost | $0 (no re-architecture) | $4,696,600 |
| Year 1–3 per-market rollout cost (4 markets) | $1,680,000 | $440,000 |
| 3-year cumulative opex (steady-state delta × 3, opex begins Year 2 for to-be post-migration) | $3,670,000 × 3 = $11,010,000 (as-is baseline opex, illustrative) | $11,010,000 + ($25,000 × 2) = $11,060,000 |
| **3-Year Total** | **$12,690,000** | **$16,196,600** |

Read at face value, the 3-year TCO appears higher for the to-be architecture — this is expected and is disclosed transparently rather than obscured, because the business case is not primarily a cost-reduction case. It is a **capability and risk-avoidance case**: the as-is column assumes the four-market expansion is achievable at all on the current architecture, which the [gap analysis](../05-phase-e-opportunities-and-solutions/gap-analysis.md) and [vision and scope](vision-and-scope.md) both argue it is not, at the committed 18-month timeline, without unacceptable reliability risk. The relevant comparison is therefore not as-is-TCO vs. to-be-TCO in isolation, but to-be-TCO vs. the cost of a delayed or failed expansion.

## Payback Period Calculation

Using only the directly quantifiable savings streams (avoided per-market rollout cost + annual incident/maintenance/reconciliation savings), shown with the arithmetic:

- One-time savings from 4-market rollout: **$1,240,000** (calculated above)
- Annual recurring savings (incident + maintenance + reconciliation, before opex increase): $420,000 + $780,000 + $245,000 = **$1,445,000/year**
- Annual opex increase (infra + licensing): $850,000 + $620,000 = **$1,470,000/year**
- Net annual recurring savings: $1,445,000 − $1,470,000 = **−$25,000/year** (i.e., slightly negative, consistent with the near-neutral opex delta above)
- Total capex to recover: **$4,696,600**
- Recovery via one-time savings alone: $4,696,600 − $1,240,000 = **$3,456,600 remaining**
- At a near-zero net annual recurring savings rate, the pure cost-payback period on remaining capex is **not achieved within the 3-year window on cost savings alone.**

**This is the honest number, and it is the one presented to the Executive Steering Committee.** The investment does not pay for itself on direct cost savings within 3 years. It is justified instead by:

1. **Revenue enablement:** the four target markets represent a projected combined incremental GMV opportunity that the current architecture cannot credibly support at the committed timeline — the cost of a 6-month expansion delay alone (illustrative estimate: $2.1M in deferred contribution margin per month of delay, based on projected market ramp curves) exceeds the entire program capex within roughly 2.2 months of delay avoided.
2. **Risk avoidance:** the current integration platform's incident pattern, extrapolated to 2.5x partner count without isolation, carries a materially higher probability of a platform-wide outage in a new, less operationally mature market — a risk the Executive Steering Committee judged unacceptable to carry into markets where ForkRoute has no existing brand trust to cushion a bad launch.

The business case presented to the Executive Steering Committee is therefore framed on payback against the cost of delay and the risk-adjusted cost of a degraded expansion, not on a 3-year opex reduction, and is presented that way explicitly rather than through an inflated ROI figure.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
