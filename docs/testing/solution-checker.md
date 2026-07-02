# Solution Checker

**`DGT-TST-020`**{ #dgt-tst-020 } — Run the [Power Apps Solution Checker](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/use-powerapps-checker) against every packed solution as a build-pipeline quality
gate — step 8 in [Build Pipeline](../alm/build-pipeline.md), after packaging and before
automated tests.

## Running it

```shell
pac solution check --path ./artifacts/dgt_myproject_core.zip
```

In Azure DevOps, use the `PowerPlatformChecker@2` task from the
[Power Platform Build Tools](https://marketplace.visualstudio.com/items?itemName=microsoft-IsvExpTools.PowerPlatform-BuildTools)
extension — see [Azure DevOps](../alm/azure-devops.md). In GitHub Actions, use
`microsoft/powerplatform-actions/check-solution@v1` — see [GitHub Actions](../alm/github-actions.md).
Both wrap the same underlying Solution Checker service `pac` calls directly.

## What it catches

The checker flags plugin code issues (e.g. blocking synchronous calls, unhandled exceptions in
patterns it recognizes), customization issues (e.g. missing index recommendations, unused
attributes), and general solution-quality findings. It does **not** replace
[server-side unit tests](unit-testing-serverside.md) — it's static analysis against the
packaged artifact, not a test of your plugin's actual behavior against representative data.

## Gating the build

Treat a failing **high-severity** result as a build failure, not a warning to review later —
configure the task/step to fail the pipeline on high-severity findings rather than only
reporting them. Lower-severity findings are worth tracking but shouldn't block every deployment
on their own; use judgment per project on where that line sits, and document the chosen
threshold in the project's architectural documentation if it deviates from "fail on high."
