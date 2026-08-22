# Technology Standards

## Purpose

This document defines the approved technology stack for the ForkRoute expansion program and the process by which a delivery team may request an exception. Standards here are binding under the [governance framework](../00-preliminary/governance-framework-setup.md); they are chosen to satisfy the [architecture principles](../00-preliminary/architecture-principles.md) and the [reference architecture](reference-architecture.md), not selected independently of them.

## Approved Standards by Layer

| Layer | Standard | Rationale |
|---|---|---|
| Event backbone | Durable, partitioned, replayable log technology (category standard; specific product selected via the vendor evaluation for adjacent buy decisions does not apply here — this is a build-on-open-standard layer, not a vendor product) with at-least-once delivery and consumer-group semantics | Required by Principle 3 and the reference architecture; partitioning must support per-tenant isolation per Principle 2 |
| Config store (dispatch rule bundles) | Versioned, schema-validated key-value/document store with strong read consistency | Config bundles are governed master data (Principle 1); strong consistency prevents a rider being dispatched against a stale rule set |
| Service-to-service APIs | Schema-first (interface definition precedes implementation), versioned, contract-tested in CI | Principle 6; prevents uncoordinated breaking changes across a growing service and market count |
| Relational transactional stores (Order, Rider, Partner services) | ACID-compliant relational database, one logical instance per residency zone | Principle 4; transactional integrity for entities that are not event-sourced |
| Payments event log | Append-only, immutable, cryptographically checksummed per record | Principle 3 plus the audit requirement from Security & Compliance |
| Data residency / zone boundary enforcement | Infrastructure-level network and IAM boundary per residency zone, not application-level filtering alone | Principle 4; a defense-in-depth requirement — an application bug should not be able to leak cross-zone data even if it tries |
| Currency/monetary values | Fixed-point decimal representation with explicit ISO currency code on every monetary field; no floating-point monetary arithmetic anywhere in the payments domain | Principle 9; floating-point rounding errors are unacceptable in financial calculations |
| Observability | Structured, correlation-ID-propagated logging; distributed tracing across the dispatch-to-payout path | Needed to debug a single order's journey across the decomposed services in the to-be application architecture |
| Secrets & credentials | Centralized secrets management, no credentials in configuration bundles or source | Config bundles are treated as lower-trust, frequently-changing artifacts (Principle 1); they must never be a path to credential exposure |

## Explicitly Deprecated / Disallowed Patterns

- **City- or market-keyed conditional branches in application code** for any behavior that the Market Config Store can express — this is the direct anti-pattern the program exists to remove (Principle 1, Principle 5).
- **Shared, unbounded rate limits across all restaurant partner tenants** in the integration platform — superseded by per-tenant isolation (Principle 2).
- **Floating-point types for any monetary field** — superseded by fixed-point decimal with explicit currency code (Principle 9).
- **Synchronous, tightly-coupled calls from the Payments Service directly to Reconciliation, Payout, Analytics, or Support Tooling** — superseded by the event backbone pattern, except where the [reference architecture's "when not to use"](reference-architecture.md) section applies.

## Exceptions Process

Standards exist to be followed by default, and deviating from one is a real decision that must be argued, not a form to route around governance. The process:

1. **Delivery team submits an exception request** to the ARB naming: the specific standard being deviated from, the technical reason, the alternative proposed, and — critically — an expiry or review date. A request with no review date is returned unprocessed.
2. **Weekly design review triages the request.** Low-impact, single-team-scoped exceptions may be approved at this level by the Chief Architect or a delegated architecture liaison.
3. **Exceptions with cross-team impact, budget impact above the $150K threshold, or that touch a Tier 1 capability (per the criticality tiering in [reference-architecture.md](reference-architecture.md)) escalate to the full ARB.**
4. **Every approved exception is logged in the exception register** (maintained alongside the ARB decision log described in [governance-framework-setup.md](../00-preliminary/governance-framework-setup.md)) with its approver, rationale, and review date. At the review date, the exception is either retired (the team has conformed to standard), renewed with updated justification, or — if it has proven durable and broadly applicable — proposed as an amendment to the standard itself.

## Example of an Approved Exception

During the payments backbone build, the Payments engineering team requested an exception to run a secondary, synchronous fallback path for payout instruction submission to one specific expansion market's banking partner, whose API does not support the asynchronous webhook confirmation pattern the standard event-driven payout flow assumes. The ARB approved this as a scoped, market-specific exception (not a change to the standard) with a 12-month review date, on the basis that the banking partner's roadmap indicated async support was planned, and re-architecting the payout orchestrator to accommodate one non-conforming partner permanently was not justified against a temporary integration gap.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
