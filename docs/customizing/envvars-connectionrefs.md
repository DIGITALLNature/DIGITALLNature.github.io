# Env Variables & Connection References

These two component types are solution-aware in their **definition**, but environment-specific
in their **value** — that split is the source of most confusion around them, so it's worth
being explicit about it here and cross-referencing
[Config & Reference Data Migration](../alm/config-data-migration.md) rather than repeating the
ALM-side handling on this page.

## Connection references

- Naming: `prfx_..._con` (lowercase), e.g. `dgt_dataverse_con`, `dgt_sharepoint_con`,
  `dgt_outlook_con`.
- The **definition** (which connector, display name) travels with the solution.
- The **connection assignment** is user-specific: whoever owns the flow's connection at
  deployment time is whose API limits and permissions the flow runs under. After every
  deployment to a new or refreshed environment, re-confirm who the connection is assigned to —
  don't assume the previous environment's assignment carried over.
- **`DGT-CUS-200`**{ #dgt-cus-200 } — For unattended/production flows, assign the connection via an **application user / service
  principal** rather than a named person's account — this also gives you the full API limit
  allocation rather than a shared per-user quota.

## Environment variables

- Naming: `prfx_..._env` (lowercase), e.g. `dgt_foreign_system_url_env`,
  `dgt_organization_friendlyname_env`.
- **`DGT-CUS-210`**{ #dgt-cus-210 } — In development, create the environment variable **definition only** (no value) and deploy
  that. If you set a value while creating it in the dev environment, remove it from the
  solution before export — "Remove from this solution" works while the variable is still in
  the same environment it was created in.
- On the target environment, set the actual value either:
    - via an unmanaged "values" solution layered on top, or
    - directly during manual deployment in the modern deployment settings UI, or
    - through a deployment settings file consumed by your pipeline (see
      [Pre- & Post-Deployment Tasks](../alm/pre-post-deployment.md)).

## Why this split matters for ALM

Both of these are exactly the kind of artifact that makes "just import the solution" an
incomplete deployment story — see
[Config & Reference Data Migration](../alm/config-data-migration.md) for how this is handled
as an explicit pipeline step rather than a manual post-deployment task someone has to remember.
