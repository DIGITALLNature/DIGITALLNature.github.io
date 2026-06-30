# dgtp Command Reference

The complete reference for every `dgtp` command, branch by branch — including all arguments,
options (with defaults), and configuration-file attributes. This page is the canonical command
reference for the [`dgt.power`](https://github.com/DIGITALLNature/DigitallPower) CLI.

`dgtp` is a .NET 8 global tool (`dotnet tool install -g dgt.power`). Run `dgtp` for the branch
list or `dgtp <command> --help` for the authoritative options of the exact build you have
installed; this page documents the behavior in depth.

For installation and a guided walkthrough, see [dgtp CLI](../setup/dgtp-cli.md).

## Connecting & global configuration

Every command that talks to Dataverse resolves its connection in this order
(`dgt.power.common/Logic/XrmConnection.cs`):

1. **Inline connection string** — if any `xrm:*` setting is present, `dgtp` connects with it and
   **ignores any selected profile**:
    - `xrm:connection` — the Dataverse connection string.
    - `xrm:securityprotocol` — TLS protocol (optional).
    - `xrm:insecure` — bypass TLS certificate validation (optional; dev/self-signed only).
2. **Selected profile** — otherwise the currently selected [profile](#profile) is used.
3. Otherwise the command fails with a missing-connection error.

Settings come from two sources, both loaded at startup:

| Source | How |
|---|---|
| `dgtp.json` | Optional JSON file in the current directory. |
| Environment variables | Any setting, prefixed with `dgtp:` and using `:` as the hierarchy separator — e.g. `dgtp:xrm:connection`, `dgtp:pollrate`. |

| Setting | Default | Purpose |
|---|---|---|
| `pollrate` | `5000` | Poll interval (ms) for long-running Dataverse async operations before timing out. Raise it for slow sandboxes. |
| `xrm:connection` | — | Inline connection string (see above); the idiomatic CI authentication path. |
| `xrm:securityprotocol` | — | TLS protocol for the inline connection. |
| `xrm:insecure` | `false` | Bypass TLS validation for the inline connection — dev/self-signed endpoints only. |

```json title="dgtp.json (example)"
{ "pollrate": 15000 }
```

```yaml title="CI: connect via environment variable, no stored profile"
env:
  dgtp:xrm:connection: $(PowerPlatformConnectionString)
```

---

## `profile`

Manages stored authentication profiles. Profiles live in **isolated storage scoped to the
current user** — not in the project directory or source control, so each developer maintains
their own.

| Command | Arguments | Description |
|---|---|---|
| `dgtp profile list` | — | List all stored profiles. |
| `dgtp profile create` | `<Name> <ConnectionString>` | Create (or overwrite) a profile — see options below. |
| `dgtp profile select` | `<Name>` | Set a profile as the active one. |
| `dgtp profile delete` | `<Name>` | Delete a profile. |
| `dgtp profile purge` | — | Delete **all** profiles. |

`Name` and `ConnectionString` are **positional** arguments (there is no `--name`/
`--connectionstring` option). `dgtp profile create` additionally accepts:

| Option | Type | Default | Description |
|---|---|---|---|
| `--msal` | bool | `false` | Token-based authentication via MSAL — use for service-principal / client-secret (CI) profiles. |
| `--skipcheck` | bool | `false` | Skip the connection test that otherwise runs on create. |
| `-s`, `--security-protocol` | string | `tls12` | TLS protocol; must be one of `ssl3`, `tls`, `tls11`, `tls12` (validated). |
| `-i`, `--insecure` | bool | `false` | Ignore TLS certificate issues — self-signed dev/test endpoints only. |

```shell
dgtp profile create dev "AuthType=OAuth;Url=https://yourorg.crm.dynamics.com;AppId=...;RedirectUri=...;LoginPrompt=Auto"
dgtp profile create build "$(ConnectionString)" --msal     # CI / service principal
dgtp profile select dev
```

---

## `codegeneration` (alias `cg`)

```text
dgtp codegeneration [TargetDirectory] [-f|--folder <name>] [-c|--config <path>]
```

Generates early-bound model files — `.cs`, `.ts`, and `metadata.xml` — from Dataverse metadata.
See [Early-Bound Models](../coding/serverside/early-binding.md) (server-side) and
[TS Model Generation](../coding/clientside/model-generation.md) (client-side) for worked config
examples and guidance.

| Argument / option | Type | Default | Description |
|---|---|---|---|
| `[TargetDirectory]` | string (positional) | `.` | Output root directory. |
| `-f`, `--folder` | string | `Model` | Sub-folder created under `TargetDirectory` for the generated model. |
| `-c`, `--config` | string | `config.json` | Path to the JSON configuration file. |

```shell
dgtp codegeneration ./src/Generated --config ./modelconfig.json
dgtp cg ./src/WebResources/model --folder dataverse --config ./ts-modelconfig.json
```

Output is written to `<TargetDirectory>/<Folder>/` in up to three sub-folders: `DotNet/`
(`.cs`), `TypeScript/` (`.ts`), and `MetaData/` (`.xml`) — each suppressible via the flags
below.

### Configuration attributes

The config file (default `config.json`) deserializes to `CodeGenerationConfig`. Attributes
apply to the **.NET**, **TypeScript**, or **both** targets as noted.

**General**

| Attribute | Type | Default | Description |
|---|---|---|---|
| `NameSpace` | string | `"Digitall.APower.Model"` | Namespace for generated .NET classes. |
| `Entities` | string[] | `[]` | Logical names of tables to generate. |
| `Actions` | string[] | `[]` | Actions to generate. |
| `CustomAPIs` | string[] | `[]` | Custom APIs to generate. |
| `AdditionalSdkMessages` | string[] | `[]` | Extra SDK messages to include. |
| `GlobalOptionSets` | string[] | `[]` | Global option sets to generate. |
| `Solutions` | string[] | `[]` | Restrict generation to these solution unique names. |
| `EntityMask` | string | `null` | RegEx mask to filter entities (e.g. `dgt_*`). |
| `SdkMessageFilters` | string[] | `[]` | Filter generated SDK messages. |
| `Hints` | bool | `true` | Emit generation hints to the console. |
| `SuppressOptions` | bool | `false` | Don't generate option-set values (.NET & TS). |
| `SuppressSdkMessages` | bool | `false` | Don't generate SDK message names (.NET & TS). |
| `SuppressDotNet` | bool | `false` | Skip the entire `.cs` (`DotNet/`) output. |
| `SuppressTypeScript` | bool | `false` | Skip the entire `.ts` (`TypeScript/`) output. |
| `SuppressMetaData` | bool | `false` | Skip the `metadata.xml` (`MetaData/`) output. |
| `UseBaseLanguage` | bool | `false` | Use the organization base language instead of the user language. |

**.NET only**

| Attribute | Type | Default | Description |
|---|---|---|---|
| `SuppressContext` | bool | `false` | Don't generate the `DataContext`/`Context.cs`. |
| `SuppressActions` | bool | `false` | Don't generate `Actions.cs`. |
| `SuppressLogicalNames` | bool | `false` | Omit the `LogicalNames` constant classes. |
| `SuppressRelations` | bool | `false` | Omit relationship properties. |
| `SuppressNavigationProperties` | bool | `false` | Omit navigation properties. |
| `SuppressEntityTypeCode` | bool | `false` | Omit the `EntityTypeCode` constant. |
| `SuppressAlternateKeys` | bool | `false` | Omit alternate-key helpers. |
| `SuppressNullableSupport` | bool | `false` | Disable C# 8+ nullable reference types in the output (the right default for shared `net462` plugin models). |
| `Virtual` | bool | `false` | Make generated properties `virtual` (e.g. for mocking). |
| `EditableReadOnlyProperties` | bool | `false` | Make read-only properties settable. |
| `NonDebuggerNonUserCode` | bool | `false` | Omit the `[DebuggerNonUserCode]` annotation. |

**TypeScript only**

| Attribute | Type | Default | Description |
|---|---|---|---|
| `TypingPath` | string | `"../../Typings/Xrm/index.d.ts"` | Path to the `@types/xrm` typings the generated classes are built against. |
| `Forms` | string[] | `[]` | Form names to generate (format `{entity}.{formname}.form`). |
| `OnlyFormsFromSolutions` | bool | `false` | Only generate forms belonging to the configured `Solutions`. |
| `BusinessProcessFlows` | string[] | `[]` | BPF names to generate. |
| `EntityFilters` | `EntityFilter[]` | `[]` | Per-entity attribute/option-set narrowing. |
| `EntityRefFilters` | `EntityRefFilter[]` | `[]` | Per-entity-reference narrowing. |
| `EntityFormFilters` | `EntityFormFilter[]` | `[]` | Per-form narrowing. |

**Filter object shapes** (all TypeScript-only):

| Object | Members |
|---|---|
| `EntityFilter` | `Entity` (required), `Attributes` (string[]), `Optionsets` (string[]) |
| `EntityRefFilter` | `Entity` (required), `Attributes` (string[]), `Optionsets` (string[]) |
| `EntityFormFilter` | `EntityForm` (required), `Attributes`, `Optionsets`, `Tabs`, `Sections`, `Grids` (all string[]) |

#### Example `config.json`

```json title="config.json"
{
  "Entities": ["account", "contact"],
  "Actions": [],
  "CustomAPIs": [],
  "Solutions": ["dgt_core"],
  "EntityMask": "dgt_*",
  "SuppressTypeScript": true,
  "SuppressMetaData": true,
  "SuppressNullableSupport": true
}
```

See [Early-Bound Models](../coding/serverside/early-binding.md) and
[TS Model Generation](../coding/clientside/model-generation.md) for target-specific (.NET vs.
TypeScript) example configs.

---

## `push`

```text
dgtp push <FileOrFolder> [--solution <name>] [--publish] [--delete-obsolete] [--config <path>]
```

| Argument / option | Type | Default | Description |
|---|---|---|---|
| `<FileOrFolder>` | string (positional, required) | — | A `.dll`, a `.nupkg` (plugin package), or a folder (web resources). |
| `--solution` | string | none | Add created/updated components to this solution. **Mandatory for web resources.** |
| `--publish` | bool | `false` | Publish changed objects after the push. |
| `--delete-obsolete` | bool | `false` | Delete **unmanaged** web resources that exist in the solution but no longer on disk. **Dangerous on shared environments.** |
| `--config` | string | none | Mapping file for web-resource push, when the on-disk layout doesn't map 1:1 to web-resource names. |

```shell
dgtp push ./bin/Release/MyPlugins.1.0.0.nupkg --solution dgt_core --publish   # plugin package
dgtp push ./dist --solution dgt_core --publish --delete-obsolete              # web resources
```

### What `push` does, by target type

1. **`.nupkg` (plugin package):** unpacked locally and compared (by name + version) against the
   `PluginPackage` already registered — created if new, updated in place if present.
2. **`.dll` (classic assembly):** loaded by reflection (not executed) and compared the same way
   against the registered `PluginAssembly`. Skipped with a warning if it contains neither plugin
   nor workflow types.
3. For assemblies/packages, `push` then reconciles against the
   [registration attributes](../coding/serverside/registration-attributes.md) in code:
   upserts **and purges** plugin/workflow types, SDK message processing **steps**, and pre/post
   **entity images** — so removing a `[PluginRegistration]` attribute and re-pushing unregisters
   the step.
4. **Folder (web resources):** every supported file is created/updated in `--solution`, matched
   by name; `--config` remaps on-disk paths to web-resource names; `--delete-obsolete` removes
   unmanaged web resources no longer present on disk.

Supported web-resource extensions: `.html`, `.css`, `.js`, `.xml`, `.png`, `.jpg`, `.gif`,
`.xap`, `.xsl`, `.ico`, `.svg`, `.resx`.

The `--config` mapping file is only needed when files aren't named after their target web
resources — it maps on-disk paths to web-resource names:

```json title="webresource-map.json"
{
  "maps": {
    "Form/account.js": "dgt_/scripts/form/account.js",
    "Ribbon/account.js": "dgt_/scripts/ribbon/account.js"
  }
}
```

---

## `maintenance`

Administrative tasks against the connected environment. Commands that derive from the base
maintenance verb inherit `-c`/`--config` (default `config.json`) and `--inline` (inline data,
supported only by some tasks); commands with their own option set are noted below.

### `dgtp maintenance bulkdelete`

Starts a bulk-delete job and waits for completion.

| Option | Default | Description |
|---|---|---|
| `--inline <fetchxml>` | — | The FetchXML selecting the records to delete. |
| `-c`, `--config` | `config.json` | Config file (alternative to `--inline`). |

```shell
dgtp maintenance bulkdelete --inline "<fetch><entity name='dgt_log'/></fetch>"
```

### `dgtp maintenance autonumber`

Sets the auto-number format on the configured columns.

`-c`/`--config` (default `config.json`) → array of:

```json
[ { "entity": "account", "field": "accountnumber", "format": "ACC-{SEQNUM:0000}" } ]
```

```shell
dgtp maintenance autonumber --config ./autonumber.json
```

### `dgtp maintenance protectfields`

Sets `IsCustomizable = false` on all calculated fields (prevents them from receiving an active
layer). No specific options.

```shell
dgtp maintenance protectfields
```

### `dgtp maintenance carrierinfo`

Exports all active Carriers from the environment to JSON (see
[Solution Concept](../architecture/solution-concept.md)).

| Option | Default | Description |
|---|---|---|
| `--filedir` | `.` | Output directory. |
| `--filename` | `carrier.json` | Output file name. |
| `-c`, `--config` | `config.json` | Inherited config option. |

```shell
dgtp maintenance carrierinfo --filedir ./carriers
```

### `dgtp maintenance solution-version <Solution>`

Increments a solution's version directly on the environment (see
[Versioning](../alm/versioning.md)). One of the flags below picks the segment; **`--revision`
is the default** when none is given.

| Argument / option | Default | Description |
|---|---|---|
| `<Solution>` | — | Unique name of the solution. |
| `--major` | `false` | `1.0.0.0` → `2.0.0.0`. |
| `--minor` | `false` | `1.0.0.0` → `1.1.0.0`. |
| `--build` | `false` | `1.0.0.0` → `1.0.1.0`. |
| `--revision` | `true` | `1.0.0.0` → `1.0.0.1` (default). |

```shell
dgtp maintenance solution-version dgt_core --minor
```

### `dgtp maintenance createworkflowstate`

Generates a workflow-state [config file](#dgtp-maintenance-workflowstate) from the environment's
current workflows.

| Option | Default | Description |
|---|---|---|
| `-o`, `--output` | `config.json` | Output file path. |
| `--overwrite` | `false` | Overwrite the output file if it exists. |
| `-s`, `--solutions` | — | Comma-separated solution unique names to consider (wildcards `%` allowed). |
| `-p`, `--publishers` | — | Comma-separated publisher names to consider (wildcards `%` allowed). |
| `--tablereport` | `true` | Print a table report to the console afterwards. |

```shell
dgtp maintenance createworkflowstate -o ./workflowstate.json --solutions dgt_core
```

### `dgtp maintenance workflowstate`

Applies a workflow-state config (enable/disable, owner, impersonation) in bulk.

| Option | Default | Description |
|---|---|---|
| `-c`, `--config` | `config.json` | The workflow-state config file. |
| `--tablereport` | `true` | Print a table report afterwards. |

Config shape (`WorkflowConfig` — note the lowercase JSON keys):

```json
{
  "owner": "user@contoso.com",
  "impersonate": null,
  "solutionfilter": ["dgt_core"],
  "publisherfilter": ["dgt"],
  "flows":         { "My Flow":     { "disabled": false, "owner": null, "impersonate": null } },
  "actions":       { "dgt_MyAction": { "disabled": false, "owner": null } },
  "businessrules": { "account": { "My Rule": { "disabled": false, "owner": null } } }
}
```

```shell
dgtp maintenance workflowstate --config ./workflowstate.json
```

### `dgtp maintenance removeredundantcomponents <SourceSolutions> <TargetSolution>`

Removes components from **`<TargetSolution>`** that also exist in **`<SourceSolutions>`** (the
read-out reference).

| Argument / option | Default | Description |
|---|---|---|
| `<SourceSolutions>` | — | Reference solution(s) whose components are read out; comma-separate for multiple. |
| `<TargetSolution>` | — | Solution the redundant components are **removed from**. |
| `--dryrun` | `false` | Report what would be removed without deleting. |
| `--includeEntities` | `false` | Also remove tables (experimental). |

!!! warning "Destructive"
    Run with `--dryrun` first and review the output before removing anything.

```shell
dgtp maintenance removeredundantcomponents dgt_core tmp_solution --dryrun
```

### `dgtp maintenance filterfxplugins`

Adds message filtering to Power Fx plugin steps.

`-c`/`--config` (default `config.json`) → array of:

```json
[ { "name": "<step name>", "filter": ["field1", "field2"], "message": "Update" } ]
```

`message` is one of `Create`, `Update`, `Delete`.

```shell
dgtp maintenance filterfxplugins --config ./fxplugins.json
```

### `dgtp maintenance ensuresdksteps`

Enables — or with `--disabled`, disables — all SDK message processing steps in a solution.

| Option | Default | Description |
|---|---|---|
| `-s`, `--solution` | `assemblies` | Unique name of the solution to work on. |
| `-d`, `--disabled` | `false` | Disable the steps instead of enabling them. |
| `--dry-run` | `false` | Report without changing anything. |

```shell
dgtp maintenance ensuresdksteps --solution assemblies --dry-run
```

---

## `analyze`

Solution-layering analysis. **Every** `analyze` command accepts these branch-wide options:

| Option | Default | Description |
|---|---|---|
| `--inline <data>` | — | Inline solution list instead of a config file. |
| `--note-patches` | `false` | Include patch solutions in the analysis. |
| `--generate-report` | `false` | Write a CSV report (to `./Analyze/`). |
| `--generate-summary` | `false` | Write a JSON summary. |
| `-c`, `--config` | `config.json` | Config file. |

| Command | Description |
|---|---|
| `dgtp analyze entityallassets` | Scan the configured solutions for tables that include all standard assets. |
| `dgtp analyze noactivelayer --inline <solutions>` | Scan (unmanaged) solutions for components without an active layer. |
| `dgtp analyze redundantcomponents --inline <solutions>` | Find components present in more than one of the given solutions. |
| `dgtp analyze redundantpatches --inline <solution>` | Find patch solutions with no top-layer components left (safe to retire). Target must be managed. See [Solution Architecture](../architecture/solution-architecture.md#managed-vs-unmanaged). |
| `dgtp analyze activelayer --inline <solutions>` | Scan (managed) solutions for components with an active layer. |
| `dgtp analyze toplayer --inline <solutions>` | Scan (managed) solutions for components where the solution isn't the top layer. |

`entityallassets` config (`-c`/`--config`) → array of:

```json
[ { "solution": "dgt_core", "strict": true, "whitelist": ["dgt_.*"], "blacklist": [] } ]
```

`whitelist`/`blacklist` are RegEx lists; `strict: true` turns an unapproved table into an error
rather than a warning.

```shell
dgtp analyze entityallassets --config ./entityallassets.json --generate-report
dgtp analyze redundantcomponents --inline dgt_core,dgt_app --generate-summary
```

---

## `export` / `import`

Most config-object types have a matched export/import pair — see
[Config & Reference Data Migration](../alm/config-data-migration.md) for the ALM context.

**All `export` commands** accept:

| Option | Default | Description |
|---|---|---|
| `--filedir` | `.` | Output directory. |
| `--filename` | `<task>.json` | Output file name. |
| `--inline` | — | Inline data (selected commands only). |

**All `import` commands** accept the same, plus:

| Option | Default | Description |
|---|---|---|
| `--filedir` | *(current dir)* | Input directory. |
| `--filename` | `<task>.json` | Input file name. |
| `--inline` | — | Inline data (selected commands only). |
| `--assignee` | — | Record owner to assign on import (queues, SLAs, routing rules, …). |

| Type | Export | Import | Default file |
|---|---|---|---|
| Team templates | `dgtp export teamtemplates` | `dgtp import teamtemplates` | `teamtemplate.json` |
| Bulk delete jobs | `dgtp export bulkdeletes` | `dgtp import bulkdeletes` | `bulkdelete.json` |
| Queues | `dgtp export queues` | `dgtp import queues` | `queue.json` |
| Document templates | `dgtp export documenttemplates` | `dgtp import documenttemplates` | `documenttemplate.json` |
| Calendars | `dgtp export calendars` | `dgtp import calendar` | `calendar.json` |
| SLAs | `dgtp export slaconfigs` | `dgtp import slaconfigs` | `slaconfig.json` |
| Routing rules | `dgtp export routingruleconfigs` | `dgtp import routingruleconfigs` | `routingruleconfig.json` |
| User-assigned security roles | `dgtp export userroles` | `dgtp import userroles` | `userrole.json` |
| Outlook templates | `dgtp export outlooktemplates` | `dgtp import outlooktemplates` | `outlooktemplate.json` |
| Secure configs | — *(import-only by design)* | `dgtp import secureconfigs` | `secureconfig.json` |

!!! note "Calendar import is singular"
    Export is `dgtp export calendars` (plural); import is `dgtp import calendar` (singular).

```shell
dgtp export queues --filedir ./data
dgtp import queues --filedir ./data --assignee integration@contoso.com
```
