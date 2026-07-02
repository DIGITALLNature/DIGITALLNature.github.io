# Project Setup

## Target framework

**`DGT-SRV-020`**{ #dgt-srv-020 } — Plugin and Custom API assemblies target **`net462`** — this is a Dataverse
platform constraint, not a project choice, and applies regardless of which .NET SDK you build with.

!!! warning "Looking ahead: .NET Framework 4.8 plugin runtime"
    Microsoft has announced Dataverse plugin support for the **.NET Framework 4.8 runtime**
    targeted for Q4 2026, and official support for .NET Framework 4.6.2 itself ends
    **January 12, 2027**. Don't change `TargetFramework` yet on the strength of the
    announcement alone, but budget time to revisit this guideline and migrate projects once
    4.8 support actually ships — treat `net462` here as "current platform requirement", not as
    a permanent decision baked into tooling or templates.

## Project format

**`DGT-SRV-030`**{ #dgt-srv-030 } — Use the **SDK-style project format** (`pac plugin init`-generated, or the
SDK-style template manually) for new projects, not the legacy non-SDK `.csproj`. This is also the format required
to use [plugin packages](plugin-packages.md).

```xml title="MyPlugins.csproj"
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net462</TargetFramework>
    <Nullable>disable</Nullable>
    <AssemblyName>dgt.Plugin.MyEntity</AssemblyName>
    <RootNamespace>dgt.Plugin.MyEntity</RootNamespace>
    <LangVersion>latest</LangVersion>
    <RestorePackagesWithLockFile>true</RestorePackagesWithLockFile>
    <!-- Plugin packages do not require signing; see Plugin Packages -->
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.CrmSdk.CoreAssemblies" Version="*" PrivateAssets="all" />
    <PackageReference Include="Digitall.Plugins" Version="*" />
    <PackageReference Include="Digitall.Plugins.Registration" Version="*" />
  </ItemGroup>

</Project>
```

!!! note "Why CoreAssemblies, not Dataverse.Client"
    **`DGT-SRV-040`**{ #dgt-srv-040 } — The Dataverse SDK assemblies already exist in the plug-in sandbox, so they
    must **not** be shipped inside the package — `PrivateAssets="all"` references them at build time but keeps
    them out of the `.nupkg` (including them otherwise errors on package registration).
    `Microsoft.PowerPlatform.Dataverse.Client` is the `ServiceClient` for *external* apps and
    pulls in a large dependency tree that doesn't belong in a plug-in. See Microsoft's
    [Build and package plug-in code](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/build-and-package).

## Naming

| Item | Pattern | Example |
|---|---|---|
| Assembly name | `prfx.[Plugin\|CustomApi\|Workflow].[Entity\|Service\|Name]` | `dgt.Plugin.Invoice` |
| Default namespace | same as assembly name | `dgt.Plugin.Invoice` |
| Project (folder) name | `[Entity\|Service\|Name]` | `Invoice` |

## One project per logical unit, versioned independently

**`DGT-SRV-060`**{ #dgt-srv-060 } — Each plugin/Custom API/workflow "group" lives in its own assembly, including
its own version — don't merge unrelated functional areas into one assembly just because they're both plugins.
This keeps the version bump described in [Versioning](../../alm/versioning.md) meaningful: a
version increase tells you specifically what changed, not "something somewhere in the project
changed."

## `.editorconfig`

Use a shared `.editorconfig` at the repository root (referenced from
[Source Control](../../alm/source-control.md)) rather than per-project files, so Rider/Visual
Studio/`dotnet format` apply the same rules everywhere:

```ini title=".editorconfig (excerpt)"
root = true

[*]
indent_style = space
indent_size = 4
insert_final_newline = true
trim_trailing_whitespace = true
max_line_length = 200

[*.cs]
csharp_new_line_before_open_brace = all
csharp_prefer_braces = true:silent
dotnet_style_qualification_for_field = false:suggestion
csharp_style_var_for_built_in_types = true:suggestion
csharp_style_var_when_type_is_apparent = true:none
csharp_style_var_elsewhere = true:suggestion
dotnet_naming_rule.constant_fields_should_be_pascal_case.severity = suggestion
dotnet_naming_rule.constant_fields_should_be_pascal_case.symbols  = constant_fields
dotnet_naming_rule.constant_fields_should_be_pascal_case.style    = pascal_case_style
dotnet_naming_symbols.constant_fields.applicable_kinds   = field
dotnet_naming_symbols.constant_fields.required_modifiers = const
dotnet_naming_style.pascal_case_style.capitalization = pascal_case
dotnet_naming_rule.camel_case_for_private_internal_fields.severity = suggestion
dotnet_naming_rule.camel_case_for_private_internal_fields.symbols  = private_internal_fields
dotnet_naming_rule.camel_case_for_private_internal_fields.style    = camel_case_underscore_style
dotnet_naming_symbols.private_internal_fields.applicable_kinds = field
dotnet_naming_symbols.private_internal_fields.applicable_accessibilities = private, internal
dotnet_naming_style.camel_case_underscore_style.required_prefix = _
dotnet_naming_style.camel_case_underscore_style.capitalization = camel_case

[*.{csproj,proj}]
indent_size = 2

[*.{yml,yaml}]
indent_size = 2
```

See [Templates](../../reference/templates.md) for the full file.

## Code style baseline

- Visual Studio default formatting (<kbd>Ctrl</kbd>+<kbd>K</kbd>, <kbd>Ctrl</kbd>+<kbd>D</kbd>)
  or the Rider/ReSharper equivalent — don't hand-tune formatting beyond what `.editorconfig`
  already encodes.
- Follow standard .NET code analysis recommendations (Roslyn analyzers / ReSharper or Rider
  inspections both surface the same baseline) rather than a DIGITALL-specific rule set.

## Signing

**`DGT-SRV-050`**{ #dgt-srv-050 } — Plugin packages (the default — see [Plugin Packages](plugin-packages.md))
**do not require signing**. Only sign assemblies if a project is still on the classic single-assembly
registration format, in which case follow the standard strong-name signing approach
(`AssemblyOriginatorKeyFile`, a `key.snk` committed per
[Source Control](../../alm/source-control.md)).
