# TOGAF Enterprise Architecture Case Study: ForkRoute

**Disclaimer:** This is an illustrative TOGAF Enterprise Architecture case study modeling common, publicly known challenges in food delivery marketplaces — not a real engagement. ForkRoute is an invented name, not affiliated with any real company, and nothing here is based on confidential information from any real employer or client. All figures, vendor names, and technical details are constructed for this exercise.

---

## Program Overview

This repository documents a full TOGAF Architecture Development Method (ADM) cycle for **ForkRoute**, a fictional food delivery marketplace with roughly 2 million monthly active users, 15,000 restaurant partners, and 40,000 active delivery riders operating in a single home market. ForkRoute's leadership has committed to expanding into four new countries within 18 months, and has funded an Enterprise Architecture engagement to determine whether the current platform can support that expansion and, if not, what target-state architecture should replace it. This repository is the architecture function's work product for that engagement: principles, current- and target-state models, gap analysis, a phased migration roadmap, governance artifacts, and the architecture decision records behind the platform's most consequential design choices.

## The Business Problem

Three structural issues in ForkRoute's current platform sit directly in the path of the expansion program:

1. **Order-dispatch and driver-allocation logic is hardcoded per city.** Each new city has historically required a code branch (or in several cases a forked service) to encode local business rules — radius thresholds, batching windows, surge triggers. The rules have drifted city to city with no single source of truth, and every new market has historically taken an estimated 8–10 weeks of engineering effort before its first order can be dispatched.
2. **The restaurant partner integration platform is a single point of failure with no multi-tenant isolation.** All 15,000 restaurant partners share one integration tier with no per-tenant blast-radius containment; a menu-sync defect or partner-side traffic spike from one restaurant chain has, on more than one occasion, degraded order intake platform-wide.
3. **Payments reconciliation runs as nightly batch jobs** across customers, restaurants, and riders. This cannot support the real-time payout expectations the new markets require, and it cannot reliably support dynamic surge pricing, which needs a live, auditable view of fares as they are calculated and settled.

The architecture engagement documented here addresses all three, with the explicit goal of a platform that can be **replicated market-by-market through configuration, not re-engineering.**

## How This Repository Is Organized

This repository follows the TOGAF ADM phase structure. See **[TOGAF-ADM-MAPPING.md](TOGAF-ADM-MAPPING.md)** for the full phase-to-folder map. In brief:

| Folder | TOGAF Phase |
|---|---|
| [00-preliminary/](00-preliminary/) | Preliminary Phase — principles and governance setup |
| [01-phase-a-vision-and-scope/](01-phase-a-vision-and-scope/) | Phase A — Architecture Vision |
| [02-phase-b-business-architecture/](02-phase-b-business-architecture/) | Phase B — Business Architecture |
| [03-phase-c-information-systems-architecture/](03-phase-c-information-systems-architecture/) | Phase C — Information Systems Architecture |
| [04-phase-d-technology-architecture/](04-phase-d-technology-architecture/) | Phase D — Technology Architecture |
| [05-phase-e-opportunities-and-solutions/](05-phase-e-opportunities-and-solutions/) | Phase E — Opportunities & Solutions |
| [06-phase-f-migration-planning/](06-phase-f-migration-planning/) | Phase F — Migration Planning |
| [07-phase-g-implementation-governance/](07-phase-g-implementation-governance/) | Phase G — Implementation Governance |
| [08-phase-h-change-management/](08-phase-h-change-management/) | Phase H — Architecture Change Management |
| [adrs/](adrs/) | Architecture Decision Records supporting the above |

## How to Read This Repository

This is written in **decision voice, not build voice**. You will not find "we deployed a Kafka cluster" — you will find "we evaluated three dispatch topologies, rejected two, and accepted a specific, quantified trade-off in choosing the third." Every phase document states the decision made, the alternatives that were seriously considered and rejected, the trade-offs accepted (cost, risk, timeline, complexity — illustrative but plausible figures), and which governance body or stakeholder group the decision is accountable to. The Architecture Decision Records in `adrs/` are the sharpest expression of this: each one is a single significant decision, argued on its merits, with consequences stated plainly — including the ones that cut against the recommendation.

Start with [TOGAF-ADM-MAPPING.md](TOGAF-ADM-MAPPING.md) for the map of the whole repository, then [01-phase-a-vision-and-scope/executive-summary.md](01-phase-a-vision-and-scope/executive-summary.md) for the one-page version aimed at a CxO reader.

---
*Fictional case study — see disclaimer above for full context.*
