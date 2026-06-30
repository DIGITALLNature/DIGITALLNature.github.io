# DIGITALL Power Platform Guidelines

Welcome to the DIGITALL guard rails for the [Microsoft Power Platform](https://learn.microsoft.com/en-us/power-platform/).

This is a constantly growing framework for our projects, the systems we operate, and the
experience and approaches we have built up along the way. Its core audiences are the two roles
working side by side on the same Dataverse environment:

- **Customizers** — makers configuring tables, forms, processes, and other low-code assets.
- **Developers** — engineers writing plugins, Custom APIs, web resources, and PCF controls.

It equally serves the **architects** who set the structure those two work within, and the
**project leads, analysts, and testers** who scope and validate the work — the
[Where to start](#where-to-start) table points each role to its entry point.

These are the **minimum standards** for new projects. Deviations are permitted, but they must
be documented and justified in the project's architectural documentation. See
[Scope & Principles](foundation/scope-and-principles.md) for how that works in practice.

!!! tip "Contributing"
    Found something outdated, missing, or wrong? Raise it in the architects' circle, open a
    GitHub discussion, or send a pull request directly. See [Governance & Contribution](foundation/governance.md).

## Where to start

Pick the entry point for your perspective:

| Perspective | Start here |
|---|---|
| **New to a project** (any role) | [Setup & Onboarding](setup/index.md), then the [Quickstart](quickstart.md) |
| **Customizer** — tables, forms, processes, low-code | [Customizing](customizing/index.md) |
| **Developer** — plugins, Custom APIs, web resources, PCF | [Coding](coding/serverside/index.md) + the [Quickstart](quickstart.md) |
| **Architect** — solution, environment & deployment design | [Architecture](architecture/index.md) + [Foundation](foundation/index.md) |
| **Project lead / analyst / tester** — scope, process, quality gates | [Foundation](foundation/index.md) + [Definition of Ready / Done](testing/dor-dod.md) |
| Looking for a command or template | [Reference](reference/index.md) |

## Our tooling

Several chapters of this guideline build directly on tooling maintained by DIGITALL:

- **[dgt.power](https://github.com/DIGITALLNature/DigitallPower)** (`dgtp`) — CLI for code
  generation, assembly/package push, solution maintenance, and Dataverse data export/import.
- **[Digitall.APower](https://github.com/DIGITALLNature/DigitallAssemblyPower)** — base
  classes and abstractions for plugins, Custom APIs, and workflow assemblies.
- **[Digitall.Plugins.Registration](https://github.com/DIGITALLNature/DigitallRegistrationPower)** —
  attributes for declarative, automated step registration.
- **[Digitall.Dataverse.Testing](https://github.com/DIGITALLNature/DigitallTesting)** —
  in-memory `IOrganizationService` for fast, deterministic plugin unit tests.

Our Carrier/Workbench solution model is documented separately in
[Solution Concept](architecture/solution-concept.md).