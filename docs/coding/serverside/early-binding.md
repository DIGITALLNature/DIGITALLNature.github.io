# Early-Bound Models (dgtp)

**`DGT-SRV-110`**{ #dgt-srv-110 } — Use early-bound, strongly-typed classes for server-side Dataverse access rather than
late-bound `Entity["fieldname"]` access — it gives you compile-time checking against the schema and
working IntelliSense. Generate them with `dgtp codegeneration` (alias `cg`); this is the
DIGITALL standard in place of the legacy `CrmSvcUtil`-based generator.

```shell
dgtp codegeneration ./src/Generated --config ./modelconfig.json
```

```shell
dgtp codegeneration ./subfolder/models -f CustomModel -c ./CustomModel/modelconfig.json
```

| Argument | Meaning |
|---|---|
| `TargetDirectory` (positional) | Output path, default current folder. |
| `--folder` / `-f` | Subfolder name for the generated model, default `Model`. |
| `--config` / `-c` | Path to the JSON config file, default `config.json`. |

## When this runs

Codegeneration runs **before** the server-side build compiles, both locally (after pulling
schema changes — see [Repository Bootstrap](../../setup/repository-bootstrap.md)) and in CI
(see [Build Pipeline](../../alm/build-pipeline.md)). Generated output is **not committed** to
source control — see [Source Control](../../alm/source-control.md#gitignore) — it's
regenerated deterministically from the schema on every build.

!!! note "Config file version"
    The examples on this page use the **V1** config shape (single file, both .NET and TypeScript
    output). V1 still works — it's mapped internally onto the newer engine — but the
    **V2** format (`"version": 2`, one focused file per output target) is now the recommended
    default for new configs. See
    [dgtp Command Reference → Configuration file versions](../../reference/dgtp-commands.md#codegeneration-alias-cg)
    for the full V2 schema and the V1 → V2 migration table.

## Targets and a minimal config

A single config file can target .NET classes, TypeScript classes, and/or a metadata XML file in
one run; suppress whichever you don't need.

```json title="modelconfig.json — plugin/server-side usage"
{
  "Entities": ["account", "contact"],
  "Actions": [],
  "CustomAPIs": [],
  "AdditionalSdkMessages": ["Associate", "Disassociate"],
  "SuppressTypeScript": true,
  "SuppressMetaData": true,
  "SuppressNullableSupport": true,
  "EditableReadOnlyProperties": true,
  "UseBaseLanguage": true
}
```

`SuppressNullableSupport: true` is the right default for **plugin** projects — the plugin
`.csproj` sets `<Nullable>disable</Nullable>` (see [Project Setup](project-setup.md)), and
generating the model without nullable-reference-type annotations keeps it consistent with that
setting and with [dependent-plugin shared models](patterns.md), which must all agree on it.

## Scoping generation to what you need

For larger schemas, scope generation rather than generating every table in the environment:

```json title="modelconfig.json — filtered to specific fields"
{
  "Entities": ["account", "contact"],
  "Actions": [],
  "CustomAPIs": [],
  "EntityFilters": [
    {
      "Entity": "contact",
      "Attributes": ["firstname", "lastname"],
      "Optionsets": ["statecode", "statuscode"]
    }
  ]
}
```

`Solutions` + `EntityMask` is the usual pattern for "everything belonging to this project":

```json title="modelconfig.json — scoped to a solution"
{
  "Entities": [],
  "Actions": [],
  "CustomAPIs": [],
  "Solutions": ["dgt_myproject_core"],
  "EntityMask": "dgt_*",
  "OnlyFormsFromSolutions": true
}
```

See [Reference → dgtp Command Reference](../../reference/dgtp-commands.md#codegeneration-alias-cg)
for the full parameter list (filters, suppression flags, TypeScript-specific options for
client-side use).

## TypeScript models

The same command generates TypeScript models for [client-side](../clientside/model-generation.md)
projects — typically as a separate config/invocation with `SuppressDotNet: true`, since the
filtering needs (form-scoped vs. plugin-scoped) usually differ between the two.
