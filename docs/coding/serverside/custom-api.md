# Custom API & Data Providers

## Custom APIs

Prefer **[Custom APIs](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/custom-api)** over classic unbound/bound Actions for new server-side operations
callable from client code, Power Automate, or other plugins — they have first-class typed
request/response parameters and show up properly in the Dataverse Web API metadata.

A Custom API has two parts in Dataverse customization (the table/message definition, its
request/response parameters — see [Customizing](../../customizing/index.md) for that side) and
one part in code: the handler plugin, registered via
[`[CustomApiRegistration]`](registration-attributes.md#customapiregistration-custom-api-handlers):

```csharp
[CustomApiRegistration("dgt_calc_vacations")]
public class CalcVacationsPlugin : Executor
{
    protected override ExecutionResult Execute()
    {
        GetInputParameter("StartDate", out DateTime startDate);
        GetInputParameter("EndDate", out DateTime endDate);

        var vacationDays = CalculateVacationDays(startDate, endDate);

        SetOutputParameter("VacationDays", vacationDays);

        return ExecutionResult.Ok;
    }

    private static int CalculateVacationDays(DateTime start, DateTime end) =>
        // ...
        0;
}
```

`GetInputParameter`/`SetOutputParameter` come from [`Executor`](digitall-assembly.md) — there's
no need to reach into `IServiceProvider`/`IPluginExecutionContext` directly for a Custom API
handler any more than for a classic plugin.

## Custom Data Providers (virtual tables)

For [virtual table](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/virtual-entities/get-started-ve) data sources, implement one handler per CRUD/retrieve event and register each
with [`[CustomDataProviderRegistration]`](registration-attributes.md#customdataproviderregistration-virtual-table-providers):

```csharp
[CustomDataProviderRegistration("dgt_virtual_table", DataProviderEvent.Retrieve)]
[CustomDataProviderRegistration("dgt_virtual_table", DataProviderEvent.RetrieveMultiple)]
public class VirtualTableReadHandler : Executor
{
    protected override ExecutionResult Execute()
    {
        if (Query(out QueryExpression query, out var columnSet))
        {
            // build and return the EntityCollection / Entity for RetrieveMultiple / Retrieve
        }

        return ExecutionResult.Ok;
    }
}
```

`Executor.Query(...)` (see [DIGITALL Assembly](digitall-assembly.md)) handles the
`QueryExpression`/`QueryByAttribute`/`FetchExpression` overloads the data provider pipeline can
hand you, including column set inference from a `FetchXml` query — you don't need to branch on
query type by hand before extracting columns.

## Managed Identity

If a Custom API or plugin needs to call out to another Azure service using a managed identity
rather than a stored secret, mark the assembly with
[`[ManagedIdentityRegistration]`](registration-attributes.md#managedidentityregistration-assembly-level-for-managed-identity-auth)
— but remember that attribute only covers the Dataverse-side registration; the Azure-side
managed identity and assembly signing are separate, manual setup steps.
