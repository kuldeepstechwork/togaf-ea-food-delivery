# Governance Framework Setup

## Purpose

This document establishes the Architecture Review Board (ARB) for the ForkRoute market-expansion program: how it is constituted, how it operates, and how its decisions are enforced. This is the Preliminary Phase governance work that the rest of the ADM cycle depends on — Phase G's [governance-framework.md](../07-phase-g-implementation-governance/governance-framework.md) describes how this body governs delivery once building starts; this document describes how the body itself is stood up and run.

## Why Governance Is Being Formalized Now

Prior to this program, ForkRoute had no standing architecture review function — city-level engineering leads made local technology and design decisions independently, which is the direct cause of the per-city dispatch-logic drift described in the [vision and scope](../01-phase-a-vision-and-scope/vision-and-scope.md). An expansion into four new markets in 18 months cannot tolerate that pattern repeating at a larger scale; the cost of drift multiplies with each new market added under the old model. Formal governance is therefore a precondition for the expansion business case, not an optional overlay.

## ARB Constitution

**Membership (voting):**

| Role | Represents |
|---|---|
| Chief Architect (chair) | Overall architecture integrity and principle enforcement |
| VP Engineering, Platform | Core services, dispatch, and integration platform |
| VP Engineering, Payments | Payments, reconciliation, and financial controls |
| Head of Data | Data architecture, residency, and analytics |
| Head of Security & Compliance | Security architecture, regulatory and data-protection compliance |
| Regional Expansion Lead (rotating, per active market) | Market-specific regulatory and operational constraints |

**Non-voting standing attendees:** Program Manager (expansion program), a delivery-team representative for each item under review, and Finance (for decisions with material budget impact).

**Quorum:** Four of six voting members, including the Chief Architect or a delegated deputy.

## Cadence

- **Weekly design review (60 minutes):** Reviews architecture proposals below the threshold requiring a full contract (see [architecture-contracts.md](../07-phase-g-implementation-governance/architecture-contracts.md)). Lightweight, decision-focused, minutes published within 24 hours.
- **Bi-weekly program governance review (90 minutes):** Reviews roadmap progress against the [migration roadmap](../06-phase-f-migration-planning/migration-roadmap.md), open architecture risks, and any escalations.
- **Ad hoc exception review:** Convened within 3 business days of a delivery team requesting a deviation from an approved standard or principle (see the exceptions process in [technology-standards.md](../04-phase-d-technology-architecture/technology-standards.md)).
- **Quarterly principle review:** The ARB re-examines the [architecture principles](architecture-principles.md) themselves against how the program has actually unfolded, and formally amends or retires a principle if evidence warrants — principles are living governance instruments, not a document frozen at kickoff.

## Escalation Path

1. **Delivery team ↔ Architecture liaison:** Most day-to-day architecture questions are resolved between a delivery team and its assigned architecture liaison without a full ARB session.
2. **Weekly design review:** Unresolved or cross-team-impacting questions go here first.
3. **Full ARB (bi-weekly or ad hoc):** Escalated when a decision trades off an approved principle, carries budget impact above $150K illustrative, or is contested between two VP-level stakeholders.
4. **Executive Steering Committee:** Reserved for decisions that change program scope, timeline, or the approved business case (see [business-case.md](../01-phase-a-vision-and-scope/business-case.md)). The ARB escalates to this committee; it does not resolve scope disputes on its own authority.

An escalation is never silently dropped: every item raised to the ARB receives either a decision, a deferral with a named owner and date, or a formal escalation upward within one review cycle.

## Principles Enforcement

The ARB enforces the [architecture principles](architecture-principles.md) through three mechanisms:

- **Design review gate:** No solution building block (Phase E) proceeds to delivery without an ARB sign-off that names which principles it satisfies and which, if any, it deviates from and why.
- **Architecture contracts:** Every delivery team engagement is bound by an architecture contract (Phase G) that references the specific principles and standards in force; non-compliance is a contract breach subject to the escalation path above, not an informal conversation.
- **Exception register:** Every approved deviation from a principle or standard is logged with its rationale, approver, and an expiry or review date, so that "temporary" exceptions do not silently become permanent architecture by default.

## Decision Record

All ARB decisions of architectural significance are captured as an ADR (see `adrs/`) or, for less significant but still binding decisions, as a dated entry in the ARB decision log maintained by the Program Manager. This operationalizes Principle 8 — decisions are recorded, not tribal.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
