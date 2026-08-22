# Architecture Contracts

## Purpose

An architecture contract is the binding agreement between the EA function (represented by the Chief Architect) and a delivery team, defining exactly what the team is accountable for building, against which principles and standards, with what acceptance criteria. It is the enforcement mechanism referenced throughout [governance-framework.md](governance-framework.md).

## What an Architecture Contract Contains

1. **Scope statement:** the specific solution building block(s) covered, referencing [solution-building-blocks.md](../05-phase-e-opportunities-and-solutions/solution-building-blocks.md).
2. **Applicable principles and standards:** the specific numbered principles ([architecture-principles.md](../00-preliminary/architecture-principles.md)) and technology standards ([technology-standards.md](../04-phase-d-technology-architecture/technology-standards.md)) the delivery team must design against.
3. **Interfaces and contracts owed to other teams:** what this building block must expose (APIs, event schemas) and what it depends on receiving from others, per Principle 6.
4. **Non-functional requirements:** availability tier, latency targets, and data residency constraints applicable to this building block, per the criticality tiering in [reference-architecture.md](../04-phase-d-technology-architecture/reference-architecture.md).
5. **Acceptance criteria:** the specific, testable conditions under which the ARB will sign off at the pre-production readiness review.
6. **Exception log reference:** any pre-approved deviations from standard, cross-referenced to the exception register.
7. **Signatories and date:** Chief Architect and delivery team engineering lead, both required before resourcing begins.

## Filled Example: Config-Driven Dispatch Engine Contract

**Building Block:** Config-Driven Dispatch Engine (per [solution-building-blocks.md](../05-phase-e-opportunities-and-solutions/solution-building-blocks.md))

**Delivery Team:** Platform Engineering — Dispatch Squad

**Applicable Principles:** Principle 1 (Configuration Over Code), Principle 5 (Every New Market Is a Configuration, Not a Fork), Principle 6 (Interfaces Are Contracts), Principle 10 (Resilience Scoped to Business Impact)

**Applicable Standards:** Schema-first API contracts with CI contract testing; config store with strong read consistency; structured logging with correlation-ID propagation (per [technology-standards.md](../04-phase-d-technology-architecture/technology-standards.md))

**Interfaces Owed:**
- Publishes `DispatchOffered`, `DispatchAccepted`, `DispatchDeclined`, and `SurgeApplied` events to the payments event backbone, versioned per the schema registry.
- Exposes a synchronous `POST /dispatch/offer` API to the Order Service with a p99 latency target of under 800ms (within the program's 2-second end-to-end dispatch SLA, leaving headroom for upstream/downstream legs).
- Consumes `MarketConfigBundle` versions from the Market Config Store; must reject and alert on any config bundle that fails schema validation rather than falling back silently to a prior version without logging the fallback.

**Non-Functional Requirements:**
- Availability tier: Tier 1 (financial/operational impact) per the criticality tiering framework — 99.95% target.
- Every dispatch decision must log which config bundle version was active, satisfying the audit traceability requirement from Security & Compliance.
- Config bundle changes must be evaluable in a simulation/dry-run mode against historical order data before publishing, to catch unintended rule interactions before they affect live dispatch.

**Acceptance Criteria (checked at pre-production readiness review):**
- Zero city-keyed conditional branches remain in the dispatch codebase; all market variation is demonstrably driven by config bundle content alone (verified by code review plus a test that runs the same binary against two different config bundles and confirms divergent behavior with no code change).
- Dry-run simulation harness is operational and has been exercised against at least one real historical dataset with results reviewed by the Regional Expansion Lead for the market in question.
- p99 latency target met under a load test simulating projected peak order volume for the largest target market.
- Divergence rate against the legacy dispatch service, measured during Transition State Alpha (per [transition-architectures.md](../06-phase-f-migration-planning/transition-architectures.md)), is below the 0.5% exit threshold for a sustained two-week window.

**Exceptions Logged Against This Contract:** None at contract signing. (One exception was later logged during delivery — see the exception register — for a temporary synchronous fallback affecting a downstream payout integration, unrelated to this block's own scope but noted here for cross-reference completeness.)

**Signatories:** Chief Architect; Dispatch Squad Engineering Lead. Signed prior to squad resourcing, per the ordering principle in [governance-framework.md](governance-framework.md).

## Why Contracts Are Written This Specifically

A vaguer contract ("build the dispatch engine to be config-driven") would be unenforceable at the pre-production readiness review — there would be no objective basis for the ARB to say yes or no. Writing testable acceptance criteria up front, even though it takes longer to negotiate the contract initially, is a deliberate trade: slower contract sign-off in exchange for a readiness review that is a real gate rather than a formality.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
