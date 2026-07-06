# Checklists

## New project onboarding

- [ ] [Publisher & prefix](../architecture/publisher-and-prefix.md) decided and documented.
- [ ] [Environment strategy](../architecture/environment-strategy.md) decided; dev/build/test
      environments provisioned with Managed Environments enabled.
- [ ] [Deployment approach](../architecture/deployment-approach.md) decided.
- [ ] Repository created with the layout in [Source Control](../alm/source-control.md);
      `.gitattributes`/`.gitignore` in place.
- [ ] CI pipeline scaffolded per [Build Pipeline](../alm/build-pipeline.md), including the
      Solution Checker gate.
- [ ] `dgtp` profiles created for build/CI (service principal, not a personal account).
- [ ] [Naming conventions](../customizing/naming-conventions.md) communicated to all
      customizers on the project.

## Pull request review

- [ ] Naming follows [Naming Conventions](../customizing/naming-conventions.md).
- [ ] New/changed plugin steps use
      [registration attributes](../coding/serverside/registration-attributes.md), not manual
      Plugin Registration Tool changes.
- [ ] No `ILMerge`/`ILRepack` introduced — see [Plugin Packages](../coding/serverside/plugin-packages.md).
- [ ] Server-side changes have corresponding
      [unit tests](../testing/unit-testing-serverside.md) using `Digitall.Dataverse.Testing`.
- [ ] Generated model output (`.cs`/`.ts`) is not committed — see
      [Source Control](../alm/source-control.md#gitignore).
- [ ] Any deviation from this guideline is documented per
      [Scope & Principles](../foundation/scope-and-principles.md), ideally with its
      [rule ID](../foundation/rule-notation.md).

## Pre-deployment (production)

- [ ] Solution version bumped on the build environment, not hand-edited — see
      [Versioning](../alm/versioning.md).
- [ ] Solution Checker passed with no unresolved high-severity findings.
- [ ] [Environment variable values](../customizing/envvars-connectionrefs.md) confirmed for
      the target environment.
- [ ] [Connection references](../customizing/envvars-connectionrefs.md) re-confirmed for the
      target environment.
- [ ] [Config/reference data](../alm/config-data-migration.md) migration step run, if the
      release includes changes to non-solution-aware configuration.
- [ ] Deployment approval obtained, per the project's
      [Power Platform Pipelines](../alm/power-platform-pipelines.md#deploying-with-a-service-principal)
      or Azure DevOps/GitHub Actions environment gate configuration.

## Go-live / cutover

Beyond the regular pre-deployment checklist, a first production go-live needs (see
[Microsoft's go-live checklist](https://learn.microsoft.com/en-us/dynamics365/guidance/implementation-guide/prepare-go-live-checklist)):

- [ ] UAT signed off; [performance test](../testing/index.md#performance-testing-before-go-live)
      under realistic load passed.
- [ ] Data migration rehearsed with a dry run, including throughput measurement — factor
      [service protection limits](../coding/cloud-integration.md#dataverse-service-protection-limits)
      into the cutover window.
- [ ] Version parity confirmed: production receives exactly the solution/app versions that
      passed test/UAT.
- [ ] Manual environment backup taken immediately before cutover; rollback decision point and
      criteria documented.
- [ ] Go/no-go decision taken and documented with the customer.
- [ ] Hypercare plan active: support responsibilities, escalation path, and coverage agreed;
      knowledge transfer from project team to support done.
- [ ] First days after go-live: watch API-limit errors, storage consumption, and flow failure
      rates (see [Monitoring](../operations/monitoring.md)).

## Post-deployment

- [ ] Smoke-test the registered plugin steps and Custom APIs expected from this release.
- [ ] Confirm flows using affected connection references run successfully.
- [ ] Release tagged in Git — see [Versioning](../alm/versioning.md).
- [ ] Release documentation / deployment notes updated per
      [Definition of Done](../testing/dor-dod.md).
