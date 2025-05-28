# IBAN Validator

Name: dgt_ValidateIban

Validates and format the provided IBAN

## Request Parameters

| Name              | Type        | Required | Description                                                                                                                    |
|-------------------|-------------|----------|--------------------------------------------------------------------------------------------------------------------------------|
| Iban              | String      | ✅        | input IBAN                                                                                                                     |
| AllowedCountries  | StringArray |          | only allow IBANs from these [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes) country codes   |
| RejectedCountries | StringArray |          | do not allow IBANs from these [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes) country codes |

## Response Properties

| Name            | Type    | Description                                                                                |
|-----------------|---------|--------------------------------------------------------------------------------------------|
| IsValid         | Boolean | indicates whether the IBAN is valid                                                        |
| ErrorCode       | String  | error code indicating the type validation error (see table below for possible error codes) |
| Message         | String  | human readable message specifying possible validation errors                               |
| EletronicFormat | String  | machine readable format of the provided IBAN (no spaces)                                   |
| PrintFormat     | String  | human readable format of the provided IBAN (with spaces)                                   |

Error Codes

| Code                               | Description                                                                           |
|------------------------------------|---------------------------------------------------------------------------------------|
| CountryNotAcceptedResult           | IBAN passes validation but is rejected based on AllowedCountries or RejectedCountries |
| IllegalCharactersResult            | IBAN contains illegal characters                                                      |
| InvalidCheckDigitsResult           | IBAN check digits are incorrect                                                       |
| InvalidLengthResult                | IBAN has an incorrect length                                                          |
| InvalidStructureResult             | the structure of the IBAN is incorrect                                                |
| UnknownCountryCodeResult           | country code is unknown or unsupported                                                |
| IllegalCountryCodeCharactersResult | IBAN contains illegal characters in the country code                                  |

## Examples

Request

```http
POST [Environment URI]/api/data/v9.2/dgt_ValidateIban
Accept: application/json
Content-Type: application/json; charset=utf-8
OData-MaxVersion: 4.0
OData-Version: 4.0

{
    "Iban": "DE75512108001245126199",
    "AllowedCountries": ["DE"],
    "RejectedCountries": ["CH", "FR", "AT"]
}
```

Response

```http
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal
OData-Version: 4.0

{
    "@odata.context": "[Environment URI]/api/data/v9.2/$metadata#Microsoft.Dynamics.CRM.dgt_ValidateIbanResponse",
    "IsValid": true,
    "Message": null,
    "ErrorCode": null,
    "ElectronicFormat": "DE75512108001245126199",
    "PrintFormat": "DE75 5121 0800 1245 1261 99"
}
```