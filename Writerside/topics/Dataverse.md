# Dataverse
**_If there is no defined standard by the customer, then we are following the “Common Development Guidlines!“_**

_Deviations must be justified in the architectural documentation_

## Configuration / Customization
- Publisher: Custom one for Customer-Managed Environment, for general proposes digitall (prefix: dgt) [dgt_publisher]
- Using XRM Toolbox before install different 3rd party solutions in dynamics!
- Changes on Ribbon Bar: XRM Toolbox
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
 
### Naming Conventions
- [snake_case](https://en.wikipedia.org/wiki/Snake_case) (lower)!!
- Entity Name: `dgt_entityname` (`publisher_name`)
- Entity Image: `dgt_/Icons/Entity/entityname.svg` (`publisher_/Icons/Entity/name`)
- Global Option Set Name: `dgt_optionsetname_set` (`publisher_name_set`)
- Action Name: `dgt_some_action` (`publisher_name`)
- Field Name: `dgt_fieldname_` (`publisher_name[_type]`)
    - field names should match the type pattern
        - Lookup: `dgt_..._id`
        - OptionSet: `dgt_..._set`
        - MultiOptionSet: `dgt_..._mset`
        - Image: `dgt_..._img`
        - Currency: `dgt_..._cur`
        - Date Only/Date Time: `dgt_..._dt`
        - Customer: `dgt_..._vid` (virtual id)
        - Polymorph/Multiple Lookup: `dgt_..._vid`
        - Whole Number: `dgt_..._int`
            - Duration: `dgt_..._dur`
            - Language: `dgt_..._lcid`
            - Time Zone: `dgt_..._tzid`
        - Decimal number: `dgt_..._dec`
        - Floating point number: `dgt_..._flt`
        - TwoOption: `dgt_..._bit`
        - File: `dgt_..._file`
        - Text Area: `dgt_..._txt`
        - Text: `dgt_...[_txt]`
            - Url: `dgt_..._url`
            - Phone: `dgt_..._number`
            - eMail: `dgt_..._email`
        - Rollup field: `dgt_...[_type]_rf`
        - Calculated field: `dgt_...[_type]_cf`
        - Formular field: `dgt_...[_type]_fx`
    - Long Field Name:
        - Max length for unique name is 41
        - If the generated name exceeds this length shorten it, but keep the type!
- Entity Relations (recommendation):
    - N:1, expected dgt_account_to_contact_on_former_id (`[publisher]_[parent(lookup)]_to_[child(field)]_on_[field`]; parent=1; child=N)
    - N:N, expected dgt_contact_to_account_by_employed (`[publisher]_[entity]_to_[entity]_by_[purpose]`)
- Alternate Keys:
    - Alternate Key: `dgt_..._key`
- Business Process Flow:
    - Name: `dgt_..._process`
- Ribbon:
    - Button: dgt.[entity].Button.[name], e.g. `dgt.dgt_message.Button.PublishMessageFile`
    - Command: dgt.[entity].Command.[name], e.g. `dgt.dgt_message.Command.PublishMessageFile`
    - DisplayRule: dgt.[entity].DisplayRule.[name], e.g. `dgt.dgt_message.DisplayRule.MainFormOnly`
    - EnableRule: dgt.[entity].EnableRule.[name], e.g. `dgt.dgt_message.EnableRule.FormExisting`
    - Icons: dgt_/Icons/[button-id].[suffix].[optional:dimension], e.g. `dgt_/Icons/Button/dgt.dgt_message.Button.PublishMessageFile.svg`
- Recommendation:
    - Security Roles & Rollup/Calculated Fields:
        - set "IsCustomozable" to false

    