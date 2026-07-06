# Cloud & Integration

Guidance for the parts of a project that sit outside Dataverse itself — Azure Functions, Logic
Apps, API Management/gateways, and similar integration components referenced in
[Solution Architecture](../architecture/solution-architecture.md#repositories-per-component-category).

## Choose the right place for the logic

Before writing an integration, decide *where* it runs — this is the highest-leverage decision:

| Need | Use |
|---|---|
| Synchronous business rule on a Dataverse write, fast and transactional | **Plugin** ([server-side](serverside/index.md)) |
| Reaction to a Dataverse event, no strict latency need, citizen-maintainable | **[Power Automate cloud flow](flows.md)** |
| Long-running, fan-out, heavy compute, or orchestration across systems | **Azure** (Functions / Logic Apps / Service Bus) |
| Synchronous call *out* to an external system from a write | **Plugin** + the HTTP/Key Vault helpers below — but mind the 2-minute sandbox limit |

**`DGT-SRV-140`**{ #dgt-srv-140 } — The platform sandbox kills a plugin at **two minutes** and you don't control retry
semantics finely — anything slow, bursty, or that must not block the user belongs in async (a flow, or
Azure behind a queue), not in a synchronous plugin.

## Calling out from a plugin

When a plugin *does* call an external service, prefer a **managed identity** via
[`[ManagedIdentityRegistration]`](serverside/custom-api.md#managed-identity) over a stored
client secret or Key Vault lookup — `Digitall.Plugins` no longer ships Key Vault/SharePoint/HTTP
helper modules (removed in `2.0.0`; see
[DIGITALL Assembly → Add-on modules](serverside/digitall-assembly.md#add-on-modules)), so calling
another service means either the managed-identity path above or calling the target SDK/REST API
directly with your own `HttpClient`.

**`DGT-INT-040`**{ #dgt-int-040 } — Use a **single, lazily-created, shared `HttpClient`** (with
connection keep-alive disabled, see below) regardless of which path you take — never create one
per invocation. Per-call clients exhaust sockets under load and are one of the standard causes
of intermittent outbound-call failures from plugins.

**`DGT-INT-020`**{ #dgt-int-020 } — Every external call from a plugin sets an **explicit
timeout** (well under the two-minute sandbox budget, so *your* error handling runs instead of
the sandbox killing the worker) and uses **`KeepAlive = false`** — the sandbox recycles
processes, and kept-alive connections to a recycled worker surface as sporadic, unreproducible
failures. See
[Microsoft's guidance on external calls](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/best-practices/business-logic/set-timeout-for-external-calls-from-plug-ins).

## Dataverse service protection limits

Dataverse throttles API consumers per user and web server in a 5-minute sliding window: 6,000
requests, 20 minutes combined execution time, ~52 concurrent requests — see
[Microsoft's service protection documentation](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/api-limits).

**`DGT-INT-010`**{ #dgt-int-010 } — Every integration that talks to Dataverse **must handle
HTTP 429 with the `Retry-After` header** — an integration that treats 429 as a fatal error, or
retries immediately in a tight loop, fails exactly when load is highest. Use
`Microsoft.PowerPlatform.Dataverse.Client.ServiceClient`, which honors `Retry-After`
automatically; hand-rolled HTTP clients must implement the same backoff.

**`DGT-INT-030`**{ #dgt-int-030 } — Never use the **Dataverse Search API** for bulk or
programmatic mass queries — it is rate-limited to roughly one request per second per user and
is built for interactive search. Bulk reads use the Web API / SDK with paging, or
[FetchXML aggregation](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/api-limits).

## Eventing out of Dataverse

- **`DGT-INT-050`**{ #dgt-int-050 } — Push Dataverse events to Azure **asynchronously via the
  service-endpoint registration** (Azure Service Bus / event publishing) — not with a plugin
  that POSTs to an external endpoint synchronously. The service endpoint gives you durable
  delivery and retry from the platform; a synchronous POST makes the remote system's downtime
  your transaction's downtime.
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
