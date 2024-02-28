# PowerAutomate
**_If there is no defined standard by the customer, then we are following the “Common Development Guidlines!“_**

_Deviations must be justified in the architectural documentation_

## Usage of Solution Aware Connection References
- Naming Conventions: `dgt_..._con` (lower), e.g. `dgt_dataverse_con`, `dgt_sharepoint_con`,`dgt_outlook_con`

## Usage of Enviroment Variables
- Naming Conventions: `dgt_..._env` (lower), e.g. `dgt_foreign_system_url_env`, `dgt_organization_friendlyname_env`
- recommendation:
    - In development create a solution with only definitions of the enviroment variables (no value) and deploy it. (Hint: You can remove current value “from this solution” if you created the Enviroment Variable just there)
    - On the target environment create an unmanaged solution where the environment variable is taken in and a value is added. - This can also be set during manual deployment in the new UI. 

**Important**: Assignment of connections to connection references are user specific! If the flow is enabled its connection is used (also relevant for API limits etc.)

**Important**: Access via App-registration is needed to get full API Limits