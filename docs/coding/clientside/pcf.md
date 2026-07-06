# PCF Controls

PCF — the [Power Apps component framework](https://learn.microsoft.com/en-us/power-apps/developer/component-framework/overview) — builds reusable, strongly-typed code components for model-driven and canvas apps.

## Scaffolding

```shell
pac pcf init --namespace dgt.PCF --name MyControl --template field
```

**`DGT-CLI-070`**{ #dgt-cli-070 } — Use `pac` (kept current via `pac install latest`) for scaffolding rather than copying an older
project's structure by hand — the template evolves with platform changes (e.g. virtual control
support, dataset vs. field control differences) faster than a guideline page can track.

## Build into a solution like any other web resource component

Once built, a PCF control's output is added to a solution the same way any other web resource
is — via `pac solution add-reference` against a locally-initialized solution, or picked up by
the normal solution packaging step in [Build Pipeline](../../alm/build-pipeline.md). Treat PCF
build output the same way as transpiled web resource JS: generated, not committed — see
[Source Control](../../alm/source-control.md#gitignore).

## XrmToolBox tooling

[PCF-CustomControlBuilder](https://github.com/Power-Maverick/PCF-CustomControlBuilder) in
XrmToolBox is a reasonable accelerator for scaffolding and iterating on a control without
leaving a GUI-first workflow — useful for customizer-adjacent PCF work, but the underlying
project it produces still follows the same structure and build conventions as one started via
`pac pcf init`.

## Linting

PCF projects scaffolded by `pac` use ESLint — enforce the
[`@microsoft/eslint-plugin-power-apps`](https://www.npmjs.com/package/@microsoft/eslint-plugin-power-apps)
rules in CI as well as locally. (Web resources lint with **Biome** instead — see
[TypeScript Web Resources](typescript-webresources.md#formatting-line-endings) — so the two
client-side project types don't share a single lint configuration.)

## Typed Dataverse access from a PCF control

Where a PCF control needs to query Dataverse directly (rather than only working with the
bound field/dataset), use the same [generated TypeScript model](model-generation.md) as form
scripts rather than constructing Web API queries by hand inside the control.
