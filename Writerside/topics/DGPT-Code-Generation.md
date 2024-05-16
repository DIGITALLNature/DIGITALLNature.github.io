# Codegeneration Command

Generates .cs, .ts and metadata.xml model files for Dataverse.

## Optional Parameters
### TargetDirectory
Full path to the directory where the generated model classes will be saved. Default is current folder.

### `--folder` `-f`
Define an alternate name for the model folder. Default is `Model`.

### `--config` `-c`
Full or relative path to the config file, e.g. 'C:\temp\config.json'. Default is `config.json`.

## Examples {id="examples-general"}

### Generate model using default parameters
```Shell
dgtp codegeneration
```

### Switch profile and generate model in certain sub folder

```Shell
dgtp profile select Test
dgtp codegeneration ./subfolder/models `
  --folder CustomModel `
  --config './CustomModel/modelconfig.json'
```

# Model configuration file
The model generation supports different kinds of tasks to execute:

* DotNet
* TypeScript
* MetaData
* DotNet + MetaData
* DotNet + TypeScript + MetaData

For the sake of clarity, the chapters on configuration file parameters are separated by target. In addition to the general parameters, target-specific parameters can also be used. Use this handy list to jump directly to the chapter you need:

* [General parameters](#general-parameters)
    * [Required parameters](#required-parameters)
    * [Additional content](#additional-content-general)
    * [Filters](#filters-general)
    * [Output management](#output-management_general)
* [DotNet specific parameters](#dotnet-specific-parameters)
    * [Filters](#filters-dotnet)
    * [Output management](#output-management-dotnet)
* [TypeScript specific parameters](#typescript-specific-parameters)
    * [Filters](#filters-typescript)
    * [Additional content](#additional-content-typescript)
    * [Output management](#output-management-typescript)

As an introduction to the config topic, please have a look at the [examples listed below](#examples-config).

## General parameters

### Required parameters
These parameters should always be included. Otherwise the code generation will fail or at least will show warnings.

|Parameter|Value type|Description|
|---|---|---|
|Entities|string[]|List of all tables for which model content should be created|
|Actions|string[]|List of all actions for which model content should be created|
|CustomAPIs|string[]|List of all custom apis for which model content should be created|

### Additional content {id="additional-content-general"}
If you want to generate additional model content on top of the default stuff, use these parameters.

|Parameter|Value type|Default|Description|
|---|---|---|---|
|AdditionalSdkMessages|string[]|`[]`|List of all additional sdk messages for which model content should be created|
|GlobalOptionSets|string[]|`[]`|Generate code for these global option sets only|

### Filters {id="filters-general"}
To reduce the amount classes or solution components to get generated code for, use these parameters.

|Parameter|Value type|Default| Description                                                                                                                                                        |
|---|---|---|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityMask|string|`"*"`| Additional to the list of entities you can create a mask to get all entities which fit example: "new_*" would create models for all entities which start with new_ |
|SdkMessageFilters|string[]|`[]`| Generate code for these sdk messages only                                                                                                                          |
|Solutions|string[]|`[]`| List all solutions for which classes should be generated.<br/>(TypeScript: see [`OnlyFormsFromSolutions`](#filters-general) below)                                               |
|SuppressOptions|bool|`false`| Deactivate creation of OptionSetValues                                                                                                                             |
|SuppressSdkMessages|bool|`false`| Deactivate creation of SdkMessages (Create, Update, Delta, etc..)                                                                                                  |

### Output management {id="output-management_general"}
To define how the output should look like or for which targets you want generated code, use the following parameters.

|Parameter|Value type|Default|Description|
|---|---|---|---|
|Hints|bool|`true`|Show hints for config improvements or deprecated parameter usage on console.|
|SuppressDotNet|bool|`false`|Deactivate creation of DotNet files (.cs)|
|SuppressMetaData|bool|`false`|Deactivate creation of MetaData files (.xml)|
|SuppressTypeScript|bool|`false`|Deactivate creation of TypeScript files (.ts)|
|UseBaseLanguage|bool|`false`|Generated summaries and optionset labels in system base language|

## DotNet specific parameters

### Filters {id="filters-dotnet"}

|Parameter|Value type|Default|Description|
|---|---|---|---|
|SuppressActions|bool|`false`|Deactivate creation of Actions|
|SuppressAlternateKeys|bool|`false`||
|SuppressContext|bool|`false`|Deactivate creation of the Context File|
|SuppressEntityTypeCode|bool|`false`||
|SuppressLogicalNames|bool|`false`|Deactivate creation of Logical Names|
|SuppressNavigationProperties|bool|`false`|Deactivate creation of the navigation properties|
|SuppressRelations|bool|`false`|Deactivate creation of all Relation Names|

### Output management {id="output-management-dotnet"}

|Parameter|Value type|Default|Description|
|---|---|---|---|
|EditableReadOnlyProperties|bool|`false`|Make generated readonly properties editable|
|NameSpace|string|`"D365.Extension.Model"`|Optional. Define namespace to be used for generated classes.|
|NonDebuggerNonUserCode|bool|`false`|Skip annotation of generated properties with 'DebuggerNonUserCode'|
|SuppressNullableSupport|bool|`false`|Don't use language features of C# 8.0 and up. These are for generating models for tools and dependant plugins.|
|Virtual|bool|`false`|Generate virtual classes/properties|

## TypeScript specific parameters

### Filters {id="filters-typescript"}

|Parameter| Value type                      |Default| Description                                                                                                         |
|---|---------------------------------|---|---------------------------------------------------------------------------------------------------------------------|
|EntityFilters| [see below](#entityfilters)     || TypeScript only (shrink imports, e.g. odata query or form open)                                                     |
|EntityFormFilters| [see below](#entityformfilters) || Generate classes only for these forms, including only the defined attributes, optionsets, tabs, sections and grids. |
|EntityRefFilter| [see below](#entityfilters)     || TypeScript only (shrink imports, e.g. odata query or form open)                                                     |
|Forms| string[]                        |`[]`| List all forms for which classes should be generated.                                                               |
|OnlyFormsFromSolutions| bool                            |`false`| Generate form classes only for forms included in the solutions defined in the [`Solutions`](#filters-general) parameter above     |

### Additional content {id="additional-content-typescript"}
|Parameter|Value type|Default|Description|
|---|---|---|---|
|BusinessProcessFlows|string[]|`[]`|Generate classes for these BPFs.|

### Output management {id="output-management-typescript"}
|Parameter|Value type|Default|Description|
|---|---|---|---|
|TypingPath|string|`"../../Typings/Xrm/index.d.ts"`|TypeScript typings, e.g. ../../Typings/Xrm/index.d.ts|

#### `EntityFilters` / `EntityRefFilters` {id="entityfilters"}
|Parameter|Value type|Default|Description|
|---|---|---|---|
|Entity|string||Logical name of the table for which the model is to be generated.|
|Attributes|string[]|`[]`|Logical names of the columns inside the table which must be part of the model.|
|Optionsets|string[]|`[]`|Logical names of the option sets inside the table which must be part of the model.|

#### `EntityFormFilters`
|Parameter|Value type|Default|Description|
|---|---|---|---|
|EntityForm|string||Name of the form for which the model is to be generated.|
|Attributes|string[]|`[]`|Logical names of the columns inside the table which must be part of the form model.|
|Optionsets|string[]|`[]`|Logical names of the option sets inside the table which must be part of the form model.|
|Tabs|string[]|`[]`|Names of the tabs inside the table which must be part of the form model.|
|Sections|string[]|`[]`|Names of the sections inside the table which must be part of the form model.|
|Grids|string[]|`[]`|Names of the sub grids inside the table which must be part of the form model.|

## Examples {id="examples-config"}

### Generate DotNet classes for plugin usage
In this example we use a very common configuration for plugin model generation.
```json
{
  "Entities": [
    "account",
    "contact"
  ],
  "Actions": [],
  "CustomAPIs": [],
  "AdditionalSdkMessages": [ "Associate", "Disassociate" ],
  "SuppressTypeScript": true,
  "SuppressMetaData": true,
  "SuppressNullableSupport": true,
  "EditableReadOnlyProperties": true,
  "UseBaseLanguage": true
}
```

### Generate DotNet classes for tool usage, with entity filter
This example generates model for account and contact. In the contact model class it just generates the specified attributes and option sets.
```json
{
  "Entities": [
    "account",
    "contact"
  ],
  "Actions": [],
  "CustomAPIs": [],
  "AdditionalSdkMessages": [ "Associate", "Disassociate" ],
  "SuppressTypeScript": true,
  "SuppressMetaData": true,
  "SuppressNullableSupport": true,
  "EditableReadOnlyProperties": true,
  "UseBaseLanguage": true,
  "EntityFilters": [
    {
      "Entity": "contact",
      "Attributes": [
        "firstname",
        "lastname"
      ],
      "Optionsets": [
        "statecode",
        "statuscode"
      ]
    }
  ]
}
```

### Generate TypeScript classes, with entity form filters
```json
{
  "Entities": [
    "account",
    "contact"
  ],
  "Actions": [],
  "CustomAPIs": [],
  "AdditionalSdkMessages": [ "Associate", "Disassociate" ],
  "SuppressDotNet": true,
  "SuppressMetaData": true,
  "TypingPath": "../../../node_modules/@types/xrm/index.d.ts",
  "UseBaseLanguage": true,
  "EntityFormFilters": [
    {
      "EntityForm": "Information",
      "Attributes": [
        "firstname",
        "lastname"
      ],
      "Optionsets": [
        "statecode",
        "statuscode"
      ],
      "Tabs": [
        "general"
      ]
    }
  ]
}
```

### Generate TypeScript classes for "_new" entities included in a solution
```json
{
  "Entities": [],
  "Actions": [],
  "CustomAPIs": [],
  "Solutions": [ "SampleSolution" ],
  "EntityMask": "new_",
  "OnlyFormsFromSolutions": true,
  "SuppressDotNet": true
}
```