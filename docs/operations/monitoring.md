# Monitoring & Telemetry

## Plugin telemetry

**`DGT-OPS-010`**{ #dgt-ops-010 } — Enable **Application Insights** integration via "Activate Data Export" in the Power Platform
admin center for every production environment. Once enabled,
[`ServiceProvider.GetLogger(LogSink.PluginTelemetry, ...)`](../coding/serverside/digitall-assembly.md#add-on-modules)
(available on both `Executor` and `PluginSkeleton`) writes to it — use it for anything beyond the
basic trace logging that `GetTracingService()` gives you, especially structured events you'd
want to query or alert on later rather than just read in a single trace log.

## Capacity

Watch Dataverse capacity — **database, file, and log are metered separately** — as part of the
operations baseline. Exceeding storage capacity blocks environment lifecycle operations
(copy, restore, create), so a capacity problem surfaces at the worst possible moment: when you
need a restore. Audit logs and attachments are the usual silent drivers; see
[capacity and storage](https://learn.microsoft.com/en-us/power-platform/admin/capacity-storage).

## Platform lifecycle & deprecations

Make a deprecation review a recurring ritual (e.g. once per sprint or monthly): check
[Microsoft's "important changes coming" page](https://learn.microsoft.com/en-us/power-platform/important-changes-coming)
and the release wave notes against what the project actually uses, and turn hits into backlog
items. This pairs with the
[release wave regression](../testing/performance-and-release-waves.md#release-wave-regression)
in a sandbox — the radar tells you what to look for; the regression run tells you whether it
bites.

## Trace logs vs. telemetry

Use `ServiceProvider.GetTracingService().Trace(...)` for step-by-step diagnostic detail you'd
want in a single plugin trace log entry when debugging a specific failed execution; use
`GetLogger(LogSink.PluginTelemetry)` for events you want to aggregate, alert on, or correlate
across executions. Don't leave the **plugin trace log** set to "All" permanently in
production — it writes to the database and eats log capacity; enable it while investigating,
then turn it back down. Telemetry that should run permanently belongs in Application Insights. They aren't redundant — a plugin typically uses both, and
`GetLogger(LogSink.PluginTelemetry, LogSink.TracingService)` writes to both at once.
`PluginSkeleton` logs its own start/end/failure events to both sinks automatically (see
[DIGITALL Assembly](../coding/serverside/digitall-assembly.md#add-on-modules)); either base
class lets you add your own log lines beyond that.

## What to monitor as a baseline

**`DGT-OPS-020`**{ #dgt-ops-020 } — Every production environment has at least this monitoring
baseline in place, with findings routed to a channel someone actually watches:

- Plugin/Custom API failures and execution time, via Application Insights.
- Flow run failures, via the standard Power Automate run history and/or Application Insights
  if integrated.
- Solution Checker trend over time (are findings increasing release over release) — see
  [Solution Checker](../testing/solution-checker.md).
- Pipeline deployment history and approvals, via
  [Power Platform Pipelines](../alm/power-platform-pipelines.md) reporting or your CI
  platform's own run history.

!!! info "Project-specific depth"
    Beyond this baseline, monitoring depth is genuinely project-specific (SLAs, customer
    requirements, criticality). Document the chosen approach in the project's architectural
    documentation rather than expecting this guideline to prescribe it in detail.
