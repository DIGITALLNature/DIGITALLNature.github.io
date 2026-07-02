# Cloud & Integration

Guidance for the parts of a project that sit outside Dataverse itself — Azure Functions, Logic
Apps, API Management/gateways, and similar integration components referenced in
[Solution Architecture](../architecture/solution-architecture.md#repositories-per-component-category).

## Choose the right place for the logic

Before writing an integration, decide *where* it runs — this is the highest-leverage decision:

| Need | Use |
|---|---|
| Synchronous business rule on a Dataverse write, fast and transactional | **Plugin** ([server-side](serverside/index.md)) |
| Reaction to a Dataverse event, no strict latency need, citizen-maintainable | **[Power Automate](https://learn.microsoft.com/en-us/power-automate/getting-started) cloud flow** |
| Long-running, fan-out, heavy compute, or orchestration across systems | **Azure** (Functions / Logic Apps / Service Bus) |
| Synchronous call *out* to an external system from a write | **Plugin** + the HTTP/Key Vault helpers below — but mind the 2-minute sandbox limit |

**`DGT-SRV-140`**{ #dgt-srv-140 } — The platform sandbox kills a plugin at **two minutes** and you don't control retry
semantics finely — anything slow, bursty, or that must not block the user belongs in async (a flow, or
Azure behind a queue), not in a synchronous plugin.

## Calling out from a plugin

When a plugin *does* call an external service, use the
[`Digitall.APower`](serverside/digitall-assembly.md#add-on-modules) modules rather than
hand-rolling `HttpClient` and auth:

- **`dgt.apower.keyvault`** — pull secrets from Azure Key Vault instead of storing them in
  plugin config or environment variables.
- the bundled HTTP/token helpers acquire OAuth tokens (Microsoft Online / ACS) for you.
- For token-free auth, prefer a **managed identity** via
  [`[ManagedIdentityRegistration]`](serverside/custom-api.md#managed-identity) over a stored
  client secret.

These follow the platform rule of a single, connection-closed, lazily-created `HttpClient` —
which is exactly why you shouldn't reimplement it per plugin.

## Eventing out of Dataverse

- **Azure Service Bus / event publishing** via the service-endpoint registration is the
  supported way to push Dataverse events to Azure asynchronously — prefer it over a plugin that
  POSTs to an external endpoint synchronously.
- **Webhooks** suit a real-time, synchronous external call, but the remote endpoint then sits in
  your transaction's critical path — treat its latency and failure as your plugin's.

## Bringing external data in

- **Virtual tables** surface external data as Dataverse rows read-only (or read/write) — see
  [Custom API & Data Providers](serverside/custom-api.md#custom-data-providers-virtual-tables).
- **Custom connectors** wrap an external API for use from flows and canvas apps; keep their
  definitions in source control with the rest of the project.

## ALM for these components

Azure-side components live in their own repositories per
[Solution Architecture](../architecture/solution-architecture.md#repositories-per-component-category),
but follow the **same** [ALM conventions](../alm/index.md) — source control, SemVer, CI, and
gated deployment — adapted to their stack (Bicep/Terraform, Functions deploy, etc.). Connectors
and connection references they depend on are governed by the active
[DLP policy](../operations/governance-dlp.md) — check it before building, not after.
