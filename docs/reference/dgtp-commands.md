# dgtp Command Reference

This page lists every `dgtp` command grouped by branch, with the options that apply across an
entire branch noted once rather than repeated per command. For installation and profile setup,
see [dgtp CLI](../setup/dgtp-cli.md). Run `dgtp` or `dgtp <command> --help` for the
authoritative, always-current list — this page is a navigable summary, not a substitute for
`--help` on a specific flag you're unsure about.

## `profile`

| Command | Description |
|---|---|
| `dgtp profile list` | List profiles |
| `dgtp profile create` | Create a new profile |
| `dgtp profile select <name>` | Select a profile as active |
| `dgtp profile delete <name>` | Delete a profile |
| `dgtp profile purge` | Delete all profiles |

See [dgtp CLI](../setup/dgtp-cli.md#profiles).

## `codegeneration` (alias `cg`)

```text
dgtp codegeneration [TargetDirectory] [-f|--folder <name>] [-c|--config <path>]
```

Generates `.cs`, `.ts`, and `metadata.xml` model files. See
[Early-Bound Models](../coding/serverside/early-binding.md) (server-side) and
[TS Model Generation](../coding/clientside/model-generation.md) (client-side) for config
shapes and the full parameter list:

??? note "Full config parameter reference"
    **General — required:** `Entities` (`string[]`), `Actions` (`string[]`), `CustomAPIs`
    (`string[]`).

    **General — additional content:** `AdditionalSdkMessages` (`string[]`, default `[]`),
    `GlobalOptionSets` (`string[]`, default `[]`).

    **General — filters:** `EntityMask` (`string`, default `"*"`), `SdkMessageFilters`
    (`string[]`, default `[]`), `Solutions` (`string[]`, default `[]`), `SuppressOptions`
    (`bool`, default `false`), `SuppressSdkMessages` (`bool`, default `false`).

    **General — output:** `Hints` (`bool`, default `true`), `SuppressDotNet` (`bool`, default
    `false`), `SuppressMetaData` (`bool`, default `false`), `SuppressTypeScript` (`bool`,
    default `false`), `UseBaseLanguage` (`bool`, default `false`).

    **.NET — filters:** `SuppressActions`, `SuppressAlternateKeys`, `SuppressContext`,
    `SuppressEntityTypeCode`, `SuppressLogicalNames`, `SuppressNavigationProperties`,
    `SuppressRelations` (all `bool`, default `false`).

    **.NET — output:** `EditableReadOnlyProperties` (`bool`, default `false`), `NameSpace`
    (`string`, default `"D365.Extension.Model"`), `NonDebuggerNonUserCode` (`bool`, default
    `false`), `SuppressNullableSupport` (`bool`, default `false`), `Virtual` (`bool`, default
    `false`).

    **TypeScript — filters:** `EntityFilters` (entity + `Attributes` + `Optionsets`),
    `EntityFormFilters` (`EntityForm` + `Attributes` + `Optionsets` + `Tabs` + `Sections` +
    `Grids`), `EntityRefFilter`, `Forms` (`string[]`, default `[]`), `OnlyFormsFromSolutions`
    (`bool`, default `false`).

    **TypeScript — additional content:** `BusinessProcessFlows` (`string[]`, default `[]`).

    **TypeScript — output:** `TypingPath` (`string`, default
    `"../../Typings/Xrm/index.d.ts"`).

## `push`

```text
dgtp push <FileOrFolder> [--solution <name>] [--publish] [--delete-obsolete] [--config <path>]
```

| Option | Effect |
|---|---|
| `<FileOrFolder>` | A `.dll`, a `.nupkg` (plugin package), or a folder (web resources). |
| `--solution` | Add created/updated components to this solution. Mandatory for web resources. |
| `--publish` | Publish changed objects after the push. |
| `--delete-obsolete` | Delete obsolete unmanaged web resources no longer present locally. **Dangerous on shared environments** — review its effect before automating unattended. |
| `--config` | Mapping file for web resource push, when on-disk layout doesn't map 1:1 to web resource names. |

Full behavior breakdown: see
[Pre- & Post-Deployment Tasks → `dgtp push` in depth](../alm/pre-post-deployment.md#dgtp-push-in-depth).

## `maintenance`

| Command | Description |
|---|---|
| `dgtp maintenance bulkdelete` | Start a bulk delete job for a given FetchXML and wait for completion. |
| `dgtp maintenance autonumber --config <path>` | Set the auto-number format for specified columns. |
| `dgtp maintenance protectfields` | Set `IsCustomizable = false` on all calculated fields. |
| `dgtp maintenance carrierinfo --filedir <dir> --filename <name>` | Export all active Carriers from an environment to JSON. See [Solution Concept](../architecture/solution-concept.md). |
| `dgtp maintenance solution-version <solution> [--major\|--minor\|--build\|--revision]` | Increment a solution's version directly on the target environment. See [Versioning](../alm/versioning.md). |
| `dgtp maintenance createworkflowstate --output <path> [--solutions <list>\|--publishers <list>]` | Generate a workflow-state configuration file. |
| `dgtp maintenance workflowstate --config <path>` | Apply a workflow-state configuration (activate/deactivate workflows in bulk). |
| `dgtp maintenance removeredundantcomponents <deploysolution> <sourcesolution> [--dryrun]` | Remove solution components from `deploysolution` that already exist in `sourcesolution`. |
| `dgtp maintenance filterfxplugins --config <path>` | Add message filtering for Power Fx plugin steps. |
| `dgtp maintenance ensuresdksteps --solution <name>` | Ensure SDK message processing steps within a solution are enabled (or disabled) as expected. |

## `analyze`

All `analyze` commands accept `--inline <data>` (inline solution list instead of a positional
list), `--note-patches`, `--generate-report`, `--generate-summary`, and `-c|--config <path>`
(default `config.json`).

| Command | Description |
|---|---|
| `dgtp analyze entityallassets` | Scan specified solutions for tables containing all standard assets. |
| `dgtp analyze noactivelayer --inline <solutions>` | Scan (unmanaged) solutions for components without an active layer. |
| `dgtp analyze redundantcomponents` | Scan for components present in multiple of the specified solutions. |
| `dgtp analyze redundantpatches` | Find patch solutions that no longer contain any top-layer components and can be retired. See [Solution Architecture](../architecture/solution-architecture.md#managed-vs-unmanaged). |
| `dgtp analyze activelayer --inline <solutions>` | Scan (managed) solutions for components with an active layer. |
| `dgtp analyze toplayer --inline <solutions>` | Scan (managed) solutions for components where the solution isn't the top layer. |

## `export` / `import`

Most config-object types support a matched export/import pair — see
[Config & Reference Data Migration](../alm/config-data-migration.md) for the ALM context. All
`export` commands accept `--filedir <dir>` (default current dir), `--filename <name>`, and
`--inline <data>`. All `import` commands accept the same plus `--assignee <owner>` for
record-owner assignment on import.

| Type | Export | Import |
|---|---|---|
| Team templates | `dgtp export teamtemplates` | `dgtp import teamtemplates` |
| Bulk delete jobs | `dgtp export bulkdeletes` | `dgtp import bulkdeletes` |
| Queues | `dgtp export queues` | `dgtp import queues` |
| Document templates | `dgtp export documenttemplates` | `dgtp import documenttemplates` |
| Calendars | `dgtp export calendars` | `dgtp import calendar` |
| SLAs | `dgtp export slaconfigs` | `dgtp import slaconfigs` |
| Routing rules | `dgtp export routingruleconfigs` | `dgtp import routingruleconfigs` |
| User-assigned security roles | `dgtp export userroles` | `dgtp import userroles` |
| Outlook templates | `dgtp export outlooktemplates` | `dgtp import outlooktemplates` |
| Secure configs | — *(import-only by design)* | `dgtp import secureconfigs` |

## Global options

Every command additionally inherits the program-wide settings (active profile, polling
behavior) described in [dgtp CLI](../setup/dgtp-cli.md) — `dgtp.json` /
`dgtp:pollrate` environment variable for the Dataverse operation poll interval.
