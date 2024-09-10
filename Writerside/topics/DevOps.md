# DevOps Project
An internal Azure DevOps project is created with the customer name - system name. Select the “Agile” template and GIt for the source code.
If the Extended Workbench is commissioned, a feature for the customer system name must be created here.

Hint: Use PowerShell CreateDevOps.ps1!
(https://github.com/DIGITALLNature/DigitallPipelines/tree/feat/generate-complete-devops-project/azure-pipeline-scripts)

## Repos

### Repositories
```plantuml
@startmindmap
    * <Project Name>
        *_ Pipeline Scripts for ALM
        *_ Unpacked CRM Solutions
    * Serverside
        *_ Custom APIs
        *_ Dataverse Plugins
        *_ Workflow Assemblies
    * Clientside
        *_ Images
        *_ FormScripts
        *_ PCFs
    * Cloud
        *_ Functions
        *_ Logic Apps
        *_ API Management/Gateways
        *_ etc
    * Data
        *_ SSIS
        *_ DataPackages
        *_ DataBricks
        *_ DataFactorys
        *_ etc
    * Documentation
        *_ Writerside
@endmindmap
```

### Repositories Folder structure
```plantuml
@startmindmap
    * Root
        * src
            * <project a>
                *_ <project>.csproj
                *_ package.json
            * <project b>
                *_ <project>.csproj
                *_ package.json
            *_ Directory.Build.props
            *_ key.snk
        * tests
            * <project a>
            * <project b>
        * samples
        *_ <ci-pipeline>.yaml
        *_ Directory.Build.props
        *_ package.json
        *_ .gitignore
        *_ <name>.sln
        *_ README
@endmindmap
```

### Repositories Branch Strategy

```mermaid
    gitGraph
        commit id: "Init"
        branch Development
        checkout Development
        branch new_feature_a
        checkout new_feature_a
        commit
        commit
        checkout Development
        branch new_feature_b
        checkout new_feature_b
        commit
        checkout new_feature_a
        commit
        commit
        checkout Development
        merge new_feature_a tag: "CI 20220401.01"
        checkout new_feature_b
        commit
        checkout Development
        merge new_feature_b tag: "CI 20220401.02"
        checkout main
        merge Development tag: "Release 2.0.0"

```


### .gitignore
Generate current .gitignore on [gitignore.io](https://www.toptal.com/developers/gitignore) for used IDE's and technology.
e.g. visualstudio,rider,visualstudiocode,dotnetcore

Add all folders where generated code is produced. For example, earlybound classes from DGTP or the transpiled
JScript files from Typescript projects.

## Pipelines

### Variable groups

### Envirpments

### YAML Pipelines
