# Environment Strategy

## Baseline environment set

| Environment | Purpose | Managed Environment? |
|---|---|---|
| Development (per developer, or shared) | Active customizing/coding work | Recommended, required if it's a pipelines source environment |
| Build | Pristine environment a CI pipeline targets for codegen, version stamping, and export — kept free of manual changes | Recommended |
| Test | Functional/integration testing | Required for pipelines targets |
| UAT | Customer/business acceptance | Required for pipelines targets |
| Production | Live system | Required for pipelines targets |

Four target environments (dev → test → UAT → prod) is the common baseline; three is the
minimum workable setup. Scale up (e.g. a dedicated performance/load environment) based on
project size, not as a default.

## Managed Environments are the default, not an opt-in

Enable **[Managed Environments](https://learn.microsoft.com/en-us/power-platform/admin/managed-environment-overview)** on every environment used as a Power Platform Pipelines target
as part of initial environment setup — not as a reaction to a later governance push. See the
warning in [Power Platform Pipelines](../alm/power-platform-pipelines.md) about Microsoft's
February 2026 automatic-enablement change: environments that aren't already managed by then get
enabled for you, so getting ahead of it is simpler than discovering it happened.

## Per-developer environments

Prefer one development environment per developer over a single shared dev environment once a
team is larger than one or two people. A shared dev environment turns into a source of
unreviewable, conflicting changes that no pipeline step can reconcile — individual environments
combined with regular `pac solution sync` (see [Source Control](../alm/source-control.md)) keep
changes attributable and mergeable through normal code review.

## Backing up unmanaged solutions

Even with a full ALM pipeline in place, it's good practice to periodically export and commit
the unpacked, unmanaged solution from the active development environment as a backup / change
tracking measure — independent of whatever the pipeline does for actual deployment. See
[Source Control](../alm/source-control.md).