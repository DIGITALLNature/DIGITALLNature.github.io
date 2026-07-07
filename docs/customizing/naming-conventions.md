# Naming Conventions

`prfx_` is a placeholder throughout this page for the project's
[publisher prefix](../architecture/publisher-and-prefix.md) — `dgt_` for DIGITALL-internal
components, or the customer-specific prefix for customer projects.

**`DGT-CUS-010`**{ #dgt-cus-010 } — All schema names are **snake_case**, lowercase. Apply the same pattern to any new component
type not explicitly listed below — the format is `[publisher]_[name][_type-suffix]`.

## Core schema

| Component | Pattern | Example |
|---|---|---|
| Table | `prfx_entityname` | `dgt_invoice` |
| Table image | `prfx_/Icons/Entity/entityname.svg` | `dgt_/Icons/Entity/invoice.svg` |
| Global choice (option set) | `prfx_optionsetname_set` | `dgt_invoicestatus_set` |
| Action | `prfx_some_action` | `dgt_send_reminder` |
| Alternate key | `prfx_fieldname_key` (field name without prefix/suffix) | `dgt_accountnumber_key` |
| Business Process Flow | `prfx_processname_process` | `dgt_leadqualification_process` |

## Column (field) suffixes

Field names follow `prfx_fieldname[_type-suffix]`. The suffix communicates the data type at a
glance, which matters once a table has dozens of columns and you're scanning a fetchXml or an
early-bound class for the right one:

| Type | Suffix | Example |
|---|---|---|
| Lookup | `_id` | `dgt_customer_id` |
| Choice (option set) | `_set` | `dgt_status_set` |
| Multi-select choice | `_mset` | `dgt_categories_mset` |
| Image | `_img` | `dgt_logo_img` |
| Currency | `_cur` | `dgt_amount_cur` |
| Date Only / Date and Time | `_dt` | `dgt_duedate_dt` |
| Customer (polymorphic lookup) | `_vid` | `dgt_customer_vid` |
| Whole Number | `_int` | `dgt_quantity_int` |
| — Duration | `_dur` | `dgt_runtime_dur` |
| — Language | `_lcid` | `dgt_preferredlanguage_lcid` |
| — Time Zone | `_tzid` | `dgt_timezone_tzid` |
| Decimal | `_dec` | `dgt_taxrate_dec` |
| Floating point | `_flt` | `dgt_measurement_flt` |
| Two Options (boolean) | `_bit` | `dgt_isactive_bit` |
| File | `_file` | `dgt_attachment_file` |
| Text Area | `_txt` | `dgt_description_txt` |
| Text | *(none, or `_txt`)* | `dgt_name` |
| — URL | `_url` | `dgt_website_url` |
| — Phone | `_number` | `dgt_phone_number` |
| — Email | `_email` | `dgt_email_email` |
| Rollup field | `[_type]_rf` | `dgt_totalamount_cur_rf` |
| Calculated field | `[_type]_cf` | `dgt_fullname_txt_cf` |
| Formula field | `[_type]_fx` | `dgt_status_set_fx` |

If a generated unique name would exceed the **41-character limit**, shorten the descriptive
part but always keep the type suffix — the suffix is what makes the field usable without
opening its metadata.

## Relationships

| Relationship | Pattern | Example |
|---|---|---|
| N:1 (parent → child) | `prfx_[parent]_to_[child]_on_[field]` | `dgt_account_to_contact_on_former_id` |
| N:N | `prfx_[entity]_to_[entity]_by_[purpose]` | `dgt_contact_to_account_by_employed` |

## Command bar (formerly "ribbon")

Microsoft has moved command bar customization to the modern **Command Bar Designer** in
make.powerapps.com; for anything the designer doesn't cover, use XrmToolBox rather than
hand-editing the underlying XML — see
[Publisher & Prefix](../architecture/publisher-and-prefix.md#third-party-components). The
naming pattern below still applies to the underlying components either tool produces:

| Component | Pattern | Example |
|---|---|---|
| Button | `prfx.prfx_table.Button.Name` | `dgt.dgt_invoice.Button.SendReminder` |
| Command | `prfx.prfx_table.Command.Name` | `dgt.dgt_invoice.Command.SendReminder` |
| Display Rule | `prfx.prfx_table.DisplayRule.Name` | `dgt.dgt_invoice.DisplayRule.MainFormOnly` |
| Enable Rule | `prfx.prfx_table.EnableRule.Name` | `dgt.dgt_invoice.EnableRule.FormExisting` |
| Icon | `prfx_/Icons/Button/[button-id].[suffix].svg` | `dgt_/Icons/Button/dgt.dgt_invoice.Button.SendReminder.svg` |

## Further recommendations

- **`DGT-CUS-020`**{ #dgt-cus-020 } — Set the managed property **`IsCustomizable`** to `false` for security roles and for
  rollup/calculated fields — these are rarely meant to be extended downstream and leaving them
  customizable invites accidental drift in managed deployments.
- Apply the same pattern style to component types not listed here; consistency matters more
  than covering every possible Dataverse component type exhaustively in this table.
