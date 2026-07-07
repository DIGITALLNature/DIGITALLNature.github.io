# Quickstart — From Zero to a Deployed Plugin

The whole server-side happy path on one page. Each step links to the chapter with the full
story and the *why*; this page is just the *what*, in order. It assumes the
[toolchain](setup/toolchain.md) (.NET SDK, Node, `pac`) is installed.

!!! tip "Read once, then bookmark"
    New to a DIGITALL Power Platform project? Skim this top to bottom to see how the pieces fit,
    then follow the linked chapters for the detail. Returning? Use it as a command cheat-sheet.

## 1. Install `dgtp` and connect

```shell
dotnet tool install -g dgt.power
dgtp profile create dev "AuthType=OAuth;Url=https://yourorg.crm.dynamics.com;AppId=...;RedirectUri=...;LoginPrompt=Auto"
dgtp profile select dev
```

→ [dgtp CLI](setup/dgtp-cli.md) for token-based/CI profiles and the `xrm:connection` alternative.

## 2. Create the plugin project

Plugin **packages** (NuGet dependent assemblies) are the standard — scaffold one with `pac`,
then add the two DIGITALL packages:

```shell
pac plugin init --outputDirectory ./src/Plugins/Contacts --skip-signing
cd ./src/Plugins/Contacts
dotnet add package Digitall.Plugins                # Executor base class
dotnet add package Digitall.Plugins.Registration   # declarative step registration
```

`--skip-signing` is correct here — packages aren't signed. → [Plugin Packages](coding/serverside/plugin-packages.md)
(why packages, not ILMerge or classic single-assembly) and
[Project Setup](coding/serverside/project-setup.md) for the exact `.csproj`.

## 3. Generate the early-bound model

```shell
dgtp codegeneration ./Model --config ./modelconfig.json
```

→ [Early-Bound Models](coding/serverside/early-binding.md) for the config shape. Generated code
is **not** committed — it's reproduced on every build.

## 4. Write the plugin

Inherit [`Executor`](coding/serverside/digitall-assembly.md), work against the
[generated model](coding/serverside/early-binding.md) (`Contact`, `SdkMessageNames`), return an
`ExecutionResult`, and register the step with a
[`[PluginRegistration]`](coding/serverside/registration-attributes.md) attribute — no Plugin
Registration Tool:

```csharp title="ContactValidationPlugin.cs"
using System;
using Digitall.Plugins;                // package: Digitall.Plugins
using Digitall.Dataverse.Model;        // generated early-bound model
using Digitall.Plugins.Registration;
using Microsoft.Xrm.Sdk;

[PluginRegistration(PluginExecutionMode.Synchronous, SdkMessageNames.Create, PluginExecutionStage.PreValidation,
    PrimaryEntityName = Contact.EntityLogicalName)]
public class ContactValidationPlugin : Executor
{
    protected override ExecutionResult Execute()
    {
        var contact = Entity.ToEntity<Contact>();

        if (!contact.BirthDate.HasValue)
        {
            return ExecutionResult.Skipped;
        }

        if (contact.BirthDate.Value > DateTime.UtcNow)
        {
            throw new InvalidPluginExecutionException("Birth date cannot be in the future.");
        }

        return ExecutionResult.Ok;
    }
}
```

## 5. Build, push, done

```shell
dotnet build -c Release
dgtp push ./bin/Release/Contacts.1.0.0.nupkg --solution dgt_myproject_plugins --publish
```

`dgtp push` creates or updates the **plugin package** (matched by name + version) **and**
reconciles its `[PluginRegistration]` steps into Dataverse — add an attribute and push to
register a step; remove it and push to unregister it. The package
[version must increase on every push](alm/versioning.md), including local dev-loop builds. →
[`dgtp push` in depth](alm/pre-post-deployment.md#dgtp-push-in-depth).

## 6. Test without a live environment

```csharp
var builder = new FakePluginContextBuilder();
var target = new Contact { BirthDate = new DateTime(1990, 1, 1) };
var sp = builder.WithMessageName(SdkMessageNames.Create).WithTarget(target).BuildServiceProvider();

var plugin = new ContactValidationPlugin();
plugin.Execute(sp);

Assert.That(plugin.Result, Is.EqualTo(ExecutionResult.Ok));
```

→ [Server-side Unit Testing](testing/unit-testing-serverside.md) with `Digitall.Dataverse.Testing`.

## Where to go from here

| You also need to… | Go to |
|---|---|
| Configure tables, forms, security (low-code) | [Customizing](customizing/index.md) |
| Write a form/ribbon script | [TypeScript Web Resources](coding/clientside/typescript-webresources.md) |
| Automate with a cloud flow | [Power Automate Flows](coding/flows.md) |
| Wire this into a build/release pipeline | [ALM & Deployment](alm/index.md) |
| Look up a `dgtp` command | [dgtp Command Reference](reference/dgtp-commands.md) |
