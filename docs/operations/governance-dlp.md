# Governance & DLP

## Managed Environments as the governance baseline

Treat Managed Environments as the default governance layer for every non-dev environment — see
[Environment Strategy](../architecture/environment-strategy.md). It's also what makes
[Power Platform Pipelines](../alm/power-platform-pipelines.md) targets compliant going
forward, not just a separate governance nice-to-have.

## The default environment

The tenant's default environment deserves explicit treatment in any governance conversation:
it can't be deleted, every licensed user is a maker in it, and business-critical solutions
tend to accumulate there by accident. Microsoft's own guidance: rename it (e.g. "Personal
Productivity"), enable
[environment routing](https://learn.microsoft.com/en-us/power-platform/admin/default-environment-routing)
so new makers land in their own developer environments, apply a restrictive DLP policy, and
keep delivered solutions out of it — see
[managing the default environment](https://learn.microsoft.com/en-us/power-platform/guidance/adoption/manage-default-environment).

## Data Loss Prevention (DLP) policies

- **`DGT-OPS-030`**{ #dgt-ops-030 } — Define DLP policies at the environment-group or tenant level appropriate to the customer's
  risk posture — this is a customer/tenant-admin decision, not something DIGITALL unilaterally
  sets, but raise it explicitly during project setup if no policy exists yet.
- Connectors used by [connection references](../customizing/envvars-connectionrefs.md) and
  flows should be checked against the active DLP policy *before* development starts on a
  feature that depends on them — discovering a blocked connector after a flow is built is a
  late, avoidable rework cost.
- Recommend setting the policy's **default group for new connectors to Blocked or
  Non-Business** — newly released connectors are classified into the default group
  automatically, so a permissive default silently opens every future connector. See
  [connector classification](https://learn.microsoft.com/en-us/power-platform/admin/dlp-connector-classification).
- For compliance-sensitive customers, raise
  [tenant isolation / cross-tenant restrictions](https://learn.microsoft.com/en-us/power-platform/admin/cross-tenant-restrictions)
  as a baseline control alongside DLP — DLP governs connectors, tenant isolation governs which
  tenants connections may reach.

## Who can do what

- **`DGT-OPS-040`**{ #dgt-ops-040 } — **Don't customize outside development environments.** Managed Environments enforce this by
  blocking unmanaged customization in target environments — rely on that enforcement rather
  than only on convention.
- **`DGT-OPS-050`**{ #dgt-ops-050 } — Production access for DIGITALL team members should be scoped to what's needed for support
  and deployment troubleshooting, not standing System Administrator access by default — pair
  this with [delegated/service-principal deployment](../alm/power-platform-pipelines.md#deploying-with-a-service-principal)
  so routine deployments don't require personal elevated access at all.

!!! info "Customer-specific policy takes precedence"
    As with everything else in this guideline, a customer's existing governance policy
    overrides the above — see [Scope & Principles](../foundation/scope-and-principles.md).
