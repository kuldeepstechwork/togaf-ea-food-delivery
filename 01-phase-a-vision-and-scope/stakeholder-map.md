# Stakeholder Map

## Purpose

This document identifies the stakeholders in the ForkRoute expansion architecture program, their primary concerns, and their RACI accountability across the program's major decision categories. It is used by the ARB to determine who must be consulted or informed before a decision is finalized, and is referenced by the [governance framework](../00-preliminary/governance-framework-setup.md) when routing escalations.

## Stakeholder Concerns

| Stakeholder | Primary Concern | Key Questions They Will Ask |
|---|---|---|
| CEO / Executive Steering Committee | Does the architecture de-risk the 18-month expansion commitment made to the board? | What is the payback period? What happens if we don't do this? |
| CFO / Finance | Capital efficiency, TCO, payment/reconciliation financial controls | What is the 3-year TCO delta? Where is the audit trail on money movement? |
| VP Engineering, Platform | Delivery feasibility, technical debt, team capacity | Can my teams actually build this in 18 months without burning out? |
| VP Engineering, Payments | Correctness and auditability of financial state | How do we guarantee no money is lost or double-counted during migration? |
| Head of Data | Data residency compliance, analytics continuity | Does this keep us compliant in each new jurisdiction from day one? |
| Head of Security & Compliance | Regulatory exposure, data protection, PCI-adjacent scope | Does the new payments event log expand our compliance scope, and how is it controlled? |
| Regional Expansion Leads (x4, one per target market) | Local regulatory and operational fit | Does the platform actually support this market's tax, currency, and labor rules? |
| Restaurant Partnerships (Business) | Partner experience, onboarding friction, SLA to partners | Does this make partner onboarding faster or slower? What is the migration experience for existing partners? |
| Rider/Courier Operations | Payout reliability, dispatch fairness | Will riders get paid faster and dispatched fairly across markets? |
| Customer Support Leadership | Operational stability during migration | What breaks for customers during cutover, and how do we handle it? |
| Engineering Delivery Teams (dispatch, payments, integration) | Clear, stable requirements and realistic timelines | What exactly are we accountable for building, and against what contract? |
| External Vendors (routing/reconciliation platform candidates) | Clear evaluation criteria, fair process | What are we being scored against? |

## RACI — Program-Level Decisions

R = Responsible, A = Accountable, C = Consulted, I = Informed

| Decision Area | CEO/Steering Committee | CFO/Finance | Chief Architect | VP Eng Platform | VP Eng Payments | Head of Data | Head of Security | Regional Leads | Partnerships (Biz) | Delivery Teams |
|---|---|---|---|---|---|---|---|---|---|---|
| Program charter & budget approval | A | R | C | C | C | I | I | C | I | I |
| Architecture principles | I | I | A/R | C | C | C | C | I | I | I |
| Dispatch engine target architecture | I | I | A | R | I | I | I | C | C | R |
| Payments/reconciliation target architecture | I | C | A | I | R | I | C | I | I | R |
| Restaurant integration platform architecture | I | I | A | R | I | I | C | I | C | R |
| Data residency approach per market | I | I | A | I | I | R | C | R | I | C |
| Vendor selection (routing/reconciliation) | I | C | A | C | C | I | C | I | I | C |
| Migration roadmap & sequencing | C | C | A | R | R | I | I | C | C | R |
| Go/no-go per market launch | A | C | R | C | C | C | C | R | C | I |
| Change management & training plan | I | I | C | C | C | I | I | C | R | C |

## Notes on Contested Accountabilities

Two RACI assignments were explicitly debated at program kickoff and are recorded here for transparency:

- **Data residency approach per market** was initially proposed as Chief-Architect-accountable throughout, but Regional Expansion Leads argued they carry the direct regulatory relationship in-market and needed shared Responsible status. The ARB agreed: Head of Data is Responsible for the technical implementation, Regional Leads are Responsible for confirming local regulatory interpretation, and Chief Architect retains overall Accountable status to resolve any conflict between the two.
- **Go/no-go per market launch** was contested between the CEO (who wanted sole authority) and the Chief Architect (who argued technical readiness needed a named Responsible owner distinct from the business go/no-go). The resolution: CEO/Steering Committee is Accountable for the business decision to launch; Chief Architect is Responsible for certifying technical readiness as an input to that decision, not a veto over it.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
