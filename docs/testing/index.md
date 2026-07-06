# Testing & Quality

- [Server-side Unit Testing](unit-testing-serverside.md) — `Digitall.Dataverse.Testing` against
  plugins and Custom APIs, with no live environment required.
- [Solution Checker](solution-checker.md) — static analysis as a build-pipeline quality gate.
- [Client-side Testing](clientside-testing.md)
- [Definition of Ready / Done](dor-dod.md)

These quality gates plug into [Build Pipeline](../alm/build-pipeline.md) at steps 8 and 9 — see
that page for exactly where each one runs relative to packaging and deployment.

## Release wave regression

**`DGT-TST-060`**{ #dgt-tst-060 } — Microsoft ships two release waves per year that reach
production environments automatically. Activate each wave's **Early Access** in a sandbox as
soon as it is available and run a regression pass (forms, command bar, critical flows and
plugins) **before** the wave reaches production — a wave regression discovered in production is
an incident; the same regression discovered in a sandbox is a backlog item. See the
[release schedule](https://learn.microsoft.com/en-us/dynamics365/get-started/release-schedule).
