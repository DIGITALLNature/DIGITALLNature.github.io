# DIGITALL Assembly (Digitall.Plugins)

[`Digitall.Plugins`](https://github.com/DIGITALLNature/DigitallAssemblyPower) is the
abstraction layer every plugin, Custom API handler, and workflow activity is built on. It
replaces boilerplate `IServiceProvider` plumbing with a small, consistent API surface, and is a
hard dependency for [registration attributes](registration-attributes.md) and
[server-side unit testing](../../testing/unit-testing-serverside.md) to work the way this
guideline describes.

```shell
dotnet add package Digitall.Plugins
```

!!! note "Migrating from `dgt.apower`"
    Versions before `2.0.0` shipped as package `dgt.apower` with namespace `Digitall.APower`,
    plus separate `dgt.apower.keyvault`/`dgt.apower.sharepoint`/`dgt.apower.enviromentvariables`
    add-on packages. As of `2.0.0` the package was renamed to **`Digitall.Plugins`** (package ID
    now matches the namespace), the Key Vault and SharePoint modules were **removed**, and
    environment-variable access moved into the core package — see
    [Add-on modules](#add-on-modules) below.

!!! info "`PluginSkeleton` vs. `Executor`"
    The package's own README now lists **`PluginSkeleton`** first and labels **`Executor`** a
    "legacy-compatible base class" — `PluginSkeleton` is the maintainers' recommended starting
    point for new plugins. This guideline still teaches `Executor` below because most other
    server-side pages ([Custom API](custom-api.md), [Patterns](patterns.md),
    [Testing](../../testing/unit-testing-serverside.md)) build on its typed-property surface, and
    `Executor` remains fully supported, not deprecated. If you're starting a new project and
    don't need that typed surface, prefer `PluginSkeleton` (see the tip below) — the choice
    doesn't affect [registration attributes](registration-attributes.md) or `dgtp push`, which
    work the same either way.

## `Executor` — the base class for your plugin

Inherit from `Executor` and implement `Execute()`:

```csharp title="ContactValidationPlugin.cs"
using Digitall.Plugins;
using Digitall.Dataverse.Model;   // generated early-bound model

namespace dgt.Plugin.Contacts;

public class ContactValidationPlugin : Executor
{
    protected override ExecutionResult Execute()
    {
        var contact = Entity.ToEntity<Contact>();

        if (!contact.BirthDate.HasValue)
        {
            return ExecutionResult.Skipped;
        }

        // ... your logic, using the typed members below ...

        return ExecutionResult.Ok;
    }
}
```

**`DGT-SRV-080`**{ #dgt-srv-080 } — `Execute()` returns an `ExecutionResult` — `Ok`, `Failure`, or `Skipped` —
rather than relying purely on exceptions for control flow. This is also what
[`Digitall.Dataverse.Testing`](../../testing/unit-testing-serverside.md) asserts against in
unit tests, so use it deliberately rather than always returning `Ok`.

`Executor.Execute(IServiceProvider)` (the actual `IPlugin.Execute`) clones itself before running
your code — following Microsoft's "plugins must be stateless" guidance — so you never need to
worry about instance fields leaking state between invocations of the same registered step.

!!! tip "Prefer a lighter base, or already have a plain `IPlugin`? Use `PluginSkeleton`"
    `PluginSkeleton` is the package's recommended abstract `IPlugin` base for new plugins — it
    wraps your logic with structured start/end/failure logging and timing (auto-logged to both
    `PluginTelemetry` and `ITracingService`) built in, while leaving you working against the raw
    `IServiceProvider`/`IPluginExecutionContext` inside `ExecuteInternal(IServiceProvider)` —
    typed access comes from extension methods such as `executionContext.GetTarget<T>()`,
    `GetPreImage<T>()`/`GetPostImage<T>()`, `GetInputParameter`/`SetOutputParameter`, and
    `serviceProvider.GetOrganizationService()`/`GetElevatedOrganizationService()`/`GetLogger()`/
    `GetManagedIdentityService()`/`GetTimeProvider()`. Also useful when a class already derives
    from another base or you're retrofitting an existing plugin — see
    `examples/SamplePlugin/SkeletonSample.cs` in the AssemblyPower repo for a full walkthrough.
    `Executor` (documented on this page) stays a fully-supported alternative: it adds
    clone-per-execution behavior and the `ExecutionResult` contract on top of the same
    capabilities, exposed as properties instead of extension methods — and it's what the rest of
    this guideline's server-side examples use.

## What `Executor` gives you, without touching `IServiceProvider` directly

| Member | Purpose |
|---|---|
| `Entity` / `EntityReference` | The `Target` input parameter, typed for you. |
| `PreEntityImage` / `PostEntityImage` | The `"PreImage"`/`"PostImage"` entity images — named to match what [registration attributes](registration-attributes.md) register them as. |
| `SecuredOrganizationService` | `IOrganizationService` running as the calling user. |
| `ElevatedOrganizationService` | `IOrganizationService` running with elevated (system) privileges. |
| `Stage` / `Mode` / `Depth` | Pipeline stage, sync/async mode, and recursion depth as readable values instead of raw integers. |
| `GetInputParameter<T>` / `GetOutputParameter<T>` / `SetOutputParameter<T>` | Generic, typed access to the message request/response parameters — used for Custom APIs and actions. |
| `Query(out QueryExpression, out ColumnSet)` (+ `QueryByAttribute`/`FetchExpression` overloads) | Typed access to a Custom API's `Query` input, with column set inference. |
| `Core` | The raw `IPluginExecutionContext`, for anything not surfaced as a typed property above. |
| `ProcessName` | A ready-made `"CRM.{Type}.{Message}.{Mode}.{Stage}.{Depth}"` string for logging/telemetry correlation. |

`Executor` itself doesn't wrap a `Trace(...)` convenience method — call
`ServiceProvider.GetTracingService()` or `ServiceProvider.GetLogger()` directly (see
[Logging](#add-on-modules) below) for step-by-step diagnostic output.

**`DGT-SRV-090`**{ #dgt-srv-090 } — Prefer `SecuredOrganizationService`/`ElevatedOrganizationService` over calling
`OrganizationService(...)` directly — the two named properties are cached per execution and
communicate intent (running as caller vs. running elevated) at the call site.

## Comparing target vs. pre-image

`executor.GetEntityAttributeValue<T>(attribute)`, `IsEntityAttributeValueNew(attribute)`,
`IsEntityAttributeValueChanged<T>(attribute)`, `IsEntityAttributeValueNullOrEmpty(attribute)`,
and `MergeEntity<T>()` (also available on the raw `IPluginExecutionContext`) cover the common
"what actually changed" checks against `Entity`/`PreEntityImage` without writing the
`Contains`/`null`-checking boilerplate by hand — see
`src/Digitall.Plugins/Extensions/EntityAttributeExtension.cs` in the AssemblyPower repo for the
exact semantics of each.

## Add-on modules

As of `2.0.0`, `Digitall.Plugins` is a **single package** — the separate Key Vault and
SharePoint add-on modules from the `dgt.apower.*` era have been removed outright (no REST-based
`KeyVaultSecret(url)` or `ISharepointService` helpers exist anymore); if you still need Key
Vault or SharePoint access, call the respective SDKs/REST APIs directly.

Environment-variable access is no longer a separate add-on — it's built into the core package:

```csharp
var value = executor.GetConfig("dgt_some_env");           // Executor, via extension method
var value = serviceProvider.GetConfig("dgt_some_env");    // raw IServiceProvider (e.g. PluginSkeleton)
```

`GetConfig` reads an environment variable's value (definition + value lookup, elevated,
`defaultvalue` fallback) without hand-rolling the `environmentvariabledefinition`/
`environmentvariablevalue` query yourself — see
`src/Digitall.Plugins/Extensions/EnvironmentVariablesExtension.cs` in the AssemblyPower repo.

Telemetry is **not** a separate module either — `ServiceProvider` (on both `Executor` and a raw
`IServiceProvider`) exposes two logging entry points, deliberately kept distinct:

| Call | Returns | Writes to |
|---|---|---|
| `GetLogger()` | `Microsoft.Xrm.Sdk.PluginTelemetry.ILogger` | [Application Insights](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/application-insights-ilogger) `PluginTelemetry` only |
| `GetLogger(params LogSink[] sinks)` | `Microsoft.Extensions.Logging.ILogger` | `PluginTelemetry`, `ITracingService`, or both, depending on the sinks passed — no sinks defaults to `ITracingService` |

Prefer the standard `Microsoft.Extensions.Logging.ILogger` overload (`GetLogger(sinks)`) for new
code — it lets you change sinks later without touching call sites, and it's what
`PluginSkeleton` (see the tip above) uses internally to auto-log start/end/failure around every
execution.

## Custom APIs and workflow activities

The same `Executor` base (or `PluginSkeleton`/extension-method surface) applies to Custom API
handlers and workflow activities, not just classic plugins — see
[Custom API & Data Providers](custom-api.md) for the
Custom-API-specific parts (input/output parameter typing via `GetInputParameter`/
`SetOutputParameter`, and the `[CustomApiRegistration]` attribute).
