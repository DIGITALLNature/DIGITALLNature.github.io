# Server-side Unit Testing

Use [`Digitall.Dataverse.Testing`](https://github.com/DIGITALLNature/DigitallTesting) for
plugin and Custom API unit tests. It provides an in-memory `IOrganizationService` (and
`IOrganizationServiceAsync2`) implementation, so tests run fast, deterministically, and without
a connection to a live Dataverse environment — which also means they run safely in CI without
needing environment credentials at all for this step.

This **replaces** the previous generation's MSTest/NSubstitute-against-a-live-sandbox
recommendation — keep your test *framework* choice (xUnit, NUnit, MSTest all work, since
`Digitall.Dataverse.Testing` doesn't mandate one), but stop asserting against a real
environment for unit-level plugin tests.

```shell
dotnet add package Digitall.Dataverse.Testing
```

Requires .NET SDK 10.0+ for the test project itself (check the package's `global.json` for the
exact pinned version) — this is independent of the `net462` target of the plugin project under
test.

## Basic CRUD test

```csharp
using Microsoft.Xrm.Sdk;
using Microsoft.Xrm.Sdk.Query;
using Digitall.Dataverse.Testing;

public class AccountTests
{
    [Test]
    public void Should_CreateAndRetrieveEntity()
    {
        var service = new FakeDataverseBuilder().GetOrganizationService();

        var account = new Entity("account") { ["name"] = "Contoso Ltd" };

        var id = service.Create(account);
        var retrieved = service.Retrieve("account", id, new ColumnSet("name"));

        Assert.That(retrieved["name"], Is.EqualTo("Contoso Ltd"));
    }
}
```

Use early-bound entities (matching [Early-Bound Models](../coding/serverside/early-binding.md))
by marking the test assembly with `[assembly: Microsoft.Xrm.Sdk.Client.ProxyTypesAssembly]` —
the fake service auto-discovers early-bound types via reflection.

## Testing a plugin end to end

`FakePluginContextBuilder` combines an `IServiceProvider` builder with an auto-configured fake
organization service — this is the entry point for testing an
[`Executor`](../coding/serverside/digitall-assembly.md)-derived plugin without touching
Dataverse:

```csharp
[Test]
public void Should_SetInvoiceNumber_OnCreate()
{
    var builder = new FakePluginContextBuilder();
    var service = builder.GetOrganizationService();

    var target = new Entity("dgt_invoice");

    var serviceProvider = builder
        .WithMessageName("Create")
        .WithTarget(target)
        .BuildServiceProvider();

    var plugin = new InvoiceNumberPlugin();
    plugin.Execute(serviceProvider);

    Assert.That(plugin.Result, Is.EqualTo(ExecutionResult.Ok));
}
```

`plugin.Result` — the `ExecutionResult` your `Execute()` override returned — is directly
assertable; see [Patterns & Pitfalls](../coding/serverside/patterns.md#prefer-executionresultskipped-over-silent-no-ops)
for why returning a meaningful `ExecutionResult` (not always `Ok`) makes this assertion useful.

## Pre-seeding data and metadata

```csharp
var service = new FakeDataverseBuilder()
    .AddData(existingAccount, relatedContact)
    .AddEntityMetadata(accountMetadata)
    .AddRelationships(accountToContactRelationship)
    .LoadMetadata("./metadata/")
    .WithUserId(testUserId)
    .GetOrganizationService();
```

`LoadMetadata` accepts exported `EntityMetadata` XML, which is the practical way to get
realistic metadata (attribute types, option set values) into a test without hand-constructing
it — export it once from a representative environment and check it into the test project
alongside the test fixtures it supports.

## Testing environment variable reads

If a plugin uses the `Digitall.APower.EnvironmentVariables` module's `GetConfig` extension (see
[DIGITALL Assembly](../coding/serverside/digitall-assembly.md#add-on-modules)), seed the
corresponding environment variable definition/value via `.AddConfig(key, defaultValue, value)`
on the builder rather than mocking the extension method itself.

## Where this runs

Server-side unit tests run as a [build pipeline](../alm/build-pipeline.md) step (step 9), after
the [Solution Checker](solution-checker.md) — both gate the build, but the Solution Checker
catches customization-level issues while these tests catch logic-level regressions in your
plugins and Custom APIs.
