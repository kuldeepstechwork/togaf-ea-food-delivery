# Transition Architectures

## Why Named Transition States Matter

A migration plan that only describes as-is and to-be leaves the riskiest period of the program — the period where both systems are partially true at once — undocumented. This document names and describes the intermediate architecture states ForkRoute's platform passes through during migration, so that delivery teams, the ARB, and operations know exactly what is true, and what is not yet true, at each stage.

## Transition State Alpha — Home Market Dual-Run

**When:** Wave 1 of the [migration roadmap](migration-roadmap.md), home market only.

**What is true at this state:**

- The config-driven dispatch engine and the event-driven payments backbone are both live in production, receiving a shadow copy of real order traffic, but the **legacy city-coded dispatch service and nightly batch reconciliation remain the systems of record** for actual rider offers and actual payout instructions.
- Every order is processed by both the legacy path and the new path; a reconciliation job (distinct from, and simpler than, the eventual Continuous Reconciliation Service) compares the two systems' dispatch decisions and payment calculations and flags divergence for engineering review.
- Customers, riders, and restaurants experience **no behavior change** during this state — they are served entirely by the legacy path. The new path's output is observed, not acted upon.

**Why this state is necessary:** The event-driven payments backbone is a Tier 1, financial-impact capability (per the [reference architecture](../04-phase-d-technology-architecture/reference-architecture.md)); cutting it over without a validation period against known-correct legacy output would mean discovering a reconciliation bug in production with real money at stake and no baseline to compare against. Dual-run converts "trust the new system because we tested it" into "trust the new system because we watched it agree with the old one across weeks of real traffic."

**Exit criteria:** Divergence rate between legacy and new-path dispatch decisions below 0.5%, and zero unexplained payment-calculation divergences, sustained for a minimum two-week window, before proceeding to full cutover. This threshold was set by the VP Engineering, Payments, in consultation with the ARB, and is documented as a binding exit gate, not a target to aim for loosely.

**What changes in the next state:** Full cutover — the new platform becomes the system of record, and the legacy dispatch service and batch reconciliation job are decommissioned for the home market, not merely deprioritized.

## Transition State Bravo — Restaurant Integration Platform Coexistence

**When:** Runs concurrently with Transition State Alpha, since restaurant integration isolation was sequenced as a parallel workstream in the gap analysis.

**What is true at this state:**

- New restaurant partners (post a defined cutoff date) are onboarded directly onto the tenant-isolated integration platform.
- Existing partners remain on the legacy shared-fate integration tier until migrated in scheduled batches, grouped by traffic profile — large chains are migrated in small, closely monitored batches; smaller independent restaurants are migrated in larger batches once the pattern is validated on the higher-risk chains.
- Both integration tiers are simultaneously live and both feed the same downstream Order Service, meaning the Order Service must, for the duration of this state, tolerate input from two structurally different upstream integration paths.

**Why this state is necessary:** Migrating all 15,000 existing restaurant partners in a single cutover would concentrate migration risk into one event with no ability to isolate or roll back a single problematic partner's migration; batch migration by traffic profile lets a bad migration affect a small, known group rather than the whole partner base.

**Exit criteria:** All existing partners migrated off the legacy shared tier, and the legacy tier decommissioned. Target: end of Wave 1.

## Transition State Charlie — Multi-Currency Partial Coverage

**When:** Between Wave 1 (home market, single currency) and Wave 2 (Market A launch, second currency introduced).

**What is true at this state:**

- The Multi-Currency Financial Primitives Library (per [solution-building-blocks.md](../05-phase-e-opportunities-and-solutions/solution-building-blocks.md)) is fully implemented and enforced in the Payments Event Log and Payout Orchestrator, but **only one currency is actually in live use** — the home market's.
- This is a deliberate "build the capability before you need it, but don't wait to prove it works before you need it either" state: the multi-currency support is validated through synthetic multi-currency test transactions and a controlled internal pilot (ForkRoute's own test-partner accounts transacting in a second currency in a non-production-adjacent environment) rather than through live customer traffic, because no live second currency exists yet at this point in the timeline.

**Why this state is necessary:** Waiting until Market A's actual launch to discover whether the multi-currency primitives work correctly would put a foundational financial-correctness question on the critical path of a live market launch. Validating it against synthetic and pilot data ahead of time, while the home market is still the only live currency, de-risks Wave 2 specifically.

**Exit criteria:** Successful synthetic and pilot validation, signed off by VP Engineering, Payments, ahead of Market A's config-authoring phase beginning.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
