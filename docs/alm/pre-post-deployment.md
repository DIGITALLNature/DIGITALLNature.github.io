# Pre- & Post-Deployment Tasks

Deployment of a Dataverse solution is rarely just "import the `.zip`". This chapter documents
the tasks that must run immediately **before** and **after** that import, and — for the
server-side push — what actually happens inside the tooling, so you know what to expect when
something doesn't look right.

## Pre-deployment

| Task | Tool | Notes |
|---|---|---|
| Regenerate early-bound models | `dgtp codegeneration` | Must run before the dependent build step compiles, not before deployment itself — see [Build Pipeline](build-pipeline.md). Listed here as a reminder it's a pre-deploy dependency, not a deploy-time one. |
| Bump solution version | `dgtp maintenance solution-version` | Targets the *build* environment, before export — see [Versioning](versioning.md). |
| Push plugin/Custom API assemblies | `dgtp push <file>.nupkg --solution <name> --publish` | See [`dgtp push` in depth](#dgtp-push-in-depth) below. |
| Push web resources | `dgtp push <folder> --solution <name> --publish [--delete-obsolete] [--config mapping.json]` | See [web resource push](#web-resource-push) below. |
| Ensure target is a Managed Environment | — | Required for Power Platform Pipelines; see [Power Platform Pipelines](power-platform-pipelines.md). |
| Resolve environment-specific values | Environment variable values, connection reference connections | These are **not** solution-aware in the way schema is — see [Env Variables & Connection References](../customizing/envvars-connectionrefs.md) and [Config & Reference Data Migration](config-data-migration.md). |

## `dgtp push` in depth

`dgtp push` is the tool we use instead of (or alongside) `pac plugin push` for registering
compiled server-side artifacts, because it does more than copy an assembly: it reconciles the
*entire* registration — types, steps, and step images — against what's declared in code via
[registration attributes](../coding/serverside/registration-attributes.md).

```shell
dgtp push ./bin/Release/MyPlugins.nupkg --solution dgt_myproject_core --publish
```

What happens, in order, depends on what `Target` resolves to:

1. **If the target is a `.nupkg`** (a [plugin package](../coding/serverside/plugin-packages.md)):
   the package is unpacked locally, and compared against the `PluginPackage` already registered
   in the target environment (matched by name + version).
   - **Not yet registered** → a new `PluginPackage` is created in the given solution.
   - **Already registered** → the existing `PluginPackage` is updated in place.
2. **If the target is a plain `.dll`** (classic, non-package plugin or workflow assembly): the
   assembly is loaded via reflection (without executing it) and compared the same way against
   the registered `PluginAssembly`.
3. Once the assembly/package is created or updated, `dgtp push`:
   - Skips the assembly entirely if it contains neither plugin nor workflow types (with a
     warning) — this catches accidentally pointing the command at the wrong `.dll`.
   - For **workflow assemblies**: upserts and purges registered workflow activity types, so
     types removed from code are also removed from Dataverse, not just left orphaned.
   - For **plugin assemblies**: upserts and purges plugin types, then — for types carrying
     `[PluginRegistration]` attributes — upserts and purges the corresponding **SDK message
     processing steps** and their **pre/post entity images**, reading the attribute parameters
     described in [Registration Attributes](../coding/serverside/registration-attributes.md).

This means **removing a `[PluginRegistration]` attribute from code and redeploying is enough to
unregister the step** — you don't need a separate cleanup task. It also means a successful
`dgtp push` is the definitive signal that registration matches code; if a step still fires
after you removed its attribute, the push didn't run, rather than something to debug in the
plugin code itself.

`--publish` publishes the affected customizations after the push completes; without it, changes
land but stay in an unpublished state.

### Web resource push

```shell
dgtp push ./WebResources --solution dgt_myproject_core --publish --delete-obsolete
```

When `Target` is a folder, `dgtp push` walks it and creates/updates web resources in the given
solution, matching files to existing web resources by name. `--delete-obsolete` removes
*unmanaged* web resources that exist in the solution but no longer exist in the folder — useful
for keeping a dev environment's web resources in sync with a renamed/removed file, but treat it
as a one-way sync and review its effect on a shared environment before automating it
unattended. `--config` points to a mapping file when the on-disk layout doesn't map 1:1 to web
resource names.

## Post-deployment

| Task | Tool | Notes |
|---|---|---|
| Set/confirm environment variable values | Manual, or an unmanaged "values" solution per environment | Definitions are solution-aware; values, by design, are not. See [Env Variables & Connection References](../customizing/envvars-connectionrefs.md). |
| Re-point connection references | Manual (per-user) or service-principal connection | Connection assignment is user-specific even after deployment — re-confirm after every target environment refresh. |
| Re-import migrated config/reference data | `dgtp import ...` | See [Config & Reference Data Migration](config-data-migration.md). |
| Smoke-test registered steps | — | Confirm the steps you expect to see after a `dgtp push` are actually present and enabled, especially after a `--delete-obsolete` run. |
| Tag the release in Git | CI, automatic | Only after the deployment to that stage's reference environment succeeds — see [Versioning](versioning.md). |

!!! note "Pipelines extensibility as the trigger point"
    If you deploy via [Power Platform Pipelines](power-platform-pipelines.md), the
    pre-/post-deployment tasks above that aren't already baked into the build artifact (e.g.
    config data migration, environment variable values) are good candidates for a **gated
    extension** Power Automate flow triggered by `OnDeploymentRequested` /
    `OnDeploymentStageRunCompleted`, rather than a manual checklist step.
