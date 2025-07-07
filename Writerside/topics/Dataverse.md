# Dataverse
**_If there is no defined standard by the customer, then we are following the “Common Development Guidlines!“_**

_Deviations must be justified in the architectural documentation_

## Configuration / Customization
- In general, we apply Microsoft's customization rules. This means that we only deviate if we see an advantage for development or if we feel there would be serious disadvantages if we stayed with the “standard” (e.g. name collisions within generated classes).
- Publisher & prefixes
  - For development of components which get developed and packaged at DIGITALL and later on deployed on customer environments, we use the _DIGITALL_ publisher, and therefore the prefix _dgt__. [dgt_publisher]
  - For all customer centric projects, we typically use a customer specific publisher, and therefore a customer specific prefix.
- Before installing any special-purpose 3rd party solution in the Power Platform, please check first if there is a XrmToolbox plugin for this
  - If you want to make changes on the ribbon bar, please also use the XrmToolbox
### Themes
- Every Instance/Stage/App gets his own color theme
- Logo: 150-400x50px (png)
### Images (Alternative 1):
- Entity Image:
    - Unified Interface:
        - svg: Black (Hex: #000000 bzw. #ff000000; RGB: 0, 0, 0) - 0px Padding
    - Ribbon Bar Icons:
        - svg: Grey (HEX: #3A3A3A bzw. ##FF3A3A3A; RGB: 58, 58, 58 ) - 0px padding

### Images (Alternative 2):
- Use the fluentui icons
    - [Fluent UI](https://github.com/microsoft/fluentui-system-icons?tab=readme-ov-file)
    - [Office UI Fabric Icons](https://uifabricicons.azurewebsites.net)
- It is recommended to use the original sources
- To quickly search for Fluent Icons and possibly download them as .svg, Flicon offers a good overview
    - [Flicon](https://www.flicon.io/)

 
### Naming Conventions

In the following, please either apply "dgt_" for internal development, or the customer specific publisher + prefix for customer projects.
`prfx_` is just a placeholder which should then be replaced by the corresponding publisher/prefix.

The format of the items in the following list is as follows:
- Description: `example` (`general format`)

Please apply a similar format on any new types not included in this list.

- All schema names have to be [snake_case](https://en.wikipedia.org/wiki/Snake_case) (lower case)
- Entity Name: `prfx_entityname` (`[publisher]_name`)
- Entity Image: `prfx_/Icons/Entity/entityname.svg` (`[publisher]_/Icons/Entity/name`)
- Global Option Set Name: `prfx_optionsetname_set` (`[publisher]_name_set`)
- Action Name: `prfx_some_action` (`[publisher]_name`)
- Field Name: `prfx_fieldname_*` (`[publisher]_name[_type]`)
    - field names should match the type pattern
        - Lookup: `prfx_..._id`
        - OptionSet: `prfx_..._set`
        - MultiOptionSet: `prfx_..._mset`
        - Image: `prfx_..._img`
        - Currency: `prfx_..._cur`
        - Date Only/Date Time: `prfx_..._dt`
        - Customer: `prfx_..._vid` (virtual id)
        - Polymorph/Multiple Lookup: `prfx_..._vid`
        - Whole Number: `prfx_..._int`
            - Duration: `prfx_..._dur`
            - Language: `prfx_..._lcid`
            - Time Zone: `prfx_..._tzid`
        - Decimal number: `prfx_..._dec`
        - Floating point number: `prfx_..._flt`
        - Two options: `prfx_..._bit`
        - File: `prfx_..._file`
        - Text Area: `prfx_..._txt`
        - Text: `prfx_...[_txt]`
            - Url: `prfx_..._url`
            - Phone: `prfx_..._number`
            - eMail: `prfx_..._email`
        - Rollup field: `prfx_...[_type]_rf`
        - Calculated field: `prfx_...[_type]_cf`
        - Formular field: `prfx_...[_type]_fx`
    - Long Field Name:
        - Max length for unique name is 41
        - If the generated name exceeds this length shorten it, but keep the type!
- Entity Relations (recommendation):
    - N:1 relation: `prfx_account_to_contact_on_former_id` (`[publisher]_[parent(lookup)]_to_[child(field)]_on_[field]`; parent=1; child=N)
    - N:N relation: `prfx_contact_to_account_by_employed` (`[publisher]_[entity]_to_[entity]_by_[purpose]`)
- Alternate Keys:
    - Alternate Key: `prfx_accountnumber_key` (`[publisher]_[fieldname-without-prefix-and-suffix]_key`)
- Business Process Flow:
    - Name: `prfx_accountnumber_process` (`[publisher]_[fieldname-without-prefix-and-suffix]_process`)
- Ribbon:
    - Button: `prfx.prfx_message.Button.PublishMessageFile` (`[publisher].[entity].Button.[name]`)
    - Command: `prfx.prfx_message.Command.PublishMessageFile` (`[publisher].[entity].Command.[name]`)
    - DisplayRule: `prfx.prfx_message.DisplayRule.MainFormOnly` (`[publisher].[entity].DisplayRule.[name]`)
    - EnableRule: `prfx.prfx_message.EnableRule.FormExisting` (`[publisher].[entity].EnableRule.[name]`)
    - Icons: `prfx_/Icons/Button/prfx.prfx_message.Button.PublishMessageFile.svg` (`[publisher]_/Icons/[button-id].[suffix].[optional:dimension]`)

### Further recommendations
* For Security Roles & Rollup/Calculated Fields, you should always set the managed property "IsCustomizable" to `false`

    