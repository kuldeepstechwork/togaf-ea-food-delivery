# TOGAF ADM Phase Mapping

This table maps each phase of the TOGAF Architecture Development Method (ADM) cycle to the folder in this repository that contains its artifacts, and states in one line what each phase was used to decide for the ForkRoute engagement.

| TOGAF ADM Phase | Folder | What This Phase Covers Here |
|---|---|---|
| Preliminary Phase | [00-preliminary/](00-preliminary/) | Establishes the architecture principles ForkRoute's EA function will govern by, and stands up the Architecture Review Board (ARB) and its operating model. |
| Phase A — Architecture Vision | [01-phase-a-vision-and-scope/](01-phase-a-vision-and-scope/) | Defines the problem, target-state vision, explicit scope boundaries, stakeholder map, business case with cost/ROI math, and a CxO-facing executive summary. |
| Phase B — Business Architecture | [02-phase-b-business-architecture/](02-phase-b-business-architecture/) | Models the as-is and to-be business processes for order dispatch, restaurant onboarding, and payments/reconciliation, plus a capability map with maturity scoring. |
| Phase C — Information Systems Architecture | [03-phase-c-information-systems-architecture/](03-phase-c-information-systems-architecture/) | Covers both the data architecture (domains, entities, data residency) and application architecture (as-is monolith vs. to-be service decomposition) sub-phases (C1/C2). |
| Phase D — Technology Architecture | [04-phase-d-technology-architecture/](04-phase-d-technology-architecture/) | Defines the reference architecture for the event-driven dispatch/payments backbone, and the approved technology standards with an exceptions process. |
| Phase E — Opportunities & Solutions | [05-phase-e-opportunities-and-solutions/](05-phase-e-opportunities-and-solutions/) | Decomposes target capabilities into solution building blocks, runs a build-vs-buy vendor evaluation for routing/reconciliation platforms, and consolidates the gap analysis. |
| Phase F — Migration Planning | [06-phase-f-migration-planning/](06-phase-f-migration-planning/) | Sequences the work into a phased, quarter-by-quarter roadmap and defines named transition architectures between as-is and to-be. |
| Phase G — Implementation Governance | [07-phase-g-implementation-governance/](07-phase-g-implementation-governance/) | Defines how architecture compliance is checked during delivery, and what an architecture contract between the EA function and a delivery team contains. |
| Phase H — Architecture Change Management | [08-phase-h-change-management/](08-phase-h-change-management/) | Covers organizational change impact, training, communications, and adoption metrics for the program. |
| Requirements Management (cross-cutting) | [adrs/](adrs/) | The five Architecture Decision Records are the durable record of the specific, significant decisions made across all phases above; they are cross-referenced from the phase documents they support. |

Phases B, C, and D in TOGAF are commonly run iteratively rather than strictly sequentially once a baseline vision exists; that was the case here. The folder numbering in this repository reflects the canonical ADM order for readability, not the literal order in which every workshop was run.

---
*Fictional case study — see [README](README.md) for full disclaimer.*
