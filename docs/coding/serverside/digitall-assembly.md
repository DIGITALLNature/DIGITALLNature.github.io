# DIGITALL Assembly (Digitall.APower)

[`Digitall.APower`](https://github.com/DIGITALLNature/DigitallAssemblyPower) is the
abstraction layer every plugin, Custom API handler, and workflow activity is built on. It
replaces boilerplate `IServiceProvider` plumbing with a small, consistent API surface, and is a
hard dependency for [registration attributes](registration-attributes.md) and
[server-side unit testing](../../testing/unit-testing-serverside.md) to work the way this
guideline describes.

```shell
dotnet add package Digitall.APower
```

## `Executor` — the base class for your plugin

Inherit from `Executor` and implement `Execute()`:

```csharp title="InvoiceNumberPlugin.cs"
using Digitall.APower;

namespace dgt.Plugin.Invoice;

public class InvoiceNumberPlugin : Executor
{
    protected override ExecutionResult Execute()
    {
        var invoice = Entity;

        if (!invoice.Contains("dgt_number"))
        {
            return ExecutionResult.Skipped;
        }

        // ... your logic, using the members below ...

        return ExecutionResult.Ok;
    }
}
```

`Execute()` returns an `ExecutionResult` — `Ok`, `Failure`, or `Skipped` — rather than relying
purely on exceptions for control flow. This is also what
[`Digitall.Dataverse.Testing`](../../testing/unit-testing-serverside.md) asserts against in
unit tests, so use it deliberately rather than always returning `Ok`.

`Executor.Execute(IServiceProvider)` (the actual `IPlugin.Execute`) clones itself before running
your code — following Microsoft's "plugins must be stateless" guidance — so you never need to
worry about instance fields leaking state between invocations of the same registered step.

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
| `Trace(format, args)` | Wraps `ITracingService`. |
| `ProcessName` | A ready-made `"CRM.{Type}.{Message}.{Mode}.{Stage}.{Depth}"` string for logging/telemetry correlation. |

Prefer `SecuredOrganizationService`/`ElevatedOrganizationService` over calling
`OrganizationService(...)` directly — the two named properties are cached per execution and
communicate intent (running as caller vs. running elevated) at the call site.

## Add-on modules

`Digitall.APower` ships optional modules as separate packages, referenced only where needed —
don't add all of them by default:

| Module | Adds |
|---|---|
| `Digitall.APower.EnvironmentVariables` | `executor.GetConfig("dgt_some_env")` — reads an environment variable's value (definition + value lookup, cached per execution) without hand-rolling the `environmentvariabledefinition`/`environmentvariablevalue` query yourself. |
| `Digitall.APower.Keyvault` | Secret retrieval from Azure Key Vault. |
| `Digitall.APower.Sharepoint` | SharePoint REST integration helpers. |
| (Logging / Telemetry / ServiceBus, where present) | Structured logging and messaging integrations — check the package list for what's current. |

## Custom APIs and workflow activities

The same `Executor` base and `PluginCore` surface apply to Custom API handlers and workflow
activities, not just classic plugins — see [Custom API & Data Providers](custom-api.md) for the
Custom-API-specific parts (input/output parameter typing via `GetInputParameter`/
`SetOutputParameter`, and the `[CustomApiRegistration]` attribute).
