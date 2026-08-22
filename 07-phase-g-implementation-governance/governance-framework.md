# Implementation Governance Framework

## Purpose

Where [governance-framework-setup.md](../00-preliminary/governance-framework-setup.md) establishes the Architecture Review Board itself, this document defines how that governance is actually exercised during delivery — the compliance checkpoints, the architecture contracts process, and how non-compliance is handled once building is underway.

## Compliance Checkpoints

Architecture compliance is checked at three points in each delivery team's lifecycle, not only at the start:

| Checkpoint | Timing | What Is Checked | Who Checks |
|---|---|---|---|
| Design review | Before implementation begins | Proposed design against [architecture principles](../00-preliminary/architecture-principles.md) and [technology standards](../04-phase-d-technology-architecture/technology-standards.md); alternatives considered are documented | ARB (weekly design review) |
| Contract sign-off | Before a delivery team is resourced | An [architecture contract](architecture-contracts.md) is signed by the Chief Architect and the delivery team's engineering lead | Chief Architect + delivery lead |
| Pre-production readiness review | Before a building block goes live (including before each wave's go/no-go, per [migration-roadmap.md](../06-phase-f-migration-planning/migration-roadmap.md)) | Contract terms actually met; deviations logged as exceptions with sign-off, not silently absorbed | ARB |

A fourth, lighter-weight checkpoint — a **quarterly compliance audit** — spot-checks live building blocks against their original contracts even after go-live, specifically to catch drift that accumulates gradually (a config bundle that grew an undocumented special-case rule, for instance) rather than arriving as a single reviewable change.

## Architecture Contracts Process

Every solution building block identified in [solution-building-blocks.md](../05-phase-e-opportunities-and-solutions/solution-building-blocks.md) that is built (not bought) is delivered under a formal architecture contract between the EA function and the responsible delivery team. The contract process, and a filled example, is documented in [architecture-contracts.md](architecture-contracts.md). In brief, a contract is proposed by the delivery lead, reviewed by the ARB at the design-review checkpoint, and signed before resourcing — this ordering is deliberate: signing before resourcing means the team is staffed against an agreed, reviewed scope, not against a scope that gets renegotiated after people are already committed to a sprint plan.

## Non-Compliance Handling

Non-compliance is treated as a spectrum, not a binary pass/fail, and the response is proportionate:

1. **Minor, correctable drift** (e.g., a missing correlation-ID field in a new event type) — flagged at the next design review or pre-production readiness review, corrected before go-live, no escalation needed.
2. **Material deviation from a signed contract without an approved exception** — escalated to the full ARB per the escalation path in [governance-framework-setup.md](../00-preliminary/governance-framework-setup.md). The delivery team's engineering lead and the Chief Architect jointly present the situation; the ARB's options are to retroactively approve an exception (if the deviation is sound but wasn't pre-cleared), require remediation before proceeding further, or — in the most serious case — pause the workstream.
3. **Repeated non-compliance from the same delivery team** — treated as a signal that the contract itself, the team's capacity, or the review process is miscalibrated, and is raised as a standing agenda item at the bi-weekly program governance review rather than re-litigated incident by incident.

Non-compliance is never handled by quietly updating documentation to match what was actually built after the fact — that would defeat the entire purpose of Principle 8 (decisions recorded, not tribal). Every deviation, whether it turns out to be a good idea or not, is a first-class recorded event.

## Governance Handover at Program Close

Per the [migration roadmap](../06-phase-f-migration-planning/migration-roadmap.md), Wave 4 includes a formal governance handover: the program-specific ARB structure (which includes a rotating Regional Expansion Lead seat) transitions to ForkRoute's standing engineering governance function. The architecture principles, exception register, and decision log all carry forward unchanged — this handover changes who staffs the review body, not what it enforces.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
