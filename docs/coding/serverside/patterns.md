# Patterns & Pitfalls

A running list of project-tested guidance that doesn't fit cleanly under Project Setup,
Plugin Packages, or Registration Attributes on their own.

## Plugins are stateless — let `Executor` handle it

**`DGT-SRV-120`**{ #dgt-srv-120 } — [`Executor.Execute(IServiceProvider)`](digitall-assembly.md) clones itself before
invoking your `Execute()` override, so per-invocation state never leaks across calls to the same registered
step even though Dataverse may reuse the plugin type instance. You still need to avoid storing
mutable state in `static` fields — the clone-per-call only protects instance fields.

## Prefer `ExecutionResult.Skipped` over silent no-ops

When a plugin determines early on that there's nothing to do (e.g. the triggering attribute
didn't actually change in a way that matters), return `ExecutionResult.Skipped` rather than
just falling through. This is directly assertable in
[unit tests](../../testing/unit-testing-serverside.md) and makes traced logs distinguish "ran
and did nothing" from "ran and succeeded."

## Use `InvalidPluginExecutionException` deliberately for user-facing messages

**`DGT-SRV-130`**{ #dgt-srv-130 } — `Executor` treats an `InvalidPluginExecutionException` with `Status = OperationStatus.Succeeded`
as a non-error result (`ExecutionResult.Ok`) before re-throwing — this is the supported pattern
for surfacing a validation message to the user without it being logged/traced as a failure. Use
it for that purpose specifically, not as a general-purpose control-flow exception.

## Filter attributes instead of re-checking inside the plugin

**`DGT-SRV-100`**{ #dgt-srv-100 } — Use `[PluginRegistration(... FilterAttributes = new[] { Account.LogicalNames.Name })]`
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

## Keep dependent-plugin shared models nullable-disabled

**`DGT-SRV-160`**{ #dgt-srv-160 } — If multiple plugin packages share a generated early-bound model (rather than each
generating its own), keep `SuppressNullableSupport: true` (see
[Early-Bound Models](early-binding.md)) consistent across every project consuming that shared
model — a mismatch here is a common source of confusing compiler warnings/errors that look
unrelated to the actual change that introduced them.
