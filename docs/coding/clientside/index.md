# Coding — Client-side

Form scripts, ribbon/command bar scripts, and PCF controls, all written in TypeScript.

- [TypeScript Web Resources](typescript-webresources.md) — project setup and structure for
  form/library scripts.
- [PCF Controls](pcf.md) — PowerApps Component Framework setup.
- [TS Model Generation](model-generation.md) — generating typed models with `dgtp`.

## Baseline tooling

| Tool | Notes |
|---|---|
| Node.js | Current LTS; the [web-resources template](typescript-webresources.md) targets **24.x**. Pin per project, don't hardcode here — see [Toolchain](../../setup/toolchain.md). |
| **pnpm** | Package manager for the web-resources template (enforced via `only-allow pnpm`). PCF projects scaffolded by `pac` use `npm`. |
| `tsc` (via the project's bundler) | |
| **Biome** | Lint **and** format for web resources — see [TypeScript Web Resources](typescript-webresources.md#formatting-line-endings). |
| `pac` (latest) | For PCF scaffolding (`pac pcf init`). |
| [`@microsoft/eslint-plugin-power-apps`](https://www.npmjs.com/package/@microsoft/eslint-plugin-power-apps) | Power Apps-specific lint rules, used for **PCF** controls (see [PCF Controls](pcf.md)). Web resources use Biome instead. |

Use whatever the current LTS/stable versions are at project start — don't copy a specific
historic version number from an old project into a new one.
