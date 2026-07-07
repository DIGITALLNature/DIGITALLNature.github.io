# Glossary

| Term | Meaning |
|---|---|
| ALM | Application Lifecycle Management — the process of moving changes from development through test to production. |
| Alternate Key | A unique key on non-primary column(s) — used for upserts and for addressing records from an external system. See [Tables, Columns & Relationships](../customizing/tables-columns-relationships.md). |
| Business Unit / Security Role / Team | The building blocks of the Dataverse [security model](../customizing/security-model.md): data partition, privilege set, and group ownership/sharing. See Microsoft's [Security roles and privileges](https://learn.microsoft.com/en-us/power-platform/admin/security-roles-privileges). |
| Carrier | A solution prepared for deployment (transport solution) in our Solution Concept model. See [Solution Concept](../architecture/solution-concept.md). |
| Customizer | A maker configuring Dataverse tables, forms, processes, and other low-code assets. |
| Definition of Ready / Done (DoR / DoD) | The agreed entry / exit quality criteria for a work item. See [Definition of Ready / Done](../testing/dor-dod.md). |
| Dependent Assembly | A Dataverse mechanism that lets a plugin assembly ship together with its NuGet dependencies in a single package, replacing ILMerge. See [Plugin Packages](../coding/serverside/plugin-packages.md). |
| `dgtp` | The `dgt.power` CLI — DIGITALL's .NET tool for Dataverse code generation, push, maintenance, analysis, and data export/import. |
| Early-bound / Early Binding | Strongly-typed, generated `.cs`/`.ts` classes representing Dataverse tables, in contrast to late-bound `Entity` access. |
| [Managed Environment](https://learn.microsoft.com/en-us/power-platform/admin/managed-environment-overview) | A Dataverse environment with governance features (DLP enforcement, pipelines eligibility, usage insights) enabled by an administrator. |
| Managed Identity | An Azure identity that lets a plugin call another Azure service without a stored secret. See [Custom API & Data Providers](../coding/serverside/custom-api.md#managed-identity) and Microsoft's [Managed identity overview](https://learn.microsoft.com/en-us/power-platform/admin/managed-identity-overview). |
| `pac` (Power Platform CLI) | Microsoft's official Power Platform command-line tool; complements [`dgtp`](../setup/dgtp-cli.md). See Microsoft's [Power Platform CLI](https://learn.microsoft.com/en-us/power-platform/developer/cli/introduction). |
| Patch | A solution that transports only the changes layered on top of a parent solution. See [Solution Architecture](../architecture/solution-architecture.md#managed-vs-unmanaged). |
| Plugin Package | The Dataverse artifact (`.nupkg`) that bundles a plugin assembly with its dependent assemblies; stored in the `PluginPackage` table. |
| Provisioning | In the Carrier/Workbench model, the mechanism that merges changes between Workbenches and Carriers with automatic semantic versioning. See [Solution Concept](../architecture/solution-concept.md). |
| Publisher / Prefix | The Dataverse publisher record and its associated customization prefix (e.g. `dgt_`) applied to all schema names. |
| Rollup / Calculated / Formula Column | Server-computed columns — an aggregate over related rows, an expression evaluated on read, or a Power Fx formula. See [Tables, Columns & Relationships](../customizing/tables-columns-relationships.md). |
| [Solution Checker](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/use-powerapps-checker) | Microsoft's static analysis tool for Dataverse solutions (`pac solution check`), used as a quality gate. |
| Solution Layering / Active Layer | Solutions stack as ordered layers per component; the *active layer* is an unmanaged customization on top of the managed layers. Basis of the [`dgtp analyze`](../reference/dgtp-commands.md#analyze) layer commands. |
| Unified Interface | The modern model-driven app UI that replaced the legacy web client; build for it by default. |
| Workbench | A temporary, self-contained solution for a unit of work in our Solution Concept model, merged into a Carrier when done. |
