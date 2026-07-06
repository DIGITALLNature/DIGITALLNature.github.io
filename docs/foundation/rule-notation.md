# Rule Notation

We keep this guideline lightweight: there is no RFC 2119 MUST/SHOULD/MAY apparatus, and no
requirement to phrase every sentence as a formal rule. Most of this guideline is plain
prose, examples, and recommendations.

Where a rule is specific enough that you'd want to point to it from architectural
documentation — e.g. *"deviates from `DGT-SRV-010`"* — it gets a short, stable **rule ID**
so the reference doesn't break when headings get reworded. Not every paragraph needs one; use
your judgment for what's worth pinning down.

## Format

```text
DGT-<AREA>-<NUMBER>
```

| Area | Used for |
|---|---|
| `FND` | Foundation |
| `ARC` | Architecture |
| `CUS` | Customizing |
| `SRV` | Server-side coding |
| `CLI` | Client-side coding |
| `INT` | Cloud & integration |
| `FLW` | Power Automate flows |
| `TST` | Testing & quality |
| `ALM` | ALM & deployment |
| `OPS` | Operations |

Numbers are assigned per area, ascending, and are **never reused or renumbered** even if the
rule is later removed — a removed rule ID is retired, not recycled, so old references in
project documentation stay unambiguous (they'll just point to a rule marked as retired).

## Example

> **`DGT-SRV-010`** — Plugin and Custom API projects target the Dataverse plugin package
> format (`.nupkg` via dependent assemblies), not classic single-assembly registration. See
> [Plugin Packages](../coding/serverside/plugin-packages.md).

In architectural documentation, a deviation would then read:

> Deviates from `DGT-SRV-010`: this project keeps the legacy single-assembly plugin because it
> is shared with a non-Dataverse host process. Reviewed with the architects' circle on
> 2026-05-04.

## Where rule IDs show up

Rule IDs are introduced gradually as chapters are written or revised — they are not retrofitted
onto the entire guideline in one pass. If a chapter you're reading doesn't use them yet, that's
expected at this stage, not an inconsistency to fix.

The [Rules Index](../reference/rules.md) lists every ID assigned so far, with a link to the rule
it names.
