# ADR-001: Config-Driven Dispatch Engine

**Status:** Accepted

## Context

ForkRoute's order-dispatch and driver-allocation logic is currently implemented as city-specific code paths within the dispatch service. Each of the current cities effectively runs its own variant of dispatch logic, hand-coded and largely undocumented in terms of how or why it diverges from other cities. Standing up dispatch logic for a new city has historically taken an estimated 8–10 weeks of engineering effort. With four new countries committed within 18 months, this model does not scale: even in the best case it implies roughly 32–40 weeks of dispatch-specific engineering effort sequenced across markets, competing directly with every other engineering priority in the program, and it leaves no defensible single source of truth for why any given market's rules are what they are.

## Decision

Replace the city-coded dispatch service with a **single, config-driven dispatch engine**: one deployable codebase whose market-specific behavior (delivery radius, rider-matching weights, order-batching rules, surge parameters) is entirely determined by a versioned, schema-validated configuration bundle loaded at runtime. Adding or changing a market's dispatch behavior becomes an act of authoring and publishing configuration, not writing and deploying code.

## Alternatives Considered

**1. Single global dispatch service with no per-market variation.** Rejected because ForkRoute's markets genuinely differ in ways that matter to dispatch quality — delivery density, typical trip distance, regulatory constraints on driver working patterns — and a one-size-fits-all rule set would degrade dispatch quality in at least some markets. This alternative optimizes for engineering simplicity at a cost the business was not willing to accept in customer and rider experience.

**2. Per-market dispatch service with shared libraries.** Each market gets its own deployable dispatch service, sharing a common library for core matching logic but with market-specific services layered on top. Rejected because it does not solve the underlying problem — it formalizes the fork rather than eliminating it, and still requires a code deployment (of the market-specific service) to change market behavior. It was seriously considered because it offers stronger per-market blast-radius isolation than the config-driven engine (a bug in one market's service cannot directly affect another's runtime), but the ARB judged the ongoing maintenance burden of N deployable services, and the recurring code-change requirement per market change, to outweigh that isolation benefit.

**3. Config-driven engine with market-specific rule sets loaded at runtime (the selected option).** Provides the isolation benefit of alternative 2 at the data layer (one market's bad config cannot affect another's, since each dispatch decision loads only its own market's bundle) while eliminating the code-deployment requirement for market-level changes entirely.

## Consequences

**Positive:**
- New-market dispatch lead time is expected to drop from 8–10 weeks to roughly 3 weeks (config authoring, validation, and dry-run testing), directly supporting the program's headline success metric.
- A single codebase means dispatch-quality improvements (better matching algorithms, better batching heuristics) benefit every market simultaneously, rather than requiring N separate patches.
- Full auditability: every dispatch decision can be traced to the exact config bundle version that produced it, satisfying the Security & Compliance audit requirement.

**Negative (accepted trade-offs):**
- The runtime rules-interpretation layer adds an estimated 8–12ms of latency per dispatch decision compared to compiled, market-specific code paths. This was judged acceptable against the program's 2-second end-to-end dispatch SLA — see the architecture contract's p99 latency target of 800ms, which retains ample headroom.
- Configuration becomes a new, first-class failure mode: a malformed or logically incorrect config bundle can degrade dispatch quality just as badly as a code bug, but without the safety net of code review and CI that engineers are used to relying on. This is mitigated by treating config bundles as governed master data (schema validation, dry-run simulation harness, ARB-adjacent review) rather than as a casual admin-panel edit — but it does not eliminate the risk, only manage it.
- Engineers must design market-parameterizable logic from the start, which increases upfront design time for the first market (the home market's migration) in exchange for near-zero incremental engineering cost for markets 2 through 5.

## Governance

This decision was reviewed and approved by the full ARB given its cross-cutting impact on Platform Engineering, the Regional Expansion Leads, and the program's headline success metric. It directly implements Principle 1 and Principle 5 (see [architecture-principles.md](../00-preliminary/architecture-principles.md)).

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
