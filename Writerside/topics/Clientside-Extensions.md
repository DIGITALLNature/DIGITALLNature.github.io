# Clientside Extensions
**_If there is no defined standard by the customer, then we are following the “Common Development Guidlines!“_**

_Deviations must be justified in the architectural documentation_

## WebResources / Client-Scripting
- TypeScript ; Node.js LTS; npm latest; tsc latest
- Webresource-Pattern
    - d365.ui.extension
- Register **OnLoad** event only, other events are registered in code
- Visual Studio Code default code formatting, enforce lint checks
- Structure of scripts:
    - Lib
    - Form
    - View
    - Ribbon
- .gitattributes to enforce CR\LF or LF (but enforce to use the same)

## PowerApps Component Framework (PCF)
- pac latest
- TypeScript; Node.js ^18.8.2 LTS; npm latest; tsc ^4.8.4
- Visual Studio Code default code formatting, enforce lint checks

**Hint:** Best Pratice to use [PCF-CustomControlBuilder](https://github.com/Power-Maverick/PCF-CustomControlBuilder) in XrmToolbox.

https://www.npmjs.com/package/@microsoft/eslint-plugin-power-apps

https://github.com/scottdurow/dataverse-gen