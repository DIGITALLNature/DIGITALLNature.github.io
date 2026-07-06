# Architecture

Decisions in this chapter are made once per project, early, and everything else in this
guideline builds on top of them.

- [Solution Architecture](solution-architecture.md) — how solutions are layered and organized.
- [Publisher & Prefix](publisher-and-prefix.md) — choosing a publisher and customization prefix.
- [Environment Strategy](environment-strategy.md) — how many environments, and what each is for.
- [Deployment Approach](deployment-approach.md) — Power Platform Pipelines vs. Azure
  DevOps/GitHub Actions vs. hybrid.
- [Solution Concept (Carrier/Workbench)](solution-concept.md) — our separated-customizing
  model, documented elsewhere and referenced here for completeness.

## Two checkpoints worth doing at project start

- **Licensing & entitlements.** Verify early that the planned design is covered by the
  customer's licenses:
  [entitlement limits](https://learn.microsoft.com/en-us/power-platform/admin/api-request-limits-allocations)
  (API requests per day, per license) are separate from the runtime service protection limits;
  server-to-server application users don't consume a user license but draw from pooled
  capacity; Managed Environments are an entitlement of standalone/Dynamics 365 licenses and
  are **not** included in the Developer plan. Discovering a licensing gap during UAT is a
  commercial problem, not a technical one.
- **Power Platform Well-Architected assessment.** Consider running
  [Microsoft's Well-Architected assessment](https://learn.microsoft.com/en-us/power-platform/well-architected/)
  for the workload at project start and feeding the findings into the backlog — it's the
  fastest structured way to surface reliability, security, and operations topics the customer
  hasn't thought about yet.
