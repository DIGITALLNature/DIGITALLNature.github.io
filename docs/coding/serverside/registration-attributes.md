# Registration Attributes

**`DGT-SRV-100`**{ #dgt-srv-100 } — [`Digitall.Plugins.Registration`](https://github.com/DIGITALLNature/DigitallRegistrationPower)
replaces manual step registration in the Plugin Registration Tool with declarative C#
attributes. **This is the registration source of truth** — what you see on a class is what
[`dgtp push`](../../alm/pre-post-deployment.md#dgtp-push-in-depth) reconciles into Dataverse,
including removing steps whose attribute was deleted. Don't hand-register a step in the Plugin
Registration Tool on top of this; the next `dgtp push` won't know about it and won't preserve
it.

```shell
dotnet add package Digitall.Plugins.Registration
```

```csharp
using Digitall.Dataverse.Model;       // SdkMessageNames, early-bound entities
using Digitall.Plugins.Registration;
```

## `[PluginRegistration]` — standard plugin steps

Stack multiple attributes on one class to register multiple steps from a single plugin.

**Mandatory (constructor) parameters:** `mode` (`PluginExecutionMode.Synchronous` /
`Asynchronous`), `messageName` (e.g. `"Create"`, `"Update"`), `stage`
(`PluginExecutionStage.PreValidation` / `PreOperation` / `MainOperation` / `PostOperation`).

**Optional named parameters:** `PrimaryEntityName`, `SecondaryEntityName`, `FilterAttributes`
(step only fires if one of these changed), `ExecutionOrder` (default `100`), `PreEntityImage` /
`PreEntityImageAttributes`, `PostEntityImage` / `PostEntityImageAttributes`, `Configuration`
(unsecure config string).

```csharp
[PluginRegistration(PluginExecutionMode.Asynchronous, SdkMessageNames.Create, PluginExecutionStage.PreOperation,
    PrimaryEntityName = Account.EntityLogicalName, ExecutionOrder = 10)]
[PluginRegistration(PluginExecutionMode.Synchronous, SdkMessageNames.Update, PluginExecutionStage.PostOperation,
    PrimaryEntityName = Account.EntityLogicalName,
    FilterAttributes = new[] { Account.LogicalNames.Name },
    PreEntityImage = true, PreEntityImageAttributes = new[] { Account.LogicalNames.Name })]
public class SamplePlugin : IPlugin
{
    public void Execute(IServiceProvider serviceProvider)
    {
        // ...
    }
}
```

The pre-image registered here is named `"PreImage"` and the post-image `"PostImage"` —
matching what `Executor.PreEntityImage`/`PostEntityImage` read, see
[DIGITALL Assembly](digitall-assembly.md).

## `[CustomApiRegistration]` — Custom API handlers

```csharp
[CustomApiRegistration("dgt_calc_vacations")]
public class CalcVacationsPlugin : IPlugin
{
    public void Execute(IServiceProvider serviceProvider)
    {
        // ...
    }
}
```

See [Custom API & Data Providers](custom-api.md) for the Custom API definition side (request
parameters, response parameters) that this handler attribute pairs with.

## `[CustomDataProviderRegistration]` — virtual table providers

Stack one attribute per event handled:

```csharp
[CustomDataProviderRegistration("dgt_virtual_table", DataProviderEvent.Create)]
[CustomDataProviderRegistration("dgt_virtual_table", DataProviderEvent.Update)]
public class HandleUpsertOnVirtualTable : IPlugin
{
    public void Execute(IServiceProvider serviceProvider)
    {
        // ...
    }
}
```

`DataProviderEvent` covers `Retrieve`, `RetrieveMultiple`, `Create`, `Update`, `Delete`.

## `[WorkflowRegistration]` — custom workflow activities

```csharp
[WorkflowRegistration("Sample", "SampleGroup")]
public class SampleWorkflow : CodeActivity
{
    [Input(nameof(Email))]
    [RequiredArgument]
    [ReferenceTarget("email")]
    public InArgument<EntityReference> Email { get; set; }

    protected override void Execute(CodeActivityContext context)
    {
        // ...
    }
}
```

`group` defaults to `"DGT"` if omitted — set it explicitly to the project's own group name so
workflow activities show up under a recognizable category in the classic workflow designer,
rather than lumped under the tooling's own default.

## `[ManagedIdentityRegistration]` — assembly-level, for Managed Identity auth

Applied once, at assembly level, to associate a managed identity with the plugin assembly or
package:

```csharp
[assembly: ManagedIdentityRegistration("00000000-0000-0000-0000-000000000000")]
```

This only covers the Dataverse-side registration. You still need to create the managed
identity in Azure and sign the assembly/package yourself — see Microsoft's
[Managed Identity overview](https://learn.microsoft.com/en-us/power-platform/admin/managed-identity-overview)
for that part; it isn't something `dgtp` or this attribute automates.

## Why this matters for ALM

Because registration lives in code, **a deviation between what's registered in Dataverse and
what's in source control is itself a bug** — not an expected drift you reconcile manually. If
you suspect drift (e.g. after someone registered something by hand in the Plugin Registration
Tool), the fix is to run `dgtp push` again, not to edit the registration in Dataverse directly.
