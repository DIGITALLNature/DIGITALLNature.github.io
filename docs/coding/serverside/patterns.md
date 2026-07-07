# Patterns & Pitfalls

A running list of project-tested guidance that doesn't fit cleanly under Project Setup,
Plugin Packages, or Registration Attributes on their own.

## Plugins are stateless — let `Executor` handle it

**`DGT-SRV-130`**{ #dgt-srv-130 } — [`Executor.Execute(IServiceProvider)`](digitall-assembly.md) clones itself before
invoking your `Execute()` override, so per-invocation state never leaks across calls to the same registered
step even though Dataverse may reuse the plugin type instance. You still need to avoid storing
mutable state in `static` fields — the clone-per-call only protects instance fields.

## No batch requests inside plugins

**`DGT-SRV-140`**{ #dgt-srv-140 } — Don't use `ExecuteMultipleRequest` or
`ExecuteTransactionRequest` inside a plugin. The plugin already runs inside the pipeline
transaction — batching gains nothing there and only extends how long the transaction is held
open. Batch APIs are for external clients and integrations, not for sandbox code. See
[Microsoft's business-logic best practices](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/best-practices/business-logic/).

## No custom threading inside plugins

**`DGT-SRV-150`**{ #dgt-srv-150 } — Don't spawn threads or use `Parallel.*`/`Task.Run` inside a
plugin — [parallel execution in sandbox code is not supported](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/best-practices/business-logic/)
and produces failures that don't reproduce locally. If work is too slow for the synchronous
pipeline, it belongs in an asynchronous step or outside Dataverse entirely (see
[Cloud & Integration](../cloud-integration.md)), not on a background thread.

## Synchronous steps on reads are exceptional

**`DGT-SRV-160`**{ #dgt-srv-160 } — Register synchronous steps on `Retrieve` or
`RetrieveMultiple` only in justified exceptions. Such a step runs on **every** read of the
table — every view, every subgrid, every lookup — and its latency is added to all of them.
Custom data providers for [virtual tables](custom-api.md) are the intended mechanism
for shaping reads; a `RetrieveMultiple` plugin is almost always the wrong layer.

## Don't use `context.Depth` as loop control

**`DGT-SRV-170`**{ #dgt-srv-170 } — Don't write `if (context.Depth > 1) return;` to "prevent
loops" — it also silently disables your plugin for every legitimate cascade, workflow, or other
plugin that triggers it. Prevent update loops with
[`FilterAttributes`](#filter-attributes-instead-of-re-checking-inside-the-plugin) and precise
registration instead; the platform's infinite-loop protection is a safety net, not a design
element. See
[Microsoft's guidance on the execution context](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/understand-the-data-context).

## Query only the columns you need

**`DGT-SRV-180`**{ #dgt-srv-180 } — Every query states an explicit `ColumnSet` with the columns
the code actually reads — never `new ColumnSet(true)` / `AllColumns`. Wide queries burn
[sandbox worker memory](https://learn.microsoft.com/en-us/troubleshoot/power-platform/dataverse/plug-in-execution/dataverse-plug-ins-errors),
transfer data you throw away, and are a
[query-throttling](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/query-throttling)
risk on large tables.

## Prefer `ExecutionResult.Skipped` over silent no-ops

When a plugin determines early on that there's nothing to do (e.g. the triggering attribute
didn't actually change in a way that matters), return `ExecutionResult.Skipped` rather than
just falling through. This is directly assertable in
[unit tests](../../testing/unit-testing-serverside.md) and makes traced logs distinguish "ran
and did nothing" from "ran and succeeded."

## Use `InvalidPluginExecutionException` deliberately for user-facing messages

**`DGT-SRV-190`**{ #dgt-srv-190 } — `Executor` treats an `InvalidPluginExecutionException` with `Status = OperationStatus.Succeeded`
as a non-error result (`ExecutionResult.Ok`) before re-throwing — this is the supported pattern
for surfacing a validation message to the user without it being logged/traced as a failure. Use
it for that purpose specifically, not as a general-purpose control-flow exception.

## Filter attributes instead of re-checking inside the plugin

**`DGT-SRV-200`**{ #dgt-srv-200 } — Use `[PluginRegistration(... FilterAttributes = new[] { Account.LogicalNames.Name })]`
rather than registering an unfiltered step and checking `if (target.Contains(Account.LogicalNames.Name))` at the
top of `Execute()`. The
filtered registration means the step doesn't fire — and doesn't consume a pipeline execution —
when an unrelated field changes, which matters for both performance and for keeping plugin
trace logs free of no-op runs.

## Prefer formula and calculated fields over rollups where equivalent

Where a value can be expressed as a formula or calculated column, prefer that over a rollup
field — rollups carry an asynchronous recalculation job and tighter limits per environment,
and are harder to reason about in a managed-solution context (see
[Naming Conventions](../../customizing/naming-conventions.md) for the `IsCustomizable = false`
recommendation on these). Reach for a rollup only when the aggregation genuinely needs it
(e.g. cross-record aggregation a formula column can't express).

## Don't bypass `dgtp push` registration with manual changes

If you find yourself about to add or edit a step in the Plugin Registration Tool directly,
stop — see [Registration Attributes](registration-attributes.md#why-this-matters-for-alm). The
fix for "the tool doesn't let me express X declaratively yet" is to raise it against
`Digitall.Plugins.Registration`, not to special-case that one step manually outside source
control.

## Don't hand-roll pre-image diffing

Use [`IsEntityAttributeValueChanged<T>`/`IsEntityAttributeValueNew`/`GetEntityAttributeValue<T>`](digitall-assembly.md#comparing-target-vs-pre-image)
instead of writing your own `target.Contains(...)`/`preImage.Contains(...)`/null-check chain to
answer "did this field actually change" — it's easy to get the "new but no pre-image" and
"unchanged empty string vs. null" edge cases subtly wrong by hand, and these are already
covered.

## Keep dependent-plugin shared models nullable-disabled

**`DGT-SRV-210`**{ #dgt-srv-210 } — If multiple plugin packages share a generated early-bound model (rather than each
generating its own), keep `SuppressNullableSupport: true` (see
[Early-Bound Models](early-binding.md)) consistent across every project consuming that shared
model — a mismatch here is a common source of confusing compiler warnings/errors that look
unrelated to the actual change that introduced them.
