# Repository Bootstrap

What a developer runs locally after cloning a project repository for the first time, mirroring
the early steps of [Build Pipeline](../alm/build-pipeline.md) but against their own dev
environment instead of a CI build environment.

## First-time setup

```shell
git clone <repository-url>
cd <repository>

# .NET / server-side
dotnet restore

# Node / client-side (web resources use pnpm; see webresources-template)
cd src/WebResources
pnpm install
cd ../..

# dgtp
dotnet tool install -g dgt.power
dgtp profile create dev "<your dev environment connection string>"
dgtp profile select dev
```

## Pulling the current schema locally

```shell
dgtp codegeneration ./src/Generated --config ./modelconfig.json
```

This regenerates the early-bound `.cs`/`.ts` models against your selected environment — run it
whenever you pull changes that include schema updates, before opening the solution in your IDE,
the same way you'd restore NuGet packages after a dependency change. See
[Early-Bound Models](../coding/serverside/early-binding.md).

## Syncing the unpacked solution

```shell
pac solution sync --solutionFolder solutions/<SolutionUniqueName>
```

See [Source Control](../alm/source-control.md) for why this is committed and how often to run
it.

## Pushing your own changes for local testing

```shell
dotnet build src/Plugins/MyPlugins.csproj -c Debug
dgtp push src/Plugins/MyPlugins/bin/Debug/MyPlugins.nupkg --solution <project-solution> --publish

pnpm --dir src/WebResources run build
dgtp push src/WebResources/dist --solution <project-solution> --publish
```

This is the same `dgtp push` mechanism CI uses — see
[Pre- & Post-Deployment Tasks](../alm/pre-post-deployment.md#dgtp-push-in-depth) for exactly
what it does. Running it locally against your own dev environment during a feature loop is the
intended use; only CI bumps solution/assembly versions (see
[Versioning](../alm/versioning.md)), so don't run `dgtp maintenance solution-version` against a
shared environment from your workstation.

