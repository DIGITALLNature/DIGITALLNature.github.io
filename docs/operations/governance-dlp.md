# Governance & DLP

## Managed Environments as the governance baseline

Treat Managed Environments as the default governance layer for every non-dev environment — see
[Environment Strategy](../architecture/environment-strategy.md). It's also what makes
[Power Platform Pipelines](../alm/power-platform-pipelines.md) targets compliant going
forward, not just a separate governance nice-to-have.

## Data Loss Prevention (DLP) policies

- Define DLP policies at the environment-group or tenant level appropriate to the customer's
  risk posture — this is a customer/tenant-admin decision, not something DIGITALL unilaterally
  sets, but raise it explicitly during project setup if no policy exists yet.
- Connectors used by [connection references](../customizing/envvars-connectionrefs.md) and
  flows should be checked against the active DLP policy *before* development starts on a
  feature that depends on them — discovering a blocked connector after a flow is built is a
  late, avoidable rework cost.

## Who can do what

- **Don't customize outside development environments.** Managed Environments enforce this by
  blocking unmanaged customization in target environments — rely on that enforcement rather
  than only on convention.
- Production access for DIGITALL team members should be scoped to what's needed for support
  and deployment troubleshooting, not standing System Administrator access by default — pair
  this with [delegated/service-principal deployment](../alm/power-platform-pipelines.md#deploying-with-a-service-principal)
  so routine deployments don't require personal elevated access at all.

!!! info "Customer-specific policy takes precedence"
    As with everything else in this guideline, a customer's existing governance policy
    overrides the above — see [Scope & Principles](../foundation/scope-and-principles.md).
