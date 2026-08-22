# Gap Analysis

## Purpose

This document consolidates the gaps between as-is and to-be architecture identified across Phases B, C, and D into a single prioritized list, feeding directly into the [migration roadmap](../06-phase-f-migration-planning/migration-roadmap.md) sequencing.

## Prioritization Method

Each gap is scored on **Business Impact** (1–5, how directly it blocks the expansion mandate or creates ongoing operational risk) and **Closure Complexity** (1–5, engineering and organizational effort to close), and prioritized using Impact ÷ Complexity as a rough sequencing signal — high-impact, lower-complexity gaps are closed first where dependencies allow, but a hard dependency (e.g., data residency must precede any per-market payments work) overrides the raw score when the two conflict.

## Prioritized Gap List

| # | Gap | Business Impact (1-5) | Closure Complexity (1-5) | Priority Signal | Dependency Notes |
|---|---|---|---|---|---|
| 1 | Dispatch logic hardcoded per city, no config layer | 5 | 4 | High | Foundational; blocks any market rollout at the target lead time |
| 2 | No dynamic/automated surge pricing | 4 | 3 | High | Depends on Gap 1 (config layer) and Gap 3 (event backbone) |
| 3 | Payments reconciliation is nightly batch, not event-driven | 5 | 4 | High | Foundational for real-time payout and surge-split correctness |
| 4 | Restaurant integration platform has no tenant isolation | 4 | 3 | High | Independent of Gaps 1–3; can proceed in parallel |
| 5 | No jurisdiction-aware data residency architecture | 5 | 3 | High | Hard dependency: must precede any expansion-market payments or customer-data work |
| 6 | Payments core assumes single currency | 4 | 3 | Medium-High | Depends on Gap 3 (event log must carry currency-aware fields from the start) |
| 7 | No standing architecture governance body prior to program | 3 | 1 | High (cheap to close) | Already addressed in Preliminary Phase; included here for completeness of the gap record |
| 8 | Restaurant partner onboarding partially manual | 3 | 2 | Medium | Independent; can be improved incrementally, not a hard blocker to first market launch |
| 9 | No formal vendor evaluation/build-vs-buy discipline historically | 2 | 1 | Medium (cheap to close) | Addressed via Phase E process itself |
| 10 | No structured change management practice for platform-level shifts | 3 | 2 | Medium | Needed ahead of first cutover, not necessarily ahead of engineering start |
| 11 | Observability/tracing insufficient to debug cross-service order journeys in to-be decomposition | 3 | 2 | Medium | Emerges as a gap only once services are decomposed; sequenced alongside Gap 1/3 delivery, not before |
| 12 | Rider/courier mobile app does not yet expose real-time payout status to riders | 2 | 2 | Low | Explicitly out of scope per [vision-and-scope.md](../01-phase-a-vision-and-scope/vision-and-scope.md) UX exclusion; noted here so it is not silently forgotten, but not sequenced in this program |

## How This Maps to Sequencing

Gaps 1, 3, and 5 are the three foundational, high-impact gaps, and — importantly — Gap 5 (data residency) has a hard dependency relationship with both Gap 3 (payments) and any expansion-market work generally: building the payments event log before the residency zone architecture exists would mean re-partitioning a live financial data store later, which is a materially more expensive and riskier operation than designing the partition in from the start. This is why the [migration roadmap](../06-phase-f-migration-planning/migration-roadmap.md) sequences the data residency layer ahead of, not alongside, the payments backbone build, even though its raw priority-signal score alone would suggest they could run in parallel.

Gap 4 (restaurant integration isolation) is deliberately sequenced as a parallel workstream rather than serialized after Gaps 1/3/5, because the ARB determined it has no meaningful technical dependency on the dispatch or payments work — the delivery teams and codebases involved are largely disjoint, and serializing it would extend the timeline without a corresponding risk-reduction benefit.

## Gaps Deliberately Not Prioritized for This Program

Gap 12 (rider payout-status visibility in the mobile app) scores lowest and is explicitly excluded from this program's roadmap, consistent with the UX-redesign exclusion in [vision-and-scope.md](../01-phase-a-vision-and-scope/vision-and-scope.md) — the underlying payout data will exist and be queryable once the Payments Event Log is built, so exposing it in the app becomes a comparatively cheap follow-on project for the Product organization once this program's platform work is complete, not something that needs to be pulled into this program's scope to be technically feasible later.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
