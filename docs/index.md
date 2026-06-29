# DIGITALL Power Platform Guidelines

Welcome to the DIGITALL guard rails for the Microsoft Power Platform.

This is a constantly growing framework for our projects, the systems we operate, and the
experience and approaches we have built up along the way. It is written for two audiences
working side by side on the same Dataverse environment:

- **Customizers** — makers configuring tables, forms, processes, and other low-code assets.
- **Developers** — engineers writing plugins, Custom APIs, web resources, and PCF controls.

These are the **minimum standards** for new projects. Deviations are permitted, but they must
be documented and justified in the project's architectural documentation. See
[Scope & Principles](foundation/scope-and-principles.md) for how that works in practice.

!!! tip "Contributing"
    Found something outdated, missing, or wrong? Raise it in the architects' circle, open a
    GitHub discussion, or send a pull request directly. See [Governance & Contribution](foundation/governance.md).

## Where to start

| If you are... | Start here |
|---|---|
| New to a project | [Setup & Onboarding](setup/index.md) |
| Configuring tables, forms, processes | [Customizing](customizing/index.md) |
| Writing plugins, Custom APIs, or web resources | [Coding](coding/serverside/index.md) |
| Setting up builds and releases | [ALM & Deployment](alm/index.md) |
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
[Solution Concept](architecture/solution-concept.md) and is not part of this rewrite.