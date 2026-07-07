# Testing & Quality

- [Server-side Unit Testing](unit-testing-serverside.md) — `Digitall.Dataverse.Testing` against
  plugins and Custom APIs, with no live environment required.
- [Solution Checker](solution-checker.md) — static analysis as a build-pipeline quality gate.
- [Client-side Testing](clientside-testing.md)
- [Definition of Ready / Done](dor-dod.md)
- [Performance & Release Waves](performance-and-release-waves.md) — load testing before
  go-live, and regression against Microsoft's release waves.

These quality gates plug into [Build Pipeline](../alm/build-pipeline.md) at steps 8 and 9 — see
that page for exactly where each one runs relative to packaging and deployment.
