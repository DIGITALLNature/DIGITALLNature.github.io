# Serverside Extensions
**_If there is no defined standard by the customer, then we are following the “Common Development Guidlines!“_**

_Deviations must be justified in the architectural documentation_

## Plugins / CodeActivities
- C#; modern (/dotnet) project format targeting NET462
- Plugin-Template
    - d365.extension
- Early Binding
    - Model Generator (d365.scripts)
- Plugin Registration
    - Attribute-based utility (d365.scripts)
- Follow Ocdata recommendations (R# or Rider provides those also)
- Visual Studio default code formatting; CRTL+K, CRTL+D
- No ILMerge allowed - use Dependant Plugins format
- d365.extension addons we may using in the project:
    - Config
    - Logging
    - KeyVault
    - Telemetry
    - ServiceBus
    - SharePoint
- Each CodeActivity (Group) in a single assmbly incl. version!!
- .gitattrutes to enforce CR\LF or LF (but enforce to use the same)
- Naming Conventions:
    - Assembly Name: `dgt.[Plugin|CustomApi|Workflow].[Entity|Service|Name]`
    - Default Namespace: `dgt.[Plugin|CustomApi|Workflow].[Entity|Service|Name]`
    - Project Name: `[Entity|Service|Name]`


## Sample csproj
```
<Project Sdk="Microsoft.NET.Sdk">

    <PropertyGroup>
        <TargetFramework>net462</TargetFramework>
        <Nullable>disable</Nullable>
        <AssemblyName>CrmIkPk.Plugins.AddressSync</AssemblyName>
        <RootNamespace>CrmIkPk.Plugins.AddressSync</RootNamespace>
        <RestorePackagesWithLockFile>true</RestorePackagesWithLockFile>
        <LangVersion>7.3</LangVersion>
        <SignAssembly>true</SignAssembly>
        <AssemblyOriginatorKeyFile>key.snk</AssemblyOriginatorKeyFile>
        <AssemblyVersion>1.0.0.1</AssemblyVersion>
    </PropertyGroup>

    <ItemGroup>
        <Reference Include="System.Net.Http" />
        <Reference Include="System.Runtime.Caching" />
    </ItemGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.CrmSdk.CoreAssemblies" Version="9.0.2.0" />
    </ItemGroup>
</Project>
```