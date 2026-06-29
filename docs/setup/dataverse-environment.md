# Dataverse Environment

## Provisioning a development environment

1. Create the environment (Power Platform admin center, or `pac admin create` for automation)
   with a Dataverse database.
2. Enable **Managed Environments** immediately — see
   [Environment Strategy](../architecture/environment-strategy.md). Don't defer this for a dev
   environment "because it's just dev"; pipelines source environments benefit from it too, and
   it avoids a forced migration later.
3. Create the project's [publisher](../architecture/publisher-and-prefix.md) and a root
   solution before any customizing starts — never customize against the Default Solution.
4. Install the apps the project needs (model-driven apps are usually created fresh, but Dynamics
   365 first-party apps like Sales/Customer Service are installed here if in scope).
5. Create a `dgtp` [profile](dgtp-cli.md#profiles) pointing at the new environment.

## D365 first-party apps

If the project scope includes a Dynamics 365 application (Sales, Customer Service, Field
Service, ...), install it explicitly rather than assuming it's already present — trial and
sandbox environments don't always come with it pre-installed, and the app's own solution
introduces dependencies that affect [solution layering](../architecture/solution-architecture.md).

## Branding

- Give every instance/stage/app its own color theme, so screenshots, support tickets, and
  screen-shares immediately tell you which environment you're looking at.
- Logo: 150–400×50px PNG.
- Entity images and ribbon/command bar icons: prefer [Fluent UI icons](https://github.com/microsoft/fluentui-system-icons)
  over custom artwork where a suitable one exists — use [Flicon](https://www.flicon.io/) to
  search and export them as SVG. If a custom icon is unavoidable:
    - Unified Interface entity image: black SVG (`#000000`), 0px padding.
    - Command bar icons: grey SVG (`#3A3A3A`), 0px padding.

## Decommissioning / resets

If an environment is reset, the pipeline configuration referencing it (if using
[Power Platform Pipelines](../alm/power-platform-pipelines.md)) needs to be re-pointed at the
re-created environment record — delete the stale environment reference in the pipeline
configuration rather than leaving it dangling.

