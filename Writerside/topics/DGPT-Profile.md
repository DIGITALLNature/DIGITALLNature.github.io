# Profile Command

Command to manage the locale authentication profiles.

## Sub-Commands

This table lists the ommands used in the DigitallPower CLI.

|Command|Description|
|-------|-----------|
|_dgtp profile_ **list**|List all know local authentication profiles.|
|_dgtp profile_ **create `<Name> <Connectionstring>`**|Create a new local authentication profiles.|
|_dgtp profile_ **delete `<Name>`**|Delete given local authentication profiles.|
|_dgtp profile_ **select `<Name>`**|Select given local authentication profiles.|
|_dgtp profile_ **purge**|Managed authentication profiles.|

### Samples

> `dgtp profile create Test "AuthType=OAuth;Url=https://_sample_.api.crm4.dynamics.com;Username=sample@sample.onmicrosoft.com;Password=sample;LoginPrompt=Never;SkipDiscovery=true;RequireNewInstance=true"`

> `dgtp profile select Test`

> `dgtp profile delete Test`

## Environment-Variable or Configfile based authentication

Alternatively, dgtp.power can specify the connection via environment variables or a configfile "dgtp.json", in the current working directory.

This is useful for scripting in build agents etc. where no connection string may be passed directly.

The following environment variables are taken into account:

|Enviroment Variable| Alternative| Description|
|-------|-----------|-----------|
|dgtp:xrm:connection| dgtp__xrm__connection| _Connectionstring_|
|dgtp:xrm:securityprotocol| dgtp__xrm__securityprotocol | Security protocoll (like Tls12)|
|dgtp:xrm:insecure| dgtp__xrm__insecure | Ignore certificate validation errors|

The dgtp.json allows analogous the following format:

> { "xrm": {"connection": "_connectionstring_", "securityprotocol": "Tls12", "insecure": true } }


## Security

Local connections are stored encrypted in the [dotnet Isolated Storage](https://learn.microsoft.com/en-us/dotnet/standard/io/isolated-storage) of the dgtp.power application for the current user.

Under Windows the data is encrypted using [ProtectedData](https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography.protecteddata?view=windowsdesktop-7.0), under Unix-like operating systems using [ASP.NET DataProtectionProvider](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider?view=aspnetcore-7.0).