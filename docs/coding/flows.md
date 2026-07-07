# Power Automate Flows

Cloud flows are the "citizen-maintainable reaction to a Dataverse event" cell in the
[where-does-the-logic-run table](cloud-integration.md#choose-the-right-place-for-the-logic).
Once a flow is part of a delivered solution, it is production code and follows the same ALM and
quality expectations as a plugin — this chapter covers the flow-specific rules.

## Solution-aware, always

**`DGT-FLW-010`**{ #dgt-flw-010 } — Create flows **inside a Dataverse solution**, never as
standalone ("My flows") artifacts. Only solution-aware flows participate in ALM: they deploy
through [pipelines](../alm/index.md), use
[connection references](../customizing/envvars-connectionrefs.md) instead of hard-wired
personal connections, and read [environment variables](../customizing/envvars-connectionrefs.md)
instead of hard-coded URLs and ids.

## Error handling

**`DGT-FLW-020`**{ #dgt-flw-020 } — No production flow without error handling. Model a
try/catch: group the main logic in a **Scope**, follow it with a scope configured to **run
after** *has failed / has timed out*, and notify a monitored channel (not a personal mailbox)
from the catch branch. An unhandled failed run that nobody notices is the flow equivalent of a
swallowed exception. See
[Microsoft's error-handling guidance](https://learn.microsoft.com/en-us/power-automate/guidance/coding-guidelines/error-handling).

Also configure each action's **retry policy** deliberately rather than accepting the default
(exponential, four attempts): for idempotent actions the default is fine or can be extended;
for non-idempotent actions (creating records, sending mails) disable the retry and compensate
in the catch branch instead — an automatic retry of a non-idempotent action is a duplicate.

## Triggers

**`DGT-FLW-030`**{ #dgt-flw-030 } — Filter in the **trigger**, not with a condition as the first
action: use **trigger conditions**, and on Dataverse triggers set **Select columns** and
**Filter rows**. A flow that triggers on every update and immediately terminates still consumes
a run and API requests — and an unfiltered *update → flow → update* pair is the standard recipe
for a trigger loop. See the
[Dataverse trigger documentation](https://learn.microsoft.com/en-us/power-automate/dataverse/create-update-delete-trigger).

## Loops & concurrency

**`DGT-FLW-040`**{ #dgt-flw-040 } — Treat concurrency and pagination settings as code, not as
defaults to discover in production: variables written inside an **Apply to each** are not
thread-safe once concurrency is enabled (use *Select*/*Filter array* composition instead of a
shared variable, or leave concurrency off); list actions return **100 items** by default —
raise the pagination limit explicitly when more rows are expected. See
[Microsoft's limits guidance](https://learn.microsoft.com/en-us/power-automate/guidance/coding-guidelines/understand-limits).

## Know what flows are not for

Flows are not a high-volume ETL tool. Power Automate has its own
[throttling and duration limits](https://learn.microsoft.com/en-us/power-automate/limits-and-config)
(actions per day per license, payload sizes, maximum run duration) that are easy to hit with
data-migration-style workloads — those belong in the
[Azure/data tooling row](cloud-integration.md#choose-the-right-place-for-the-logic) of the
where-does-the-logic-run decision, not in a flow with a loop.
