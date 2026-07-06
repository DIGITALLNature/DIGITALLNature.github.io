# Rules Index

Referenceable rules across this guideline. Each rule has a short, stable ID —
`DGT-<AREA>-<NUMBER>` — assigned ascending per area and **never reused**: if a rule is removed,
its ID is retired, not recycled, so references from project documentation stay unambiguous. See
[Rule Notation](../foundation/rule-notation.md) for the scheme.

Cite a rule by its ID (e.g. *"deviates from `DGT-SRV-010`"*); the links below jump straight to
the rule where it is stated. Each row also has its own anchor
(e.g. `reference/rules.md#dgt-srv-010`), so you can point a colleague at one specific rule
directly in this list — they land on it and see the surrounding rules for context.

## Foundation (`FND`)

| Rule | Statement | Defined in |
|---|---|---|
| [`DGT-FND-010`](#dgt-fnd-010){ #dgt-fnd-010 } | Deviations from this guideline are permitted but must be documented and justified in the project's architectural documentation. | [Scope & Principles](../foundation/scope-and-principles.md#dgt-fnd-010) |
| [`DGT-FND-020`](#dgt-fnd-020){ #dgt-fnd-020 } | If the customer has a defined standard, that standard takes precedence over this guideline. | [Scope & Principles](../foundation/scope-and-principles.md#dgt-fnd-020) |
| [`DGT-FND-030`](#dgt-fnd-030){ #dgt-fnd-030 } | Every project maintains an ADR log in its main repository (`docs/decisions/`, one Markdown file per decision). | [Architecture Decision Records](../foundation/architecture-decision-records.md#dgt-fnd-030) |
| [`DGT-FND-040`](#dgt-fnd-040){ #dgt-fnd-040 } | Fundamental decisions for or against a guideline rule require an ADR in MADR format. | [Architecture Decision Records](../foundation/architecture-decision-records.md#dgt-fnd-040) |
| [`DGT-FND-050`](#dgt-fnd-050){ #dgt-fnd-050 } | A deviation ADR references the affected rule ID machine-readably; the ADR lifecycle is maintained. | [Architecture Decision Records](../foundation/architecture-decision-records.md#dgt-fnd-050) |
| [`DGT-FND-060`](#dgt-fnd-060){ #dgt-fnd-060 } | ADRs pass through PR review and are part of the Definition of Done's documentation item. | [Architecture Decision Records](../foundation/architecture-decision-records.md#dgt-fnd-060) |

## Architecture (`ARC`)

| Rule | Statement | Defined in |
|---|---|---|
| [`DGT-ARC-010`](#dgt-arc-010){ #dgt-arc-010 } | Use the DIGITALL publisher/`dgt_` prefix for productized components; a customer-specific publisher/prefix for customer projects. | [Publisher & Prefix](../architecture/publisher-and-prefix.md#dgt-arc-010) |
| [`DGT-ARC-020`](#dgt-arc-020){ #dgt-arc-020 } | Once a prefix is chosen for a project, it does not change. | [Publisher & Prefix](../architecture/publisher-and-prefix.md#dgt-arc-020) |
| [`DGT-ARC-030`](#dgt-arc-030){ #dgt-arc-030 } | Follow Microsoft's customization guidance by default; deviate only with a documented, concrete reason. | [Publisher & Prefix](../architecture/publisher-and-prefix.md#dgt-arc-030) |
| [`DGT-ARC-040`](#dgt-arc-040){ #dgt-arc-040 } | Prefer an XrmToolBox plugin over installing a special-purpose third-party solution. | [Publisher & Prefix](../architecture/publisher-and-prefix.md#dgt-arc-040) |
| [`DGT-ARC-050`](#dgt-arc-050){ #dgt-arc-050 } | Enable Managed Environments on every Power Platform Pipelines target environment from initial setup. | [Environment Strategy](../architecture/environment-strategy.md#dgt-arc-050) |
| [`DGT-ARC-060`](#dgt-arc-060){ #dgt-arc-060 } | Four target environments (dev → test → UAT → prod) is the common baseline; three is the minimum. | [Environment Strategy](../architecture/environment-strategy.md#dgt-arc-060) |
| [`DGT-ARC-070`](#dgt-arc-070){ #dgt-arc-070 } | Decide the deployment approach (Pipelines / Azure DevOps / GitHub Actions / hybrid) once, early in the project. | [Deployment Approach](../architecture/deployment-approach.md#dgt-arc-070) |
| [`DGT-ARC-080`](#dgt-arc-080){ #dgt-arc-080 } | Organize Dataverse solutions by component type (core/server/client/apps/flows), with a strict, documented dependency direction. | [Solution Architecture](../architecture/solution-architecture.md#dgt-arc-080) |
| [`DGT-ARC-090`](#dgt-arc-090){ #dgt-arc-090 } | Development environments hold the unmanaged solution; every other environment always receives it managed. | [Solution Architecture](../architecture/solution-architecture.md#dgt-arc-090) |
| [`DGT-ARC-100`](#dgt-arc-100){ #dgt-arc-100 } | A component lives in exactly one solution; tables are added segmented, never "Add all assets". | [Solution Architecture](../architecture/solution-architecture.md#dgt-arc-100) |

## Customizing (`CUS`)

| Rule | Statement | Defined in |
|---|---|---|
| [`DGT-CUS-010`](#dgt-cus-010){ #dgt-cus-010 } | All schema names are snake_case, lowercase, `[publisher]_[name][_type-suffix]`. | [Naming Conventions](../customizing/naming-conventions.md#dgt-cus-010) |
| [`DGT-CUS-020`](#dgt-cus-020){ #dgt-cus-020 } | Set `IsCustomizable = false` for security roles and rollup/calculated fields. | [Naming Conventions](../customizing/naming-conventions.md#dgt-cus-020) |
| [`DGT-CUS-030`](#dgt-cus-030){ #dgt-cus-030 } | Check whether a standard table already fits before adding a custom one. | [Tables, Columns & Relationships](../customizing/tables-columns-relationships.md#dgt-cus-030) |
| [`DGT-CUS-040`](#dgt-cus-040){ #dgt-cus-040 } | Decide ownership, table type, and primary column deliberately — they are fixed at table creation. | [Tables, Columns & Relationships](../customizing/tables-columns-relationships.md#dgt-cus-040) |
| [`DGT-CUS-050`](#dgt-cus-050){ #dgt-cus-050 } | Pick the narrowest correct column data type. | [Tables, Columns & Relationships](../customizing/tables-columns-relationships.md#dgt-cus-050) |
| [`DGT-CUS-060`](#dgt-cus-060){ #dgt-cus-060 } | Don't store what you can derive — prefer rollup/calculated/formula columns over a plugin-written plain column. | [Tables, Columns & Relationships](../customizing/tables-columns-relationships.md#dgt-cus-060) |
| [`DGT-CUS-070`](#dgt-cus-070){ #dgt-cus-070 } | Default relationships to referential cascade; use parental only where the child has no meaning without the parent. | [Tables, Columns & Relationships](../customizing/tables-columns-relationships.md#dgt-cus-070) |
| [`DGT-CUS-080`](#dgt-cus-080){ #dgt-cus-080 } | One main form per persona, not one form with everything hidden per role. | [Forms, Views & BPF](../customizing/forms-views-bpf.md#dgt-cus-080) |
| [`DGT-CUS-090`](#dgt-cus-090){ #dgt-cus-090 } | Logic belongs in code once non-trivial; register only `OnLoad` and wire the rest in TypeScript. | [Forms, Views & BPF](../customizing/forms-views-bpf.md#dgt-cus-090) |
| [`DGT-CUS-100`](#dgt-cus-100){ #dgt-cus-100 } | Keep view columns lean — every column is a query column. | [Forms, Views & BPF](../customizing/forms-views-bpf.md#dgt-cus-100) |
| [`DGT-CUS-110`](#dgt-cus-110){ #dgt-cus-110 } | A BPF models a cross-table process as few, meaningful ordered stages. | [Forms, Views & BPF](../customizing/forms-views-bpf.md#dgt-cus-110) |
| [`DGT-CUS-120`](#dgt-cus-120){ #dgt-cus-120 } | Build commands in the modern Command Bar Designer, not by hand-editing ribbon XML. | [Command Bar](../customizing/command-bar.md#dgt-cus-120) |
| [`DGT-CUS-130`](#dgt-cus-130){ #dgt-cus-130 } | Don't hide or override standard command-bar buttons globally without a specific, documented reason. | [Command Bar](../customizing/command-bar.md#dgt-cus-130) |
| [`DGT-CUS-140`](#dgt-cus-140){ #dgt-cus-140 } | Brand per app using the modern model-driven app theme, kept inside the solution. | [Themes & Icons](../customizing/themes-and-icons.md#dgt-cus-140) |
| [`DGT-CUS-150`](#dgt-cus-150){ #dgt-cus-150 } | Use SVG web resources for table and command-bar icons. | [Themes & Icons](../customizing/themes-and-icons.md#dgt-cus-150) |
| [`DGT-CUS-160`](#dgt-cus-160){ #dgt-cus-160 } | Apply least privilege — grant only what a persona needs, at the smallest working ownership depth. | [Security Model](../customizing/security-model.md#dgt-cus-160) |
| [`DGT-CUS-170`](#dgt-cus-170){ #dgt-cus-170 } | No standing System Administrator for application/service accounts. | [Security Model](../customizing/security-model.md#dgt-cus-170) |
| [`DGT-CUS-180`](#dgt-cus-180){ #dgt-cus-180 } | Test with a real security role, not as System Administrator. | [Security Model](../customizing/security-model.md#dgt-cus-180) |
| [`DGT-CUS-190`](#dgt-cus-190){ #dgt-cus-190 } | Assign unattended/production flow connections via an application user / service principal. | [Env Variables & Connection References](../customizing/envvars-connectionrefs.md#dgt-cus-190) |
| [`DGT-CUS-200`](#dgt-cus-200){ #dgt-cus-200 } | In development, create the environment variable definition only (no value) and deploy that. | [Env Variables & Connection References](../customizing/envvars-connectionrefs.md#dgt-cus-200) |
| [`DGT-CUS-210`](#dgt-cus-210){ #dgt-cus-210 } | High-volume telemetry-style data goes into an elastic table with a TTL column, never a standard table. | [Tables, Columns & Relationships](../customizing/tables-columns-relationships.md#dgt-cus-210) |
| [`DGT-CUS-220`](#dgt-cus-220){ #dgt-cus-220 } | Never implement security through UI visibility — use column security and privileges. | [Security Model](../customizing/security-model.md#dgt-cus-220) |
| [`DGT-CUS-230`](#dgt-cus-230){ #dgt-cus-230 } | Security roles are solution components and get deployed — never rebuilt manually in test/prod. | [Security Model](../customizing/security-model.md#dgt-cus-230) |

## Server-side (`SRV`)

| Rule | Statement | Defined in |
|---|---|---|
| [`DGT-SRV-010`](#dgt-srv-010){ #dgt-srv-010 } | Plugin/Custom API projects use the plugin **package** (dependent-assembly `.nupkg`) format; ILMerge is not allowed. | [Plugin Packages](../coding/serverside/plugin-packages.md#dgt-srv-010) |
| [`DGT-SRV-020`](#dgt-srv-020){ #dgt-srv-020 } | Plugin and Custom API assemblies target **`net462`**. | [Project Setup](../coding/serverside/project-setup.md#dgt-srv-020) |
| [`DGT-SRV-030`](#dgt-srv-030){ #dgt-srv-030 } | Use the **SDK-style** project format, not the legacy non-SDK `.csproj`. | [Project Setup](../coding/serverside/project-setup.md#dgt-srv-030) |
| [`DGT-SRV-040`](#dgt-srv-040){ #dgt-srv-040 } | Reference `Microsoft.CrmSdk.CoreAssemblies` with `PrivateAssets="all"` — not `Dataverse.Client`. | [Project Setup](../coding/serverside/project-setup.md#dgt-srv-040) |
| [`DGT-SRV-050`](#dgt-srv-050){ #dgt-srv-050 } | Plugin packages are **not** signed (signing is only for classic single-assembly). | [Project Setup](../coding/serverside/project-setup.md#dgt-srv-050) |
| [`DGT-SRV-060`](#dgt-srv-060){ #dgt-srv-060 } | One plugin/Custom API/workflow group per assembly, versioned independently. | [Project Setup](../coding/serverside/project-setup.md#dgt-srv-060) |
| [`DGT-SRV-070`](#dgt-srv-070){ #dgt-srv-070 } | Inherit `Executor` and return a meaningful `ExecutionResult` (`Ok`/`Failure`/`Skipped`), not always `Ok`. | [DIGITALL Assembly](../coding/serverside/digitall-assembly.md#dgt-srv-070) |
| [`DGT-SRV-080`](#dgt-srv-080){ #dgt-srv-080 } | Prefer `SecuredOrganizationService`/`ElevatedOrganizationService` over calling `OrganizationService(...)` directly. | [DIGITALL Assembly](../coding/serverside/digitall-assembly.md#dgt-srv-080) |
| [`DGT-SRV-090`](#dgt-srv-090){ #dgt-srv-090 } | Register steps declaratively via `[PluginRegistration]` — code is the source of truth. | [Registration Attributes](../coding/serverside/registration-attributes.md#dgt-srv-090) |
| [`DGT-SRV-100`](#dgt-srv-100){ #dgt-srv-100 } | Use `FilterAttributes` on the registration rather than re-checking inside `Execute()`. | [Patterns & Pitfalls](../coding/serverside/patterns.md#dgt-srv-100) |
| [`DGT-SRV-110`](#dgt-srv-110){ #dgt-srv-110 } | Prefer Custom APIs over classic (un)bound Actions for new server-side operations. | [Custom API & Data Providers](../coding/serverside/custom-api.md#dgt-srv-110) |
| [`DGT-SRV-120`](#dgt-srv-120){ #dgt-srv-120 } | Keep plugins stateless — no mutable `static` state. | [Patterns & Pitfalls](../coding/serverside/patterns.md#dgt-srv-120) |
| [`DGT-SRV-130`](#dgt-srv-130){ #dgt-srv-130 } | Use `InvalidPluginExecutionException` (`Status = Succeeded`) only for user-facing validation messages. | [Patterns & Pitfalls](../coding/serverside/patterns.md#dgt-srv-130) |
| [`DGT-SRV-140`](#dgt-srv-140){ #dgt-srv-140 } | Don't do slow/external work synchronously in a plugin (2-minute limit); prefer managed identity over stored secrets. | [Cloud & Integration](../coding/cloud-integration.md#dgt-srv-140) |
| [`DGT-SRV-150`](#dgt-srv-150){ #dgt-srv-150 } | Use generated early-bound models (`dgtp codegeneration`); generated code is not committed. | [Early-Bound Models](../coding/serverside/early-binding.md#dgt-srv-150) |
| [`DGT-SRV-160`](#dgt-srv-160){ #dgt-srv-160 } | Keep `SuppressNullableSupport: true` consistent across every project sharing a generated model. | [Patterns & Pitfalls](../coding/serverside/patterns.md#dgt-srv-160) |
| [`DGT-SRV-170`](#dgt-srv-170){ #dgt-srv-170 } | No `ExecuteMultipleRequest`/`ExecuteTransactionRequest` inside plugins. | [Patterns & Pitfalls](../coding/serverside/patterns.md#dgt-srv-170) |
| [`DGT-SRV-180`](#dgt-srv-180){ #dgt-srv-180 } | No custom threading (`Parallel.*`, `Task.Run`) inside plugins. | [Patterns & Pitfalls](../coding/serverside/patterns.md#dgt-srv-180) |
| [`DGT-SRV-190`](#dgt-srv-190){ #dgt-srv-190 } | Synchronous steps on `Retrieve`/`RetrieveMultiple` only in justified exceptions. | [Patterns & Pitfalls](../coding/serverside/patterns.md#dgt-srv-190) |
| [`DGT-SRV-200`](#dgt-srv-200){ #dgt-srv-200 } | Don't use `context.Depth` as loop control — use filtering attributes and precise registration. | [Patterns & Pitfalls](../coding/serverside/patterns.md#dgt-srv-200) |
| [`DGT-SRV-210`](#dgt-srv-210){ #dgt-srv-210 } | Every query states an explicit `ColumnSet` — never `AllColumns`. | [Patterns & Pitfalls](../coding/serverside/patterns.md#dgt-srv-210) |
| [`DGT-SRV-220`](#dgt-srv-220){ #dgt-srv-220 } | A plugin package/assembly belongs to exactly one solution. | [Plugin Packages](../coding/serverside/plugin-packages.md#dgt-srv-220) |

## Client-side (`CLI`)

| Rule | Statement | Defined in |
|---|---|---|
| [`DGT-CLI-010`](#dgt-cli-010){ #dgt-cli-010 } | Form and command-bar scripts are written in TypeScript and bundled with webpack (the `webresources-template`). | [TypeScript Web Resources](../coding/clientside/typescript-webresources.md#dgt-cli-010) |
| [`DGT-CLI-020`](#dgt-cli-020){ #dgt-cli-020 } | Register only the form's `OnLoad` event in the designer; wire all other handlers in code. | [TypeScript Web Resources](../coding/clientside/typescript-webresources.md#dgt-cli-020) |
| [`DGT-CLI-030`](#dgt-cli-030){ #dgt-cli-030 } | Only `*.form.ts` / `*.ribbon.ts` become bundles; shared code lives in plain imported modules. | [TypeScript Web Resources](../coding/clientside/typescript-webresources.md#dgt-cli-030) |
| [`DGT-CLI-040`](#dgt-cli-040){ #dgt-cli-040 } | Generate typed models with `dgtp codegeneration` and type the form context against them. | [TypeScript Web Resources](../coding/clientside/typescript-webresources.md#dgt-cli-040) |
| [`DGT-CLI-050`](#dgt-cli-050){ #dgt-cli-050 } | Biome owns formatting and linting for web resources; don't hand-tune style. | [TypeScript Web Resources](../coding/clientside/typescript-webresources.md#dgt-cli-050) |
| [`DGT-CLI-060`](#dgt-cli-060){ #dgt-cli-060 } | Use `pac` for PCF scaffolding rather than copying an older project's structure by hand. | [PCF Controls](../coding/clientside/pcf.md#dgt-cli-060) |
| [`DGT-CLI-070`](#dgt-cli-070){ #dgt-cli-070 } | No deprecated client APIs: no `Xrm.Page`, no `parent.Xrm`, no platform scripts, no OData v2. | [TypeScript Web Resources](../coding/clientside/typescript-webresources.md#dgt-cli-070) |

## Cloud & Integration (`INT`)

| Rule | Statement | Defined in |
|---|---|---|
| [`DGT-INT-010`](#dgt-int-010){ #dgt-int-010 } | Every integration handles HTTP 429 / `Retry-After` (service protection limits); use `ServiceClient` or equivalent backoff. | [Cloud & Integration](../coding/cloud-integration.md#dgt-int-010) |
| [`DGT-INT-020`](#dgt-int-020){ #dgt-int-020 } | External calls from plugins set an explicit timeout and `KeepAlive = false`. | [Cloud & Integration](../coding/cloud-integration.md#dgt-int-020) |
| [`DGT-INT-030`](#dgt-int-030){ #dgt-int-030 } | Never use the Dataverse Search API for bulk queries. | [Cloud & Integration](../coding/cloud-integration.md#dgt-int-030) |

## Power Automate Flows (`FLW`)

| Rule | Statement | Defined in |
|---|---|---|
| [`DGT-FLW-010`](#dgt-flw-010){ #dgt-flw-010 } | Create flows solution-aware, never as standalone "My flows" artifacts. | [Power Automate Flows](../coding/flows.md#dgt-flw-010) |
| [`DGT-FLW-020`](#dgt-flw-020){ #dgt-flw-020 } | No production flow without error handling (scope-based try/catch and failure notification). | [Power Automate Flows](../coding/flows.md#dgt-flw-020) |
| [`DGT-FLW-030`](#dgt-flw-030){ #dgt-flw-030 } | Filter in the trigger (trigger conditions, column/row filters), not with a first-action condition. | [Power Automate Flows](../coding/flows.md#dgt-flw-030) |
| [`DGT-FLW-040`](#dgt-flw-040){ #dgt-flw-040 } | Treat concurrency and pagination settings as code — variables aren't thread-safe under concurrency; raise pagination limits explicitly. | [Power Automate Flows](../coding/flows.md#dgt-flw-040) |

## Testing & Quality (`TST`)

| Rule | Statement | Defined in |
|---|---|---|
| [`DGT-TST-010`](#dgt-tst-010){ #dgt-tst-010 } | Use `Digitall.Dataverse.Testing` (in-memory) for plugin/Custom API unit tests, not a live environment. | [Server-side Unit Testing](../testing/unit-testing-serverside.md#dgt-tst-010) |
| [`DGT-TST-020`](#dgt-tst-020){ #dgt-tst-020 } | Run the Power Apps Solution Checker against every packed solution as a build-pipeline quality gate. | [Solution Checker](../testing/solution-checker.md#dgt-tst-020) |
| [`DGT-TST-030`](#dgt-tst-030){ #dgt-tst-030 } | Unit-test TypeScript web resource logic with Jest, mocking `Xrm` via `xrm-mock`. | [Client-side Testing](../testing/clientside-testing.md#dgt-tst-030) |
| [`DGT-TST-040`](#dgt-tst-040){ #dgt-tst-040 } | A user story must meet the Definition of Ready before entering a sprint. | [Definition of Ready / Done](../testing/dor-dod.md#dgt-tst-040) |
| [`DGT-TST-050`](#dgt-tst-050){ #dgt-tst-050 } | A user story must meet the Definition of Done, including passing tests and updated documentation, to be closed. | [Definition of Ready / Done](../testing/dor-dod.md#dgt-tst-050) |
| [`DGT-TST-060`](#dgt-tst-060){ #dgt-tst-060 } | Activate each release wave's Early Access in a sandbox and run a regression before the wave reaches production. | [Testing & Quality](../testing/index.md#dgt-tst-060) |

## ALM & Deployment (`ALM`)

| Rule | Statement | Defined in |
|---|---|---|
| [`DGT-ALM-010`](#dgt-alm-010){ #dgt-alm-010 } | Regularly commit the unpacked Dataverse solution to source control, regardless of deployment approach. | [Source Control](../alm/source-control.md#dgt-alm-010) |
| [`DGT-ALM-020`](#dgt-alm-020){ #dgt-alm-020 } | Enforce a single line-ending convention across the repository via `.gitattributes`. | [Source Control](../alm/source-control.md#dgt-alm-020) |
| [`DGT-ALM-030`](#dgt-alm-030){ #dgt-alm-030 } | Bump the solution version via `dgtp maintenance solution-version`, not by hand-editing `solution.xml`. | [Versioning](../alm/versioning.md#dgt-alm-030) |
| [`DGT-ALM-040`](#dgt-alm-040){ #dgt-alm-040 } | The plugin package/assembly version must increase on every build that gets pushed. | [Versioning](../alm/versioning.md#dgt-alm-040) |
| [`DGT-ALM-050`](#dgt-alm-050){ #dgt-alm-050 } | Follow the canonical build-pipeline step order, independent of the CI orchestrator. | [Build Pipeline](../alm/build-pipeline.md#dgt-alm-050) |
| [`DGT-ALM-060`](#dgt-alm-060){ #dgt-alm-060 } | Use YAML pipelines checked into the repository, not classic editor-defined pipelines. | [Azure DevOps](../alm/azure-devops.md#dgt-alm-060) |
| [`DGT-ALM-070`](#dgt-alm-070){ #dgt-alm-070 } | Use the matching `dgtp export`/`dgtp import` command pair for non-solution-aware config objects. | [Config & Reference Data Migration](../alm/config-data-migration.md#dgt-alm-070) |
| [`DGT-ALM-080`](#dgt-alm-080){ #dgt-alm-080 } | Use token-based (service principal) `dgtp` profiles for CI — never personal credentials. | [Build Pipeline](../alm/build-pipeline.md#dgt-alm-080) |
| [`DGT-ALM-090`](#dgt-alm-090){ #dgt-alm-090 } | Import managed solutions as Upgrade (or stage for upgrade), not Update. | [Pre- & Post-Deployment Tasks](../alm/pre-post-deployment.md#dgt-alm-090) |
| [`DGT-ALM-100`](#dgt-alm-100){ #dgt-alm-100 } | Provide connection references and environment-variable values via a deployment settings file per target. | [Pre- & Post-Deployment Tasks](../alm/pre-post-deployment.md#dgt-alm-100) |

## Operations (`OPS`)

| Rule | Statement | Defined in |
|---|---|---|
| [`DGT-OPS-010`](#dgt-ops-010){ #dgt-ops-010 } | Define DLP policies at the environment-group or tenant level and check connectors against them before development starts. | [Governance & DLP](../operations/governance-dlp.md#dgt-ops-010) |
| [`DGT-OPS-020`](#dgt-ops-020){ #dgt-ops-020 } | Don't customize outside development environments — rely on Managed Environments to enforce this. | [Governance & DLP](../operations/governance-dlp.md#dgt-ops-020) |
| [`DGT-OPS-030`](#dgt-ops-030){ #dgt-ops-030 } | Scope production access to what's needed for support/troubleshooting, not standing System Administrator. | [Governance & DLP](../operations/governance-dlp.md#dgt-ops-030) |
| [`DGT-OPS-040`](#dgt-ops-040){ #dgt-ops-040 } | Enable Application Insights integration for every production environment. | [Monitoring & Telemetry](../operations/monitoring.md#dgt-ops-040) |
