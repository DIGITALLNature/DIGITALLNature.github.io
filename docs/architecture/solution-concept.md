# Solution Concept (Carrier / Workbench)

Our Carrier/Workbench model for separated customizing is documented in detail elsewhere and is
**not** part of this rewrite — this page only places it in context relative to the rest of
this guideline, and links onward.

## In short

- **Carrier** — a solution prepared for deployment (a transport solution) that artifacts get
  staged into across environments.
- **Workbench** — a temporary, self-contained solution scoped to a single unit of work, which
  a maker merges into a target Carrier once the change is ready, then closes.
- **Provisioning** — the mechanism that merges changes between Workbenches and Carriers, with
  automatic semantic versioning.

## Where it fits relative to this guideline

- It sits *above* [Solution Architecture](solution-architecture.md): Carriers are the
  deployment unit, while solution layering (core/server/client/apps) describes what goes inside
  them.
- It complements rather than replaces [ALM & Deployment](../alm/index.md) — a project using the
  Carrier/Workbench model still follows the same [Build Pipeline](../alm/build-pipeline.md) and
  [Versioning](../alm/versioning.md) conventions for anything outside pure low-code customizing.
- `dgtp maintenance carrierinfo` exports active Carriers from an environment for inspection —
  see [Reference](../reference/dgtp-commands.md).

## Full documentation

For day-to-day usage (creating a Workbench, merging into a Carrier, finalizing a release) and
configuration (installing the solution, setting up a Carrier), see the dedicated tutorials and
the [DigitallSolutions releases](https://github.com/DIGITALLNature/DigitallSolutions/releases).
