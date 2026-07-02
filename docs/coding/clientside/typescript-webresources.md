# TypeScript Web Resources

**`DGT-CLI-010`**{ #dgt-cli-010 } — Form and command-bar (ribbon) scripts are written in TypeScript and bundled with webpack. The
canonical starting point is the
**[`webresources-template`](https://github.com/DIGITALLNature/webresources-template)**
repository — clone it rather than wiring up TypeScript, bundling, and linting from scratch.

## Toolchain

| Tool | Role |
|---|---|
| **pnpm** | Package manager — enforced via a `preinstall` hook (`only-allow pnpm`), so `npm`/`yarn` are rejected. |
| **TypeScript** (`strict`) | `@types/xrm` provides the Xrm API typings; `tsconfig.json` targets ES6 with `moduleResolution: bundler`. |
| **webpack** + `ts-loader` | Bundles each entry point into a single web resource file. |
| **Biome** | Linting **and** formatting in one tool (replaces ESLint + Prettier). Config in `biome.json`. |
| **Jest** + `xrm-mock` | Unit testing against a mocked `Xrm` — see [Client-side Testing](../../testing/clientside-testing.md). |

Node.js **24.x** is the recommended runtime. Install dependencies with `pnpm install`.

## Project structure

```text
src/
├── form/      # form scripts — entry points named *.form.ts
├── ribbon/    # command-bar scripts — entry points named *.ribbon.ts
└── model/     # generated early-bound TypeScript model (dgtp); excluded from linting
```

webpack discovers entry points by globbing `src/form/*.form.ts` and `src/ribbon/*.ribbon.ts`
and emits one bundle per entry into `dist/scripts/form/<name>.form.js` /
`dist/scripts/ribbon/<name>.ribbon.js`. Each bundle's exports are attached to a single global
object, **`DGT`** (webpack `library.type: assign-properties`) — so a handler written as
`export namespace Account { export function onLoad… }` in `account.form.ts` is referenced in the
form designer as **`DGT.Account.onLoad`**.

!!! tip "One entry point per form/ribbon; shared code in plain modules"
    **`DGT-CLI-030`**{ #dgt-cli-030 } — Only files matching `*.form.ts` / `*.ribbon.ts` become bundles. Put shared helpers in
    ordinary `.ts` modules and `import` them — they're compiled into the bundles that use them,
    rather than emitted as separate web resources.

## Build & test

| Command | Does |
|---|---|
| `pnpm run build` | dependency check + Biome lint + **development** bundle (inline source maps). |
| `pnpm run build:prod` | Biome lint + **minified production** bundle. |
| `pnpm run bundle` / `bundle:prod` | webpack only — dev / prod. |
| `pnpm run lint` | Biome lint. |
| `pnpm run test` / `test-watch` | Jest (`--runInBand`). |

## Register `OnLoad` only — bind everything else in code

**`DGT-CLI-020`**{ #dgt-cli-020 } — Register only the form's **`OnLoad`** event in the form designer (pointing at, e.g.,
`DGT.Account.onLoad`). Register all other handlers — `OnSave`, field `OnChange`, tab/section
visibility, and so on — programmatically from inside `onLoad`:

```typescript title="src/form/account.form.ts"
export namespace Account {
  export function onLoad(executionContext: Xrm.Events.EventContext): void {
    const formContext = executionContext.getFormContext<XrmTable.Account.AccountMainFormContext>();

    formContext.data.entity.addOnSave(onSave);
    formContext.getAttribute("name")?.addOnChange(onNameChange);
  }

  function onSave(executionContext: Xrm.Events.SaveEventContext): void { /* ... */ }
  function onNameChange(executionContext: Xrm.Events.EventContext): void { /* ... */ }
}
```

This keeps a form's full event wiring visible in source control, rather than split between the
form XML and the script — a form's behavior shouldn't require opening the form designer to
understand.

## Use the generated, form-typed model

**`DGT-CLI-040`**{ #dgt-cli-040 } — Generate typed models with `dgtp codegeneration` (see
[TS Model Generation](model-generation.md)) into `src/model/`, and type the form context against
them instead of looking attributes up by bare string:

- `executionContext.getFormContext<XrmTable.Account.AccountMainFormContext>()` exposes only the
  attributes, controls, tabs, and sections that exist **on that specific form** — a typo in an
  attribute name becomes a compile error.
- `XrmTable.Account.FormContext` is the looser, whole-table variant for scripts not tied to a
  single form.

The template's `scripts/model-generate.ps1` wraps the dgtp call:

```powershell
dgtp profile select <profile>
dgtp codegeneration ./src/model/ --config ./src/model/model.json --folder dataverse
```

`src/model/` is generated and excluded from Biome — don't hand-edit it, and keep it out of
source control the same way as server-side models (see
[Source Control](../../alm/source-control.md#gitignore)).

## Formatting & line endings

**`DGT-CLI-050`**{ #dgt-cli-050 } — Biome owns both formatting (4-space indent, single quotes, **LF** line endings) and linting —
run `pnpm run lint` and don't hand-tune style. Because Biome normalizes to LF, mixed line
endings are a non-issue here; the repo-wide `.gitattributes`
([Source Control](../../alm/source-control.md#gitattributes)) stays as the belt-and-braces
default.

## Deploy

`pnpm run build:prod` produces `dist/scripts/...`; push it with `dgtp push`:

```powershell
dgtp push ./dist --solution <WebResourceSolution> --publish
```

See [`dgtp push` → web resource push](../../alm/pre-post-deployment.md#web-resource-push) for
`--delete-obsolete` and mapping-config behavior, and
[Build Pipeline](../../alm/build-pipeline.md) for where this runs in CI. The template ships a
ready-made Azure DevOps pipeline (root `azure-pipelines.yml`) that builds with pnpm on Node 24
and pushes via `dgtp` using a `dgtp:xrm:connection` connection string — see
[dgtp CLI → Configuration](../../setup/dgtp-cli.md#connecting-profile-or-connection-string).
The repo also still contains `.azure-pipelines/build-push-webresources.yml`, an older
npm/Node-22-based pipeline with unresolved `# TODO` placeholders that nothing currently
references — ignore it until it's cleaned up or removed.
