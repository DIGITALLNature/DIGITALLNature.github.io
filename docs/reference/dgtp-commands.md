# dgtp Command Reference

The complete reference for every `dgtp` command, branch by branch — including all arguments,
options (with defaults), and configuration-file attributes. This page is the canonical command
reference for the [`dgt.power`](https://github.com/DIGITALLNature/DigitallPower) CLI.

`dgtp` is a .NET 10 global tool (`dotnet tool install -g dgt.power`). Run `dgtp` for the branch
list or `dgtp <command> --help` for the authoritative options of the exact build you have
installed; this page documents the behavior in depth.

For installation and a guided walkthrough, see [dgtp CLI](../setup/dgtp-cli.md).

## Connecting & global configuration

Every command that talks to Dataverse resolves its connection in this order
(`dgt.power.common/Logic/XrmConnection.cs`):

1. **Inline connection string** — if any `xrm:*` setting is present, `dgtp` connects with it and
   **ignores any selected profile**:
    - `xrm:connection` — the Dataverse connection string.
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
| `dgtp profile auth-check` | — | Non-interactively check whether the active profile's MSAL token is still valid — no browser prompt. Exit code `0` = valid (or a classic, non-MSAL profile), exit code `2` = interactive login required. Intended as a pre-flight check for automation/coding agents before running any other Dataverse command. |

`Name` and `ConnectionString` are **positional** arguments (there is no `--name`/
`--connectionstring` option). `dgtp profile create` additionally accepts:

| Option | Type | Default | Description |
|---|---|---|---|
| `--msal` | bool | `false` | Token-based authentication via MSAL — use for service-principal / client-secret (CI) profiles. |
| `--skipcheck` | bool | `false` | Skip the connection test that otherwise runs on create. |

```shell
dgtp profile create dev "AuthType=OAuth;Url=https://yourorg.crm.dynamics.com;AppId=...;RedirectUri=...;LoginPrompt=Auto"
dgtp profile create build "$(ConnectionString)" --msal     # CI / service principal
dgtp profile select dev
dgtp profile auth-check   # exit 0 = valid, exit 2 = re-authenticate needed
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

### Configuration file versions

The config file (default `config.json`) supports two schemas, distinguished by a `version`
field. JSON schemas for both are under
[`schemas/codegeneration/`](https://github.com/DIGITALLNature/DigitallPower/tree/beta/schemas/codegeneration)
in the DigitallPower repo.

- **V2 (`"version": 2`, recommended)** — one focused config file per output target
  (`"type": "dotnet"` or `"type": "typescript"`), separating **scope** (`entities`, `requests`,
  `optionSets` — what to load from Dataverse) from **output** (a type-specific `output` object —
  what to write to disk).
- **V1 (`"version": 1`, or no `version` field — legacy)** — a single file for both .NET and
  TypeScript output. Still works unchanged (mapped internally to the V2 engine before running),
  but new configs should use V2.

#### V2 — shared root properties

| Property | Type | Default | Description |
|---|---|---|---|
| `version` | integer | `1` | Must be `2` to use the V2 engine. |
| `type` | `"dotnet"` \| `"typescript"` | — | **Required.** Selects the generator and schema. |
| `namespace` | string \| null | `null` (TS) / `"Digitall.Dataverse.Model"` (.NET) | Root namespace for generated classes. |
| `language` | integer \| null | `null` | LCID for label localization (e.g. `1033`); `null`/omitted uses the organization's base language. |

#### V2 — scope properties (shared by both types)

| Property | Type | Default | Description |
|---|---|---|---|
| `entities.names` | string[] | `[]` | Explicit entity logical names. |
| `entities.fromSolutions` | string[] | `[]` | Include all entities belonging to these solutions. |
| `entities.mask` | string \| null | `null` | Publisher-prefix wildcard (e.g. `"dgt_*"`). |
| `requests` | string[] | `[]` | SDK message / custom action names — generates message constants. |
| `optionSets` | string[] | `[]` | Global option set logical names. |

The three `entities` inputs are an **additive union** — an entity is included if it matches
`names`, belongs to a listed solution, or matches `mask`.

#### V2 — .NET output (`"type": "dotnet"`)

| Property | Type | Default | Description |
|---|---|---|---|
| `output.target` | `"Modern"` \| `"Framework"` | `"Modern"` | `Modern` = net8.0+ (nullable, implicit usings); `Framework` = .NET Framework 4.6.2 — use this for Dataverse plugins (see [Project Setup](../coding/serverside/project-setup.md)). |
| `output.virtual` | bool | `false` | Add `virtual` to generated entity properties (mocking, subclassing). |
| `output.editableReadOnly` | bool | `false` | Make read-only attributes settable. |
| `output.include.context` | bool | `true` | Generate the `DataContext` class. |
| `output.include.options` | bool | `true` | Generate `OptionSetValues` enum classes. |
| `output.include.logicalNames` | bool | `true` | Generate logical-name string constants. |
| `output.include.relations` | bool | `true` | Generate relationship metadata. |
| `output.include.navigationProps` | bool | `true` | Generate navigation properties. |
| `output.include.entityTypeCode` | bool | `true` | Generate the entity type code constant. |
| `output.include.alternateKeys` | bool | `true` | Generate alternate-key members. |
| `output.include.metadata` | bool | `false` | Write `metadata.xml` sidecar files. |

#### V2 — TypeScript output (`"type": "typescript"`)

| Property | Type | Default | Description |
|---|---|---|---|
| `output.forms` | object \| absent | *(all forms)* | Omit to generate all forms for all scoped entities. |
| `output.forms.filter` | string[] | `[]` | Restrict to specific forms, `"entityLogicalName.formName"`; empty = all forms. |
| `output.forms.fromSolutions` | bool | `false` | Only include forms belonging to the solutions in `entities.fromSolutions`. |
| `output.forms.testHelpers` | bool | `false` | Also generate XrmMock test-helper files alongside form helpers. |
| `output.customApis` | bool | `true` | Generate typed Custom API request/response wrappers. |

#### V2 examples

```json title="genconfig.dotnet.json — plugin/server-side, entities from a solution"
{
  "version": 2,
  "type": "dotnet",
  "namespace": "Contoso.Dataverse.Model",
  "entities": { "fromSolutions": ["dgt_myproject_core"], "mask": "dgt_*" },
  "requests": ["dgt_ApproveOrder"],
  "output": { "target": "Framework", "include": { "metadata": false } }
}
```

```json title="genconfig.typescript.json — form-scoped, client-side"
{
  "version": 2,
  "type": "typescript",
  "entities": { "fromSolutions": ["dgt_myproject_core"] },
  "output": {
    "forms": { "filter": ["contact.Information"], "testHelpers": true }
  }
}
```

> **Tip:** run one command per config file —
> `dgtp cg ./generated -c ./genconfig.dotnet.json` and
> `dgtp cg ./generated -c ./genconfig.typescript.json`.

#### V1 — legacy, still supported

```json title="config.json (V1, legacy)"
{
  "version": 1,
  "Entities": ["account", "contact"],
  "Solutions": ["dgt_myproject_core"],
  "EntityMask": "dgt_*",
  "Actions": ["dgt_ApproveOrder"],
  "GlobalOptionSets": [],
  "NameSpace": "Contoso.Dataverse.Model",
  "SuppressMetaData": true
}
```

| V1 property | V2 equivalent |
|---|---|
| `Entities` | `entities.names` |
| `Solutions` | `entities.fromSolutions` |
| `EntityMask` | `entities.mask` |
| `Actions` / `SdkMessageFilters` | `requests` |
| `GlobalOptionSets` | `optionSets` |
| `NameSpace` | `namespace` |
| `OnlyFormsFromSolutions` | `output.forms.fromSolutions` |
| `SuppressMetaData` | `output.include.metadata: false` |

> **Note:** V1's per-attribute filters (`EntityFilters`, `EntityRefFilters`, `EntityFormFilters`)
> have **no V2 equivalent** — removed by design to keep the V2 schema focused. If you rely on
> attribute-level narrowing within a single generated form/entity, stay on V1 for that config
> until an equivalent lands in V2.

See [Early-Bound Models](../coding/serverside/early-binding.md) and
[TS Model Generation](../coding/clientside/model-generation.md) for target-specific usage
guidance, and the DigitallPower [README](https://github.com/DIGITALLNature/DigitallPower#-code-generation)
for the full, authoritative schema reference.

---

## `push`

```text
dgtp push <FileOrFolder> [--solution <name>] [--publish] [--delete-obsolete]
          [--delete-on-upgrade] [--no-migrate-custom-apis] [--config <path>]
```

| Argument / option | Type | Default | Description |
|---|---|---|---|
| `<FileOrFolder>` | string (positional, required) | — | A `.dll`, a `.nupkg` (plugin package), or a folder (web resources). |
| `--solution` | string | none | Add created/updated components to this solution. **Mandatory for web resources.** |
| `--publish` | bool | `false` | Publish changed objects after the push. |
| `--delete-obsolete` | bool | `false` | Delete **unmanaged** web resources that exist in the solution but no longer on disk. **Dangerous on shared environments.** |
| `--delete-on-upgrade` | bool | `false` | On a major/minor assembly version bump (which creates a **new** assembly record), migrate plugin steps and Custom API references to the new assembly, then delete the old one. |
| `--no-migrate-custom-apis` | bool | `false` | Skip Custom API reference migration on a version bump (ignored when `--delete-on-upgrade` is set, since deletion requires migration). |
| `--config` | string | none | Mapping file for web-resource push, when the on-disk layout doesn't map 1:1 to web-resource names. |

### Assembly version upgrades

When a plugin assembly's **major or minor** version changes (e.g. `1.0.0.0` → `1.1.0.0`),
Dataverse treats it as a different assembly identity, so `push` creates a **new** assembly
record rather than updating in place:

| Flag | Behavior |
|---|---|
| *(default)* | New assembly created; Custom API references migrated to the new types automatically. |
| `--delete-on-upgrade` | Migrates plugin steps **and** Custom APIs to the new assembly, then deletes the old assembly. |
| `--no-migrate-custom-apis` | Skips Custom API migration (ignored when `--delete-on-upgrade` is set). |

Plugin **packages** (`.nupkg`) don't need any of this — the platform manages assembly GUIDs
within a package, so content updates preserve all references automatically.

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
| `--detailed` | `false` | Include every process in the generated config, not just disabled ones or ones with a changed owner. |

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
