# ADR-002: Event-Driven Payments Reconciliation

**Status:** Accepted

## Context

ForkRoute's payments reconciliation across customers, restaurants, and riders currently runs as a nightly batch job: transactional tables accumulate the day's activity, and a batch process aggregates, applies commission logic, and produces payout instructions submitted the following business day. This model cannot support two explicit requirements of the expansion program: real-time payout expectations in the new markets, and dynamic, calculated surge pricing whose splits must flow correctly through to payout in near-real time. It also means discrepancies between what customers were charged and what restaurants/riders were owed surface only once daily, at best, rather than as they occur.

## Decision

Replace batch reconciliation with an **event-sourced payments architecture**: every financial state change (authorization, capture, commission calculation, payout instruction, refund, dispute) is written as an immutable event to an append-only, replayable log, which becomes the authoritative system of record. Reconciliation becomes a continuous process that consumes this log, rather than a nightly job that reads transactional snapshot tables.

## Alternatives Considered

**1. Keep nightly batch, but run it more frequently (e.g., hourly).** Rejected because it does not address the underlying architectural limitation — it reduces the *maximum* latency of discrepancy detection and payout but does not provide a genuinely real-time capability, and it does not solve the harder problem of correctly calculating and threading a dynamic surge split through to payout, which requires knowing the pricing decision at the moment it was made, not reconstructing it from a batch snapshot hours later. This was seriously considered as a lower-risk, lower-cost incremental improvement, and rejected specifically because it would not meet the sub-30-minute payout target the business case is built around.

**2. Synchronous, strongly-consistent payment ledger with direct calls from every consumer (reconciliation, payout, analytics, support) to the payments service.** Rejected because it creates a shared-fate dependency: if the reconciliation service is slow or down, it should not be able to affect the payments service's ability to process a live payment authorization, but a synchronous coupled design makes exactly that failure mode possible. This alternative was attractive for its simplicity (no eventual-consistency reasoning required) but the ARB judged the coupling risk unacceptable for a Tier 1, financial-impact capability.

**3. Event-sourced payments with an append-only log as system of record (the selected option).** Decouples producers from consumers, gives every consumer (reconciliation, payout, analytics, support, audit) its own independently-scaled, independently-failing view of state, and provides a natural audit trail as a byproduct of the architecture rather than a bolted-on reporting feature.

## Consequences

**Positive:**
- Payout latency drops from next-business-day to a sub-30-minute target, directly enabling the real-time payout expectation the expansion markets require.
- Discrepancy detection moves from a once-daily batch check to continuous, near-real-time alerting (2–5 minute lag target for Tier 1 consumers, per [reference-architecture.md](../04-phase-d-technology-architecture/reference-architecture.md)).
- Dynamic surge pricing becomes technically supportable end-to-end: a surge calculation event and its corresponding payout split are both first-class, auditable events in the same log.
- Full replay capability provides a genuine disaster-recovery and audit advantage over the batch model — any consumer's state can be rebuilt from the log from scratch.

**Negative (accepted trade-offs):**
- This is a materially higher engineering bar than the batch model it replaces. Event-sourcing discipline (idempotency keys, schema versioning, replay tooling, dead-letter handling) is new operational surface area for teams accustomed to transactional-table-plus-batch-job patterns, and getting it wrong in the payments domain has direct financial and regulatory consequences — this is not a domain where "we'll harden it later" is acceptable.
- Eventual consistency is now a property of the system that every downstream consumer must reason about explicitly. This is mitigated by the differentiated criticality tiering (Payout Orchestrator held to a 2-minute lag SLA; Analytics tolerates 4 hours) but does not eliminate the conceptual burden on engineers building against the log.
- The nightly-batch general ledger integration is not being replaced in this program (see [vision-and-scope.md](../01-phase-a-vision-and-scope/vision-and-scope.md) scope exclusions); the event log's output must be adapted to feed that legacy integration, which is an explicitly accepted piece of ongoing technical debt rather than a clean architectural boundary, pending a separate future finance-systems initiative.

## Governance

Approved by the full ARB with VP Engineering, Payments as Responsible owner and Chief Architect as Accountable, per the RACI in [stakeholder-map.md](../01-phase-a-vision-and-scope/stakeholder-map.md). This decision directly implements Principle 3.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
