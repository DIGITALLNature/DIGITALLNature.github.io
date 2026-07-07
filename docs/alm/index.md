# ALM & Deployment

This chapter is the core of how a change moves from a developer's or customizer's machine into
production. It assumes you have already read [Deployment Approach](../architecture/deployment-approach.md),
which decides *whether* a project uses Power Platform Pipelines, Azure DevOps, GitHub Actions,
or a hybrid — this chapter covers *how* each of those is actually implemented at DIGITALL.

- [Source Control](source-control.md) — repository layout, branching, `.gitignore`, solution
  sync.
- [Versioning](versioning.md) — SemVer, build numbers, solution and assembly version stamping.
- [Build Pipeline](build-pipeline.md) — the canonical build order from source to deployable
  artifacts.
- [Pre- & Post-Deployment Tasks](pre-post-deployment.md) — what `dgtp` and other tooling do
  immediately before and after a deployment.
- [Power Platform Pipelines](power-platform-pipelines.md) — the low-code ALM path.
- [Azure DevOps](azure-devops.md) — the high-code ALM path on Azure DevOps.
- [GitHub Actions](github-actions.md) — the high-code ALM path on GitHub.
- [Config & Reference Data Migration](config-data-migration.md) — moving non-solution-aware
  data between environments.

!!! note "Two ALM tracks, used in combination"
    In practice, most projects run **both** tracks at once: the platform's own pipelines for
    the Dataverse stream, and Azure DevOps/GitHub Actions for everything in the wider system
    boundary (interfaces, Azure resources, data platform). See
    [Deployment Approach](../architecture/deployment-approach.md) for the decision tree.
