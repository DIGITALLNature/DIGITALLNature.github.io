# Azure DevOps

This is the high-code ALM path, used per the decision tree in
[Deployment Approach](../architecture/deployment-approach.md) — typically for projects with a
wider system boundary (interfaces, Azure resources, data platform) alongside the Dataverse
stream, or where the customer already standardizes on Azure DevOps.

## Project setup

- Create a dedicated Azure DevOps project named `<Customer> - <System Name>`.
- Process template: **Agile**.
- Version control: **Git**.
- If the Carrier/Workbench model is in use, create a corresponding feature/area path for the
  customer system — see [Solution Concept](../architecture/solution-concept.md).

## Pipeline structure

**`DGT-ALM-110`**{ #dgt-alm-110 } — Use **YAML pipelines** checked into the repository (under `pipelines/`, see
[Source Control](source-control.md)) rather than classic editor-defined pipelines — this keeps
the pipeline definition reviewable in the same pull request as the code change it affects.

A minimal pipeline follows the [Build Pipeline](build-pipeline.md) step order. For readability
the example uses a single solution (`dgt_myproject_core`); a real project pushes and exports
per layered solution (see
[Solution Architecture](../architecture/solution-architecture.md)) — same steps, repeated per
solution:

```yaml title="pipelines/build.yml"
trigger:
  branches:
    include:
      - develop
      - main

pool:
  vmImage: windows-latest

variables:
  - group: dgt-myproject-common   # variable group: holds dgtp profile/connection settings

stages:
  - stage: Build
    jobs:
      - job: BuildAndPush
        steps:
          - task: UseDotNet@2
            inputs:
              version: "10.x"

          - task: NodeTool@0
            inputs:
              versionSpec: "24.x"

          - script: |
              corepack enable
              pnpm install
              pnpm run build:prod
            displayName: "Build client-side projects (web resources)"
            workingDirectory: src/WebResources

          - script: dotnet tool install -g dgt.power
            displayName: "Install dgtp CLI"

          - script: dgtp profile create build "$(DataverseConnectionString)" --msal
            displayName: "Configure dgtp profile"

          - script: dgtp codegeneration ./src/Generated --config ./modelconfig.json
            displayName: "Regenerate early-bound models"

          - script: dgtp maintenance solution-version dgt_myproject_core --build
            displayName: "Bump solution version"

          - task: DotNetCoreCLI@2
            displayName: "Build & pack plugin project"
            inputs:
              command: pack
              projects: "src/Plugins/**/*.csproj"

          - script: dgtp push $(Build.ArtifactStagingDirectory)/MyPlugins.nupkg --solution dgt_myproject_core --publish
            displayName: "Push plugin package"

          - script: dgtp push src/WebResources/dist --solution dgt_myproject_core --publish --delete-obsolete
            displayName: "Push web resources"

          - task: PowerPlatformToolInstaller@2

          - task: PowerPlatformExportSolution@2
            inputs:
              authenticationType: PowerPlatformSPN
              PowerPlatformSPN: dgt-myproject-build-connection
              SolutionName: dgt_myproject_core
              SolutionOutputFile: $(Build.ArtifactStagingDirectory)/dgt_myproject_core.zip

          - task: PowerPlatformChecker@2
            inputs:
              authenticationType: PowerPlatformSPN
              PowerPlatformSPN: dgt-myproject-build-connection
              FilesToAnalyze: $(Build.ArtifactStagingDirectory)/*.zip

          - task: DotNetCoreCLI@2
            displayName: "Run unit tests"
            inputs:
              command: test

          - publish: $(Build.ArtifactStagingDirectory)
            artifact: solution
```

Notes on the example above:

- The [Power Platform Build Tools](https://marketplace.visualstudio.com/items?itemName=microsoft-IsvExpTools.PowerPlatform-BuildTools)
  extension provides `PowerPlatformToolInstaller`, `PowerPlatformExportSolution`,
  `PowerPlatformChecker`, and the import/deploy task counterparts used in a release stage.
- `dgtp` steps run as regular CLI script steps — there's no dedicated Azure DevOps task for
  it; install it once per job via `dotnet tool install -g`.
- Authenticate `dgtp` the same way the build agent authenticates to Dataverse, via a service
  principal connection stored in a variable group, not a personal account.

## Release stage

Use a second stage (or a separate release pipeline) that consumes the `solution` artifact and
calls `PowerPlatformImportSolution@2` against each target environment in sequence, gated by
Azure DevOps environment approvals for production. Pair this with the post-deployment tasks in
[Pre- & Post-Deployment Tasks](pre-post-deployment.md).

## Variable groups & environments

- One variable group per Dataverse environment tier (`dgt-myproject-dev`,
  `dgt-myproject-test`, `dgt-myproject-prod`), holding the service principal connection details
  and environment URL for that tier.
- Use Azure DevOps **environments** (not just variable groups) for test/UAT/production targets
  so that approvals and deployment history are visible per environment, mirroring what
  [Power Platform Pipelines](power-platform-pipelines.md) gives you natively on the low-code
  path.
