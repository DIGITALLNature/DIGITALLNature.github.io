# TS Model Generation (dgtp)

The client-side counterpart to [server-side early binding](../serverside/early-binding.md) —
same `dgtp codegeneration` command, a different config shape geared toward forms rather than
plugin entity access.

```shell
dgtp codegeneration ./src/WebResources/Generated --config ./ts-modelconfig.json
```

!!! note "Config file version"
    The examples on this page use the **V1** config shape. V1 still works, but **V2** is now the
    recommended format for new configs — except for the exact attribute/tab/section-level form
    narrowing `EntityFormFilters` gives you below, which has no V2 equivalent yet. See
    [dgtp Command Reference → Configuration file versions](../../reference/dgtp-commands.md#codegeneration-alias-cg).

## A form-scoped config

```json title="ts-modelconfig.json"
{
  "Entities": [],
  "Actions": [],
  "CustomAPIs": [],
  "SuppressDotNet": true,
  "SuppressMetaData": true,
  "TypingPath": "../../../node_modules/@types/xrm/index.d.ts",
  "UseBaseLanguage": true,
  "EntityFormFilters": [
    {
      "EntityForm": "Information",
      "Attributes": ["firstname", "lastname"],
      "Optionsets": ["statecode", "statuscode"],
      "Tabs": ["general"]
    }
  ]
}
```

`EntityFormFilters` scopes the generated model to exactly the attributes, option sets, tabs,
sections, and grids present on a specific form — narrower and more relevant to a form script
than generating a full table model. `TypingPath` points at the `@types/xrm` (or
[`dataverse-gen`](https://github.com/scottdurow/dataverse-gen)-compatible) typings the
generated classes are built against; keep this in sync with whichever `@types/xrm` version the
project's `package.json` actually has installed.

## Solution-scoped generation

For "every form belonging to this project" rather than listing forms individually:

```json title="ts-modelconfig.json — solution scoped"
{
  "Entities": [],
  "Actions": [],
  "CustomAPIs": [],
  "Solutions": ["dgt_myproject_core"],
  "EntityMask": "dgt_*",
  "OnlyFormsFromSolutions": true,
  "SuppressDotNet": true
}
```

## Keep generated output out of source control

Same rule as the server-side model — see
[Source Control](../../alm/source-control.md#gitignore) — the generated `.ts` files are
reproducible from this config and the current schema, and are regenerated as part of
[Build Pipeline](../../alm/build-pipeline.md) and locally per
[Repository Bootstrap](../../setup/repository-bootstrap.md), not committed.

See [Reference → dgtp Command Reference](../../reference/dgtp-commands.md#codegeneration-alias-cg)
for the complete parameter list shared between the .NET and TypeScript targets.
