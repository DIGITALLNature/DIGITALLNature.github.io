# Environment Strategy

## Baseline environment set

| Environment | Purpose | Managed Environment? |
|---|---|---|
| Development (per developer, or shared) | Active customizing/coding work | Recommended, required if it's a pipelines source environment |
| Build | Pristine environment a CI pipeline targets for codegen, version stamping, and export — kept free of manual changes | Recommended |
| Test | Functional/integration testing | Required for pipelines targets |
| UAT | Customer/business acceptance | Required for pipelines targets |
| Production | Live system | Required for pipelines targets |

**`DGT-ARC-080`**{ #dgt-arc-080 } — Four target environments (dev → test → UAT → prod) is the common baseline; three is the
minimum workable setup. Scale up (e.g. a dedicated performance/load environment) based on
project size, not as a default.

Before finalizing the set, agree uptime and recovery targets (RTO/RPO) for the workload with
the customer — they determine whether the baseline is enough (see
[Power Platform Well-Architected, Reliability](https://learn.microsoft.com/en-us/power-platform/well-architected/reliability/))
and they feed directly into the backup and restore approach below.

## Managed Environments are the default, not an opt-in

**`DGT-ARC-090`**{ #dgt-arc-090 } — Enable **[Managed Environments](https://learn.microsoft.com/en-us/power-platform/admin/managed-environment-overview)** on every environment used as a Power Platform Pipelines target
as part of initial environment setup — not as a reaction to a later governance push. See the
warning in [Power Platform Pipelines](../alm/power-platform-pipelines.md) about Microsoft's
February 2026 automatic-enablement change: environments that aren't already managed by then get
enabled for you, so getting ahead of it is simpler than discovering it happened.

## Per-developer environments

**`DGT-ARC-100`**{ #dgt-arc-100 } — Use one development environment **per developer** once the
team is larger than one or two people; don't share a single dev environment. A shared dev
environment turns into a source of unreviewable, conflicting changes that no pipeline step can
reconcile — individual environments combined with regular `pac solution sync` (see
[Source Control](../alm/source-control.md)) keep changes attributable and mergeable through
normal code review.

## Backup & restore

Decide the backup and restore approach as part of the environment strategy, not when the first
incident happens. The platform behaviors worth planning around (see
[Microsoft's backup/restore documentation](https://learn.microsoft.com/en-us/power-platform/admin/backup-restore-environments)):

- System backups are continuous, but **retention differs**: production environments (as
  Managed Environments) can retain up to 28 days — sandboxes only 7.
- There is **no restore directly onto production**: the path is switching production to
  sandbox type, restoring, and switching back — which also drops it to 7-day retention while
  it is a sandbox. Rehearse this once before you need it.
- A restore is not a clean rewind: solution cloud flows come back **turned off**, connection
  references need their connections re-assigned, and canvas app IDs change. Put these steps in
  the project runbook.
- Take a **manual backup before every major deployment** — it's the cheapest rollback option
  you'll ever configure.

## Backing up unmanaged solutions

Even with a full ALM pipeline in place, it's good practice to periodically export and commit
the unpacked, unmanaged solution from the active development environment as a backup / change
tracking measure — independent of whatever the pipeline does for actual deployment. See
[Source Control](../alm/source-control.md).