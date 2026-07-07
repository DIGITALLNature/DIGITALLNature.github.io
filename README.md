# DIGITALL Power Platform Guidelines

Project guidelines for customizers and developers working on Microsoft Power Platform / Dataverse
projects at DIGITALL.

📖 **Published site: <https://digitallnature.github.io/>**

This repository holds the source for that site — a [MkDocs](https://www.mkdocs.org/) /
[Material](https://squidfunk.github.io/mkdocs-material/) static site that is the single, current
reference for how we build, test, and ship Dataverse solutions. It covers:

- **Foundation** — scope & principles, rule notation, governance, glossary
- **Architecture** — solution architecture, publisher/prefix, environment strategy, deployment approach
- **Setup & Onboarding** — toolchain, the `dgtp` CLI, environment provisioning, repository bootstrap
- **Customizing** — naming, tables/columns/relationships, forms/views/BPF, command bar, themes, security
- **Coding** — server-side (plugins, Custom APIs, `Digitall.Plugins`) and client-side (TypeScript web resources, PCF)
- **Testing & Quality**, **ALM & Deployment**, **Operations**, and a **Reference** section

New to a project? Start with the [Quickstart](https://digitallnature.github.io/quickstart/).

## Repository layout

```text
docs/               # all guideline content (Markdown), one folder per chapter
mkdocs.yml          # site configuration and navigation
requirements.txt    # Python build dependencies (MkDocs Material + plugins)
.github/workflows/  # deploy.yml — builds & publishes the site
```

## Local development

Requires **Python 3.x**. A virtual environment is recommended:

```bash
python -m venv .venv
# Windows: .\.venv\Scripts\Activate.ps1   |   macOS/Linux: source .venv/bin/activate

pip install -r requirements.txt

mkdocs serve            # live preview at http://127.0.0.1:8000
mkdocs build --strict   # production build; fails on broken links or nav
```

## Deployment

Pushing to `main` triggers [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml), which
installs the dependencies and runs `mkdocs gh-deploy --force` to build the site and publish it to
the `gh-pages` branch — served at <https://digitallnature.github.io/>. No manual step is required.

## Contributing

Found something outdated, missing, or wrong? Edit the relevant file under `docs/` and open a pull
request, or raise it in the architects' circle or a GitHub discussion. See
[Governance & Contribution](https://digitallnature.github.io/foundation/governance/) for the change
process.

Keep contributions in the established style: concise, practical, cross-linked, and grounded in our
tooling — these are the **minimum standards** for new projects, and deliberate deviations are
documented in a project's own architectural documentation rather than changed here unilaterally.

## Related repositories

The tooling these guidelines build on:

- [`dgt.power`](https://github.com/DIGITALLNature/DigitallPower) (`dgtp`) — CLI for code generation, assembly/package push, solution maintenance, and data export/import
- [`Digitall.Plugins`](https://github.com/DIGITALLNature/DigitallAssemblyPower) — base classes for plugins, Custom APIs, and workflow activities
- [`Digitall.Plugins.Registration`](https://github.com/DIGITALLNature/DigitallRegistrationPower) — declarative plugin/step registration attributes
- [`Digitall.Dataverse.Testing`](https://github.com/DIGITALLNature/DigitallTesting) — in-memory `IOrganizationService` for fast plugin unit tests
- [`webresources-template`](https://github.com/DIGITALLNature/webresources-template) — TypeScript/webpack starter for Dataverse web resources

---

Copyright &copy; DIGITALL Nature
