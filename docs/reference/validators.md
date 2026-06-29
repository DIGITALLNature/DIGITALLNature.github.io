# DIGITALL Validators

[DigitallValidators](https://github.com/DIGITALLNature/DigitallValidators) is a managed
solution providing generic, reusable validators as [Custom APIs](../coding/serverside/custom-api.md)
— callable equally from plugins, client-side JavaScript/TypeScript, or Power Automate. They run
entirely locally; no data leaves the environment.

## Getting started

1. Download the latest release from
   [GitHub Releases](https://github.com/DIGITALLNature/DigitallValidators/releases/latest).
2. Import the managed solution into your environment.
3. Call the Custom API directly, or wrap it in a small helper on the calling side — see the
   examples below.

## Phone Number Validator — `dgt_ValidatePhoneNumber`

Validates and formats a phone number.

**Request parameters**

| Name | Type | Required | Description |
|---|---|---|---|
| `PhoneNumber` | String | ✅ | Input phone number. |
| `Region` | String | | [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/List_of_ISO_3166_country_codes) country code. |
| `Format` | String | | `E164` (default) / `INTERNATIONAL` / `NATIONAL` / `RFC3966`. |

**Response properties**

| Name | Type | Description |
|---|---|---|
| `IsValid` | Boolean | Whether the phone number is valid for the given region. |
| `ErrorCode` | String | One of the error codes below. |
| `Message` | String | Human-readable validation message. |
| `FormattedPhoneNumber` | String | Formatted number in the requested format (only if valid). |

**Error codes:** `INVALID_COUNTRY_CODE`, `REGION_MISMATCH`, `NOT_A_NUMBER`,
`INVALID_PHONE_NUMBER`, `TOO_SHORT_AFTER_IDD`, `TOO_SHORT_NSN`, `TOO_LONG`.

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

## IBAN Validator — `dgt_ValidateIban`

Validates and formats an IBAN.

**Request parameters**

| Name | Type | Required | Description |
|---|---|---|---|
| `Iban` | String | ✅ | Input IBAN. |
| `AllowedCountries` | String array | | Only accept IBANs from these country codes. |
| `RejectedCountries` | String array | | Reject IBANs from these country codes. |

**Response properties**

| Name | Type | Description |
|---|---|---|
| `IsValid` | Boolean | Whether the IBAN is valid. |
| `ErrorCode` | String | One of the error codes below. |
| `Message` | String | Human-readable validation message. |
| `ElectronicFormat` | String | Machine-readable format (no spaces). |
| `PrintFormat` | String | Human-readable format (with spaces). |

**Error codes:** `CountryNotAcceptedResult`, `IllegalCharactersResult`,
`InvalidCheckDigitsResult`, `InvalidLengthResult`, `InvalidStructureResult`,
`UnknownCountryCodeResult`, `IllegalCountryCodeCharactersResult`.

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

## Contributing

Issues and contributions go to the
[DigitallValidators repository](https://github.com/DIGITALLNature/DigitallValidators) directly,
not to this guideline.
