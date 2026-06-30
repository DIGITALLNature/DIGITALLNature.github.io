# DIGITALL Assembly (Digitall.APower)

[`Digitall.APower`](https://github.com/DIGITALLNature/DigitallAssemblyPower) is the
abstraction layer every plugin, Custom API handler, and workflow activity is built on. It
replaces boilerplate `IServiceProvider` plumbing with a small, consistent API surface, and is a
hard dependency for [registration attributes](registration-attributes.md) and
[server-side unit testing](../../testing/unit-testing-serverside.md) to work the way this
guideline describes.

```shell
dotnet add package dgt.apower
```

!!! note "Package ID vs. namespace"
    The package is published as **`dgt.apower`**; the namespace you `using` is
    **`Digitall.APower`** — they deliberately differ. Install `dgt.apower`, then write
    `using Digitall.APower;`.

## `Executor` — the base class for your plugin

Inherit from `Executor` and implement `Execute()`:

```csharp title="ContactValidationPlugin.cs"
using Digitall.APower;
using Digitall.APower.Model;   // generated early-bound model

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

`Execute()` returns an `ExecutionResult` — `Ok`, `Failure`, or `Skipped` — rather than relying
purely on exceptions for control flow. This is also what
[`Digitall.Dataverse.Testing`](../../testing/unit-testing-serverside.md) asserts against in
unit tests, so use it deliberately rather than always returning `Ok`.

`Executor.Execute(IServiceProvider)` (the actual `IPlugin.Execute`) clones itself before running
your code — following Microsoft's "plugins must be stateless" guidance — so you never need to
worry about instance fields leaking state between invocations of the same registered step.

!!! tip "Already have a plain `IPlugin`? Use `PluginCore` directly"
    You don't have to inherit `Executor` to get the typed surface. Inside any
    `IPlugin.Execute(IServiceProvider serviceProvider)` you can `new PluginCore(serviceProvider)`
    and use the same `Entity`, `SecuredOrganizationService`, `GetInputParameter<T>`, `Trace`,
    etc. — handy when a class already derives from another base or you're retrofitting an
    existing plugin. Inheriting `Executor` stays the default for new code: it adds the
    clone-per-execution and the `ExecutionResult` contract on top of that same surface.

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

`Digitall.APower` ships optional modules as separate NuGet packages, referenced only where
needed — don't add all of them by default. Each package id starts with `dgt.apower.`:

| Package | Namespace | Adds |
|---|---|---|
| `dgt.apower.enviromentvariables` | `Digitall.APower.EnvironmentVariables` | `executor.GetConfig("dgt_some_env")` — reads an environment variable's value (definition + value lookup, cached per execution) without hand-rolling the `environmentvariabledefinition`/`environmentvariablevalue` query yourself. |
| `dgt.apower.keyvault` | `Digitall.APower.Keyvault` | `executor.KeyVaultSecret(url)` — reads a secret from Azure Key Vault over REST, caching both the access token and the secret. Reads its `Azure.TenantId`, `KeyVault.ClientId`, and `KeyVault.ClientSecret` from environment variables, so it pulls in the environment-variables module. |
| `dgt.apower.sharepoint` | `Digitall.APower.Sharepoint` | `ISharepointService` — SharePoint REST helpers (request digest, file/folder read & create, list-item update, permissions). |

!!! warning "The Key Vault package id is misspelled"
    The environment-variables package is published as `dgt.apower.enviromentvariables` — with a
    typo, "enviroment" missing its second *n*. Install it exactly as written; only the package
    id is affected, the namespace is spelled correctly.

The OAuth token helpers the Key Vault module relies on (`GetMicrosoftOnlineAccessToken`,
`GetAccessControlAccessToken`) live in an internal **shared project** (`dgt.apower.http`) that
is compiled *into* the Key Vault and SharePoint packages — there is no standalone `http`
package to add.

!!! warning "Prefer managed identity over a stored client secret"
    The Key Vault module authenticates with a `KeyVault.ClientSecret` read from a Dataverse
    environment variable — but environment-variable *values* are not a secret store (readable by
    anyone with access, not encrypted). Where the target supports it, use a
    [managed identity](custom-api.md#managed-identity) instead of a stored secret; if a client
    secret is unavoidable, treat that environment variable as sensitive and rotate it.

Telemetry is **not** a separate module: `PluginCore` already exposes an `ILogger`
(`PluginTelemetry`) wired to the [Application Insights](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/application-insights-ilogger) export configured in the Power Platform
admin center, so structured logging is available from the core package without an add-on.

## Custom APIs and workflow activities

The same `Executor` base and `PluginCore` surface apply to Custom API handlers and workflow
activities, not just classic plugins — see [Custom API & Data Providers](custom-api.md) for the
Custom-API-specific parts (input/output parameter typing via `GetInputParameter`/
`SetOutputParameter`, and the `[CustomApiRegistration]` attribute).
