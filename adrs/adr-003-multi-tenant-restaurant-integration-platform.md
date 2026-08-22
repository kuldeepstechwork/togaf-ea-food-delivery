# ADR-003: Multi-Tenant Restaurant Integration Platform

**Status:** Accepted

## Context

All 15,000 of ForkRoute's restaurant partners integrate through a single shared integration tier handling menu sync, order injection, and status updates, with no per-partner resource isolation. A menu-sync defect or a traffic spike from one partner (a large chain's promotional campaign, for instance) runs in the same shared infrastructure as every other partner's traffic, and has on multiple occasions degraded order intake platform-wide. The expansion program implies roughly 2.5x the current partner count across four new markets, which increases both the frequency of partner-side anomalies (more partners, more chances for one to misbehave) and the blast radius of the current shared-fate design if left unaddressed.

## Decision

Re-architect the restaurant integration platform to be **explicitly multi-tenant**: each restaurant partner (or partner group, for chains operating multiple locations under shared infrastructure) is provisioned with isolated rate limits, dedicated processing lanes, and independent circuit breakers, such that one partner's traffic anomaly or integration failure is contained to that partner and cannot degrade the platform for others.

## Alternatives Considered

**1. Global rate limiting with a higher ceiling, sized for the expansion partner count.** Rejected because global limiting protects the platform in aggregate but does nothing to stop one noisy or misbehaving partner from consuming a disproportionate share of that global ceiling and starving others within it — it addresses the symptom (platform overload) without addressing the actual failure mode (one partner degrading another's experience). This was considered as a low-cost interim measure and rejected as insufficient for the target state, though a variant of it may still be used as a coarse outer safety net alongside per-tenant isolation.

**2. Fully separate integration infrastructure per partner (dedicated deployment per restaurant chain).** Rejected as operationally unworkable at ForkRoute's scale — provisioning and maintaining 15,000+ fully separate deployments (or even a meaningful subset for large chains) would consume far more operational overhead than the isolation benefit justifies, and most independent restaurant partners generate traffic volumes low enough that full dedicated infrastructure would be significant over-provisioning.

**3. Tenant-scoped API gateway with per-tenant quotas, circuit breakers, and processing lanes within a shared but isolated platform (the selected option).** Provides meaningful isolation (a bad partner cannot exhaust another's resources) without the operational cost of full per-partner infrastructure duplication — tenants share underlying infrastructure but are logically and operationally isolated within it.

## Consequences

**Positive:**
- Platform-wide order-intake degradation from a single partner's incident is expected to drop to zero (contained to single-tenant blast radius), directly addressing the success metric in [vision-and-scope.md](../01-phase-a-vision-and-scope/vision-and-scope.md).
- Onboarding at the expansion's target partner scale becomes materially safer — the platform's failure characteristics do not get worse simply because there are more partners.
- Per-tenant observability (quota consumption, error rates) becomes a natural byproduct, which also improves the partnerships team's ability to have data-driven conversations with individual restaurant partners about their integration health.

**Negative (accepted trade-offs):**
- Adds real infrastructure cost and operational complexity — per-tenant quota management, monitoring, and circuit-breaker configuration is ongoing operational surface area, not a one-time setup cost, consistent with Principle 2's explicit acknowledgment that isolation is "hardening that must be budgeted, not treated as free."
- Existing partners must be migrated from the shared tier to the isolated tier in batches (see Transition State Bravo in [transition-architectures.md](../06-phase-f-migration-planning/transition-architectures.md)), which extends the migration timeline compared to a theoretical single-cutover approach, in exchange for containing migration risk to smaller, monitored batches rather than one high-stakes event.
- Very low-traffic partners (the median independent restaurant) receive isolation infrastructure sized for a risk profile they individually pose little of — this is accepted as the necessary cost of a uniform, predictable isolation model rather than a partner-tier-dependent one, which the ARB judged not worth the added complexity of differentiated tenant tiers at this stage.

## Governance

Approved by the ARB; VP Engineering, Platform is Responsible, with Restaurant Partnerships (Business) consulted given the direct impact on partner onboarding experience. This decision directly implements Principle 2.

---
*Fictional case study — see [README](../README.md) for full disclaimer.*
