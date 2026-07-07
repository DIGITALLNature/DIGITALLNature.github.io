# Performance & Release Waves

Two kinds of testing that don't map to a single user story, and therefore tend to fall through
the cracks of a sprint-driven backlog: load behavior before go-live, and regression against
platform updates that arrive on Microsoft's schedule rather than yours.

## Performance testing before go-live

Plan a performance test under realistic production load as part of go-live readiness — form
load times, search, and integration throughput are **non-functional acceptance criteria**, not
things to discover in week one of production. Factor
[service protection limits](../coding/cloud-integration.md#dataverse-service-protection-limits)
into any load scenario that exercises integrations. See
[Microsoft's go-live preparation guidance](https://learn.microsoft.com/en-us/dynamics365/guidance/implementation-guide/prepare-to-go-live).

## Release wave regression

**`DGT-TST-060`**{ #dgt-tst-060 } — Microsoft ships two release waves per year that reach
production environments automatically. Activate each wave's **Early Access** in a sandbox as
soon as it is available and run a regression pass (forms, command bar, critical flows and
plugins) **before** the wave reaches production — a wave regression discovered in production is
an incident; the same regression discovered in a sandbox is a backlog item. See the
[release schedule](https://learn.microsoft.com/en-us/dynamics365/get-started/release-schedule).

The [deprecation radar](../operations/monitoring.md#platform-lifecycle-deprecations) in
Operations is the standing counterpart: the radar tells you what to look for, the regression
run tells you whether it bites.
