# GitHub Actions

The GitHub-native equivalent to [Azure DevOps](azure-devops.md) — same
[build pipeline](build-pipeline.md) step order, same [versioning](versioning.md) approach,
different orchestrator. Use it per [Deployment Approach](../architecture/deployment-approach.md),
typically when the customer (or DIGITALL itself, for our own tooling repositories) standardizes
on GitHub rather than Azure DevOps.

## Building blocks

- [`microsoft/powerplatform-actions`](https://github.com/microsoft/powerplatform-actions) —
  the official action collection wrapping `pac` for export, unpack, pack, import, upgrade, and
  the Solution Checker (`checker`). Always start a workflow with
  `microsoft/powerplatform-actions/actions-install@v1` before any other Power Platform action.
- `dgtp` — installed as a regular `dotnet tool install -g dgt.power` step, same as in Azure
  DevOps; there is no dedicated GitHub Action for it.
- Standard `actions/setup-dotnet`, `actions/setup-node`, and `dotnet`/`pnpm` CLI steps for the
  client- and server-side builds (web resources use pnpm — see
  [TypeScript Web Resources](../coding/clientside/typescript-webresources.md)).

## Example workflow

For readability the example uses a single solution (`dgt_myproject_core`); a real project
pushes and exports per layered solution (see
[Solution Architecture](../architecture/solution-architecture.md)) — same steps, repeated per
solution.

```yaml title=".github/workflows/build.yml"
name: build

on:
  push:
    branches: [develop, main]
  pull_request:

jobs:
  build:
    runs-on: windows-latest
    environment: build
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: "10.x"

      - uses: actions/setup-node@v4
        with:
          node-version: "24.x"

      - name: Build client-side projects (web resources)
        working-directory: src/WebResources
        run: |
          corepack enable
          pnpm install
          pnpm run build:prod

      - name: Install dgtp CLI
        run: dotnet tool install -g dgt.power

      - name: Configure dgtp profile
        run: dgtp profile create build "${{ secrets.DATAVERSE_BUILD_CONNECTION }}" --msal

      - name: Regenerate early-bound models
        run: dgtp codegeneration ./src/Generated --config ./modelconfig.json

      - name: Bump solution version
        run: dgtp maintenance solution-version dgt_myproject_core --build

      - name: Build & pack plugin project
        run: dotnet pack src/Plugins/MyPlugins.csproj -c Release -o ./artifacts

      - name: Push plugin package
        run: dgtp push ./artifacts/MyPlugins.nupkg --solution dgt_myproject_core --publish

      - name: Push web resources
        run: dgtp push src/WebResources/dist --solution dgt_myproject_core --publish --delete-obsolete

      - name: Install Power Platform Tools
        uses: microsoft/powerplatform-actions/actions-install@v1

      - name: Export solution
        uses: microsoft/powerplatform-actions/export-solution@v1
        with:
          environment-url: ${{ vars.BUILD_ENVIRONMENT_URL }}
          app-id: ${{ secrets.BUILD_CLIENT_ID }}
          client-secret: ${{ secrets.BUILD_CLIENT_SECRET }}
          tenant-id: ${{ secrets.BUILD_TENANT_ID }}
          solution-name: dgt_myproject_core
          solution-output-file: artifacts/dgt_myproject_core.zip

      - name: Solution Checker
        uses: microsoft/powerplatform-actions/check-solution@v1
        with:
          environment-url: ${{ vars.BUILD_ENVIRONMENT_URL }}
          app-id: ${{ secrets.BUILD_CLIENT_ID }}
          client-secret: ${{ secrets.BUILD_CLIENT_SECRET }}
          tenant-id: ${{ secrets.BUILD_TENANT_ID }}
          path: artifacts/dgt_myproject_core.zip

      - name: Run unit tests
        run: dotnet test

      - uses: actions/upload-artifact@v4
        with:
          name: solution
          path: artifacts/dgt_myproject_core.zip
```

A second, separate workflow consumes this artifact and calls
`microsoft/powerplatform-actions/import-solution@v1` per target environment, gated by GitHub
Environments with required reviewers for production — the same approval mechanic Azure DevOps
environments give you.

## Secrets & authentication

- Use a **service principal per environment tier** (`BUILD_CLIENT_ID`/`TEST_CLIENT_ID`/...),
  stored as repository or environment secrets — never a personal account password, even for
  early prototyping.
- GitHub **Environments** (Settings → Environments) are the right place for
  environment-specific secrets and required-reviewer approval gates, mirroring Azure DevOps
  environments and Power Platform Pipelines' own approval step.

## Bridging to Power Platform Pipelines

If a project's Dataverse stream is deployed via
[Power Platform Pipelines](power-platform-pipelines.md) but the wider system also needs GitHub
for source control or CI on non-Dataverse components, use a **gated extension** flow to push the
pipelines-exported solution into this repository automatically (`workflow_dispatch`-triggered
export/unpack/commit workflow, invoked from a Power Automate flow on `OnDeploymentRequested`),
rather than maintaining a manual export-and-commit step. This is the same hybrid pattern
described in [ALM & Deployment](index.md).
