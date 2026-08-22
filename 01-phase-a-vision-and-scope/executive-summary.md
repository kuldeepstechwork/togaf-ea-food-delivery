# Executive Summary

## The Problem

ForkRoute's commitment to launch in four new countries within 18 months is at risk from three structural weaknesses in the current platform: order-dispatch rules are hand-coded per city and take 8–10 weeks to stand up in each new market; the restaurant integration platform has no isolation between partners, so one partner's problem can take down order intake for everyone; and payments settle overnight in batch, which cannot support the real-time payouts and dynamic pricing the new markets require. Expanding on the current platform means repeating a slow, fragile process four more times, at growing risk.

## The Recommendation

Re-architect three core systems — dispatch, payments/reconciliation, and restaurant integration — around a **configuration-driven, event-based platform** that treats "add a market" as an authoring exercise, not an engineering project. This is a re-architecture, not a rebuild: the home market continues operating throughout, migrating in stages (see [migration-roadmap.md](../06-phase-f-migration-planning/migration-roadmap.md)).

## Cost

Total program investment is estimated at **$4.7M** (illustrative) over 18 months, including a 15% contingency. Steady-state operating cost rises only marginally (~$25K/year net, illustrative) — higher infrastructure and vendor licensing cost is nearly offset by lower incident and maintenance cost. Per-market rollout cost drops from an estimated **$420K to $110K**, saving roughly **$1.24M** in direct rollout cost across the four target markets. Full detail and arithmetic: [business-case.md](business-case.md).

## Timeline

18 months, phased: home-market platform re-architecture and first migration wave in the first 9 months, expansion-market rollouts sequenced across the remaining 9 months as each new market's configuration bundle is authored and validated. Full sequencing: [migration-roadmap.md](../06-phase-f-migration-planning/migration-roadmap.md).

## Risk, In Plain Language

This program does **not** pay for itself in direct cost savings within 3 years — that is stated plainly in the business case rather than hidden behind an optimistic ROI figure. The justification is **avoided cost of delay and avoided risk**: every month the expansion is delayed by platform limitations is estimated to cost more in deferred contribution margin than the program's total capex divided by roughly two months. The larger risk is reputational and operational — launching in a new market on an integration platform with no partner isolation carries a materially higher chance of a visible outage in a market where ForkRoute has no brand trust cushion yet. The Architecture Review Board judged that risk unacceptable to carry forward unchanged.

## The Ask

Approval of the $4.7M program budget and the 18-month timeline, with go/no-go checkpoints per market retained by the Executive Steering Committee as described in the [stakeholder RACI](stakeholder-map.md).

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
