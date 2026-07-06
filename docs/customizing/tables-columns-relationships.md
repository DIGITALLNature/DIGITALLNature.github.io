# Tables, Columns & Relationships

This page is about *what* to model and the choices made once per table that can't be undone
later. Naming is covered separately in [Naming Conventions](naming-conventions.md); this page
assumes you follow it.

## Reuse before you create

**`DGT-CUS-030`**{ #dgt-cus-030 } — Check whether a standard table (Account, Contact, the Activity tables, etc.) already fits before
adding a custom one. A custom table is a long-term maintenance commitment — security, views,
forms, and an early-bound model all follow from it. Extend a standard table with custom columns
in preference to cloning it.

## Decisions you make once, at creation

**`DGT-CUS-040`**{ #dgt-cus-040 } — Some choices are fixed at table creation — decide them deliberately:

| Decision | Why it's permanent | DIGITALL default |
|---|---|---|
| **Ownership** — User/Team-owned vs. Organization-owned | Can't be changed after creation; drives the whole [security model](security-model.md). | User/Team-owned unless the data is genuinely org-global reference data. |
| **Table type** — standard / activity / elastic / virtual | Type is fixed at creation. | Standard. Use *activity* tables for things that appear in a timeline; *elastic* only for high-volume/high-throughput telemetry-style data; *virtual* for external data (see [Custom API & Data Providers](../coding/serverside/custom-api.md)). |
| **Primary column** | Hard to repurpose later. | A meaningful human-readable name; if the record is identified by a generated number, keep the primary name column *and* add the number column. |

## Elastic tables for high-volume data

**`DGT-CUS-210`**{ #dgt-cus-210 } — High-volume, telemetry-style data (logs, events, IoT
readings) goes into an **elastic table with a TTL column**, never into a standard table.
Standard tables accumulate storage and index cost forever; an elastic table auto-expires rows
via time-to-live. Know the trade-offs before choosing elastic — they change how server-side
logic must be written:

- No multi-record transactions; a plugin exception does **not** roll back the write, so
  validation must run in **PreValidation**.
- `partitionid` is immutable per row — choose the partitioning strategy deliberately up front.

See [Microsoft's elastic tables documentation](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/elastic-tables).

## Columns

- **`DGT-CUS-050`**{ #dgt-cus-050 } — **Pick the narrowest correct data type.** The type drives storage, the early-bound model, and
  the form control — see the [field-suffix table](naming-conventions.md#column-field-suffixes),
  which doubles as the list of available types.
- **Prefer global choices** (option sets) over local ones for any value list used on more than
  one column — they're reusable and generate a single shared enum in the
  [early-bound model](../coding/serverside/early-binding.md). Name them per the conventions.
- **`DGT-CUS-060`**{ #dgt-cus-060 } — **Don't store what you can derive.** Use **rollup**, **calculated**, and **formula** columns
  for derived values instead of a plugin writing a plain column — and mark them
  `IsCustomizable = false` (see [Naming Conventions](naming-conventions.md#further-recommendations)).
  Reach for a plugin only when the logic exceeds what a formula column can express.
- **Alternate keys** carry integration identity — define one for any column an external system
  uses to address a record (e.g. a foreign system id), and name it `prfx_..._key`.
- Give every table and column a **description**; it's the cheapest documentation that travels
  with the schema.

## Relationships

- **`DGT-CUS-070`**{ #dgt-cus-070 } — **1:N** is the default. Choose the cascade behavior deliberately: **parental** cascades
  share/assign/delete down the hierarchy (powerful, but a deep parental chain is expensive and
  hard to reason about); **referential** just links. Default to referential and only use
  parental where the child genuinely has no meaning without the parent.
- **N:N** — use the native many-to-many only when you need no extra data on the link. The moment
  the relationship itself has attributes (a role, a date, a quantity), model an **intermediate
  table** with two 1:N relationships instead.
- **Polymorphic / Customer** lookups (`_vid`) target more than one table — useful, but they
  complicate queries and the early-bound model, so don't reach for them when a single-target
  lookup will do.

!!! tip "Model for the early-bound generator"
    Every table, column, choice, and relationship here becomes generated `.cs`/`.ts` via
    [`dgtp codegeneration`](../coding/serverside/early-binding.md). Clean, conventionally-named
    schema produces a clean model; inconsistent schema produces a model developers fight.
