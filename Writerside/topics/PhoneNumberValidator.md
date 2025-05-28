# Phone Number Validator

Name: dgt_ValidatePhoneNumber

Validates and format the provided phone number

## Request Parameters

| Name        | Type   | Required | Description                                                                                     |
|-------------|--------|----------|-------------------------------------------------------------------------------------------------|
| PhoneNumber | String | ✅        | input phone number                                                                              |
| Region      | String |          | [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes) country code |
| Format      | String |          | phone number formats: E164 (default) / INTERNATIONAL / NATIONAL / RFC3966                       |

## Response Properties

| Name                 | Type    | Description                                                                                   |
|----------------------|---------|-----------------------------------------------------------------------------------------------|
| IsValid              | Boolean | indicates whether the phone number is valid for the given region code                         |
| ErrorCode            | String  | error code indicating the type of validation error (see table below for possible error codes) |
| Message              | String  | human readable message specifying possible validation errors                                  |
| FormattedPhoneNumber | String  | formatted phone number based on the specified format (only provided if valid)                 |

Error Codes

| Code                 | Description                                                      |
|----------------------|------------------------------------------------------------------|
| INVALID_COUNTRY_CODE | the country code is not a supported country or no country at all |
| REGION_MISMATCH      | the phone number is not valid for the provided region            |
| NOT_A_NUMBER         | the string passed is not a valid phone number                    |
| INVALID_PHONE_NUMBER | the phone number is not a valid pattern                          |
| TOO_SHORT_AFTER_IDD  | the string is too short to be a viable phone number              |
| TOO_SHORT_NSN        | the string is too short to be a viable phone number              |
| TOO_LONG             | the string is too long to be a viable phone number               |

## Examples

Request

```http
POST [Environment URI]/api/data/v9.2/dgt_ValidatePhoneNumber
Accept: application/json
Content-Type: application/json; charset=utf-8
OData-MaxVersion: 4.0
OData-Version: 4.0

{
    "PhoneNumber": "015223433333",
    "Region": "DE",
    "Format": "E164"
}
```

Response

```http
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal
OData-Version: 4.0

{
    "@odata.context": "[Environment URI]/api/data/v9.2/$metadata#Microsoft.Dynamics.CRM.dgt_ValidatePhoneNumberResponse",
    "IsValid": true,
    "ErrorCode": null,
    "Message": null,
    "FormattedPhoneNumber": "+4915223433333"
}
```