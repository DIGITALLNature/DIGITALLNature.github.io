# Plugin Packages

**`DGT-SRV-010`**{ #dgt-srv-010 } — Plugin packages (NuGet-based **dependent assemblies**) are the default for all
new server-side projects. **ILMerge is not allowed** — it was never officially supported by Dataverse, is no
longer maintained upstream, and plugin packages provide the same capability (and more) through
a supported mechanism.

## Why plugin packages

- Bundle your plugin assembly together with any NuGet dependencies into a single `.nupkg`,
  uploaded as one unit and stored in the `PluginPackage` table.
- No signing required.
- Supports including non-assembly resources (e.g. localized string files) alongside code,
  something ILMerge never handled cleanly.
- This is the path Microsoft is actively investing in; classic single-assembly registration is
  legacy at this point, not a parallel first-class option.

## Creating a project

```shell
pac plugin init --outputDirectory ./src/Plugins/MyPlugins --skip-signing
```

`--skip-signing` is the right default here — signing is for the classic format, not plugin
packages. If you're converting an existing classic plugin project instead of starting fresh,
expect to: switch to the SDK-style project format, re-add only the assembly references your
code actually needs (don't carry over a long historical reference list), and verify the
resulting `.nupkg` contents.

See [Project Setup](project-setup.md) for the resulting `.csproj` shape.

## Inspecting the package

Use [NuGet Package Explorer](https://github.com/NuGet/NuGetGallery/wiki/NuGet-Package-Explorer)
to drag-and-drop the built `.nupkg` and confirm its contents match expectations — in particular,
check for accidentally-included transitive dependencies (e.g. `System.Web.Extensions`) that
inflate the package without being needed; mark such references `Private=false` / not
copy-local if they're satisfied by the sandbox runtime already.

## Size limits

- A plugin assembly (including custom workflow activities) must stay under **16 MB**.
- As a best practice, consolidate related plugins and workflow activities into a single
  assembly as long as you stay under that limit — see
  [Solution Architecture](../../architecture/solution-architecture.md) for how this interacts
  with solution layering.
- Packages with hundreds to thousands of `IPlugin` types take noticeably longer to import
  (observed: ~15 minutes for a thousand types) — this applies to every import path (API,
  web UI, Plugin Registration Tool), so don't assume a slow import is specific to one
  deployment method.

## Registering and updating

Use [`dgtp push`](../../alm/pre-post-deployment.md#dgtp-push-in-depth) rather than the Plugin
Registration Tool for anything beyond local, one-off experimentation:

```shell
dgtp push ./bin/Release/MyPlugins.1.4.27.nupkg --solution dgt_myproject_core --publish
```

`dgtp push` detects whether the package already exists in the target environment (by name and
version) and creates or updates it accordingly, then reconciles plugin types, steps, and step
images against your [registration attributes](registration-attributes.md). See
[Versioning](../../alm/versioning.md) for why the package version must increase on every
pushed build, including local dev-loop pushes.

## What this replaces

| Legacy approach | Replaced by |
|---|---|
| ILMerge / ILRepack | Plugin package dependent assemblies |
| Shared Projects for code reuse | NuGet package references inside the plugin package |
| Manual step registration in the Plugin Registration Tool | [Registration Attributes](registration-attributes.md) + `dgtp push` |
