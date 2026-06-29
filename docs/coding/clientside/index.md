# Coding — Client-side

Form scripts, ribbon/command bar scripts, and PCF controls, all written in TypeScript.

- [TypeScript Web Resources](typescript-webresources.md) — project setup and structure for
  form/library scripts.
- [PCF Controls](pcf.md) — PowerApps Component Framework setup.
- [TS Model Generation](model-generation.md) — generating typed models with `dgtp`.

## Baseline tooling

| Tool | Notes |
|---|---|
| Node.js LTS | Pinned via `.nvmrc`/`engines`, not hardcoded in this guideline — see [Toolchain](../../setup/toolchain.md). |
| `npm` (latest) | |
| `tsc` (latest, or whatever the project's bundler pulls in) | |
| `pac` (latest) | For PCF scaffolding (`pac pcf init`). |
| [`@microsoft/eslint-plugin-power-apps`](https://www.npmjs.com/package/@microsoft/eslint-plugin-power-apps) | Lint rules specific to Power Apps client-side code; enforce in CI, not just locally. |

Use whatever the current LTS/stable versions are at project start — don't copy a specific
historic version number from an old project into a new one.
