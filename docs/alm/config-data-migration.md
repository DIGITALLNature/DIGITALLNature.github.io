# Config & Reference Data Migration

Not everything a project needs in a target environment travels inside a solution. This chapter
covers the data and configuration that is **not solution-aware**, or only partially so, and
therefore needs an explicit migration step rather than relying on solution import alone.

## What is and isn't solution-aware

| Artifact | Solution-aware? | How it moves between environments |
|---|---|---|
| Tables, columns, forms, views, processes | Yes | Solution import. |
| Environment variable **definitions** | Yes | Solution import. |
| Environment variable **values** | **No** | Set per environment; see [Env Variables & Connection References](../customizing/envvars-connectionrefs.md). |
| Connection reference **definitions** | Yes | Solution import. |
| Connection reference **connections** | **No** (user-specific) | Re-assigned per environment, per user. |
| Security roles, teams | Yes | Solution import. |
| Team templates, queues, calendars, SLAs, routing rules, bulk delete jobs, document templates, Outlook templates, user roles | **No** | `dgtp export` / `dgtp import`. |
| Business/reference data (option set seed data, configuration tables) | **No** | `dgtp export` / `dgtp import`, or a dedicated data migration tool for larger volumes. |

## `dgtp export` / `dgtp import`

**`DGT-ALM-120`**{ #dgt-alm-120 } — For the non-solution-aware Dataverse configuration objects listed above, use the matching
`dgtp export`/`dgtp import` command pair rather than recreating them by hand in each
environment:

```shell
dgtp export teamtemplates --filedir ./config/teamtemplates
dgtp export queues --filedir ./config/queues
dgtp export calendars --filedir ./config/calendars
dgtp export slaconfigs --filedir ./config/sla
dgtp export routingruleconfigs --filedir ./config/routing
dgtp export documenttemplates --filedir ./config/doctemplates
dgtp export outlooktemplates --filedir ./config/outlooktemplates
dgtp export bulkdeletes --filedir ./config/bulkdeletes
dgtp export userroles --filedir ./config/userroles
```

```shell
dgtp import teamtemplates --filedir ./config/teamtemplates
# ...same pattern for each importer, plus:
dgtp import secureconfigs --filedir ./config/secureconfigs
```

Treat the exported files the same way as the unpacked solution: commit them to
[source control](source-control.md) under `config/`, review changes in pull requests, and run
the corresponding `import` as a deployment step for environments that need that configuration
— not necessarily every environment needs every config object, so scope the import step per
target rather than running the full set blindly everywhere.

!!! note "`secureconfigs` is import-only"
    There is no `dgtp export secureconfigs` — secure configuration values are written, not
    read back, by design. Maintain the source values outside Dataverse (a secret store or an
    encrypted file outside source control) and import them explicitly per environment.

## Larger reference/business data sets

For bulk reference data beyond what the commands above cover (e.g. seeding a large lookup
table, migrating legacy records), use a dedicated data migration tool
(Configuration Migration Tool, or a custom import) rather than scripting it through `dgtp`,
which is not designed for high-volume record migration. Document the chosen tool and its
mapping files alongside the project's other ALM scripts.

## Where this fits in the pipeline

Config/reference data migration is typically a **post-deployment** task, run after the
solution import has completed against the target environment — see
[Pre- & Post-Deployment Tasks](pre-post-deployment.md). On a project using
[Power Platform Pipelines](power-platform-pipelines.md), this is a natural candidate for a
gated extension flow rather than a manual step a release manager has to remember.
