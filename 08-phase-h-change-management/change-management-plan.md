# Change Management Plan

## Purpose

This document covers the organizational change impact of the ForkRoute re-architecture program — who is affected and how, the training and communication plan, and the metrics used to judge whether adoption actually succeeded. Technical migration risk is covered in [transition-architectures.md](../06-phase-f-migration-planning/transition-architectures.md); this document covers the human side.

## Change Impact Assessment

| Affected Group | Nature of the Change | Impact Severity |
|---|---|---|
| City Operations Managers | Lose direct manual control over surge pricing; shift from an operator role to a config-authoring and monitoring role | High — this is an authority change, not just a tooling change |
| Restaurant Partnerships Team | Shift from uniform hands-on onboarding to a triage model (self-service for standard cases, hands-on for complex ones) | Medium — workload composition changes, headcount planning must adjust |
| Solutions Engineering | Time reallocated away from routine onboarding toward complex-partner integration work | Medium — role scope narrows and deepens simultaneously |
| Platform & Payments Engineering | New event-sourcing and config-governance disciplines required; new operational runbooks for the event backbone | High — a genuinely new skill set for teams used to batch/synchronous patterns |
| Customer Support Leadership | New dispute-handling workflow using the payments event log's audit trail instead of next-day batch reports | Medium — process changes, but the new tooling is strictly more capable than what it replaces |
| Regional Expansion Leads | New responsibility for config bundle authoring and validation sign-off per market | High — this is new work these roles did not previously do at all |
| Finance | New real-time payout reconciliation view replaces monthly close-cycle-aligned batch reports | Medium — improves visibility but requires adjusting existing close-process assumptions |

## Why Change Management Matters Here Specifically

The single highest-severity change — City Operations Managers losing manual surge control — is also the one most likely to generate quiet resistance if not addressed directly, because it is a genuine reduction in a role's discretionary authority, not merely a new tool to learn. The change management plan treats this honestly rather than reframing it as a pure upgrade: operations managers are being asked to trust an automated system with a decision they used to make by judgment, and the plan's communication and training approach is built around earning that trust, not assuming it.

## Training Plan

| Audience | Training Approach | Timing |
|---|---|---|
| City Operations Managers | Hands-on workshops using the config dry-run simulation harness (same tool used in the [architecture contract](../07-phase-g-implementation-governance/architecture-contracts.md) acceptance criteria) against their own city's historical data, so they can see the automated engine's decisions against known outcomes before it goes live | Ahead of Transition State Alpha exit |
| Regional Expansion Leads | Structured config-authoring certification — a Regional Lead must complete a supervised config bundle authoring and validation exercise before being authorized to sign off a market's live bundle independently | Ahead of each market's Wave 2/3/4 config-authoring phase |
| Platform & Payments Engineering | Internal event-sourcing and config-governance onboarding curriculum, plus paired on-call rotations during Transition State Alpha to build operational familiarity under real (but shadow-mode) load | Ahead of and during Wave 1 |
| Restaurant Partnerships & Solutions Engineering | Workshop on the new self-service onboarding flow and triage criteria for when a partner needs hands-on support | Ahead of Transition State Bravo partner migration batches |
| Customer Support Leadership | Walkthrough of the new payments audit-trail tooling for dispute handling, with side-by-side comparison against the old batch-report workflow | Ahead of Wave 1 home-market cutover |

## Communication Plan

- **Program kickoff communication** (all affected groups): explains the business driver (expansion mandate), what is changing, and — deliberately — what is *not* changing (customer/rider/restaurant-facing app interfaces are unchanged, per [vision-and-scope.md](../01-phase-a-vision-and-scope/vision-and-scope.md)), to prevent rumor-driven overestimation of the change's scope.
| Audience | Cadence | Channel |
|---|---|---|
| Executive Steering Committee | Bi-weekly, tied to the program governance review | Written status report + review meeting |
| City Operations Managers | Monthly during Foundation Wave, weekly during their city's transition window | Team meeting + written FAQ document |
| Restaurant Partnerships & Solutions Engineering | Monthly, with a dedicated session ahead of each partner migration batch | Team meeting |
| All engineering delivery teams | Ongoing via the ARB's weekly design review minutes and bi-weekly program governance review | Published minutes |
| Restaurant partners (external) | Advance notice ahead of their own migration batch (Transition State Bravo), with a defined support contact during the migration window | Partner portal notice + email |

## Adoption Metrics

| Metric | How Measured | Target |
|---|---|---|
| City Operations Manager engagement with config dry-run tooling pre-cutover | % of managers completing at least one supervised dry-run exercise | 100% before their city's Transition State Alpha exit |
| Regional Expansion Lead config-authoring certification completion | % certified ahead of their market's config-authoring phase | 100% before Wave 2/3/4 begins for that market |
| Support ticket volume referencing confusion about surge pricing changes | Ticket tagging via existing support taxonomy | Declining trend over the first 60 days post-cutover, benchmarked against the first-week volume |
| Restaurant partner self-service onboarding completion rate (no solutions engineer involvement) | % of new partner onboardings completed without escalation | 70%+ within 90 days of Transition State Bravo completion |
| Engineering team self-reported confidence in event-sourcing/config-governance operations (survey) | Pre/post training survey, 1–5 scale | Average score improvement of at least 1.5 points pre- to post-training |

Adoption metrics are reviewed at the bi-weekly program governance review alongside technical migration metrics, on the basis that a technically successful migration with poor human adoption is not, in fact, a successful program outcome.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
