# Glossary

| Term | Meaning |
|---|---|
| ALM | Application Lifecycle Management — the process of moving changes from development through test to production. |
| Carrier | A solution prepared for deployment (transport solution) in our Solution Concept model. See [Solution Concept](../architecture/solution-concept.md). |
| Customizer | A maker configuring Dataverse tables, forms, processes, and other low-code assets. |
| Dependent Assembly | A Dataverse mechanism that lets a plugin assembly ship together with its NuGet dependencies in a single package, replacing ILMerge. See [Plugin Packages](../coding/serverside/plugin-packages.md). |
| `dgtp` | The `dgt.power` CLI — DIGITALL's .NET tool for Dataverse code generation, push, maintenance, analysis, and data export/import. |
| Early-bound / Early Binding | Strongly-typed, generated `.cs`/`.ts` classes representing Dataverse tables, in contrast to late-bound `Entity` access. |
| [Managed Environment](https://learn.microsoft.com/en-us/power-platform/admin/managed-environment-overview) | A Dataverse environment with governance features (DLP enforcement, pipelines eligibility, usage insights) enabled by an administrator. |
| Plugin Package | The Dataverse artifact (`.nupkg`) that bundles a plugin assembly with its dependent assemblies; stored in the `PluginPackage` table. |
| Publisher / Prefix | The Dataverse publisher record and its associated customization prefix (e.g. `dgt_`) applied to all schema names. |
| [Solution Checker](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/use-powerapps-checker) | Microsoft's static analysis tool for Dataverse solutions (`pac solution check`), used as a quality gate. |
| Workbench | A temporary, self-contained solution for a unit of work in our Solution Concept model, merged into a Carrier when done. |
