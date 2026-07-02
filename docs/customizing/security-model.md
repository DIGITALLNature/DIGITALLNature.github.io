# Security Model

Dataverse security is composed from a few building blocks. Design it deliberately and document
the result in the project's architectural documentation — it's hard to retrofit and it depends
on the [table ownership](tables-columns-relationships.md) decisions already made.

## Building blocks

| Block | Use it for | DIGITALL guidance |
|---|---|---|
| **Business units** | Coarse data partitioning across org structure. | Keep the BU tree as flat as the data-access requirements allow; use the modern matrix data-access where org and data boundaries differ, rather than deep BU nesting. |
| **[Security roles](https://learn.microsoft.com/en-us/power-platform/admin/security-roles-privileges)** | What a *persona* can do. | One role per persona, built from least privilege up. Don't edit out-of-the-box roles — copy and adjust, so platform updates to the originals don't surprise you. |
| **Teams** | Sharing and group ownership. | Prefer **Microsoft Entra group teams** so membership follows identity governance. Use **access teams** for ad-hoc per-record sharing; **owner teams** when a team, not a person, owns records. |
| **Column (field) security** | A handful of genuinely sensitive columns. | Apply a field security profile to the specific columns; don't blanket-secure a whole table this way. |
| **Hierarchy security** | Manager-sees-reports access. | Enable only if the org actually needs it; it adds query cost. |

## Principles

- **`DGT-CUS-160`**{ #dgt-cus-160 } — **Least privilege.** Start from no access and grant what a persona needs, scoped to the
  smallest ownership depth (User → BU → parent-child BU → Org) that works.
- **`DGT-CUS-170`**{ #dgt-cus-170 } — **No standing System Administrator for app/service accounts.** Application users get a
  purpose-built role — see the service-principal connection guidance in
  [Env Variables & Connection References](envvars-connectionrefs.md) and
  [Governance & DLP](../operations/governance-dlp.md#who-can-do-what).
- **Mark security roles `IsCustomizable = false`** where they're not meant to be extended
  downstream (see [Naming Conventions](naming-conventions.md#further-recommendations)).
- **`DGT-CUS-180`**{ #dgt-cus-180 } — **Test with a real role, not as admin.** System Administrator hides every privilege gap;
  validate forms, views, and plugins under the actual persona role before calling a feature
  done.

## In code

Plugins run against either the **calling user** (`SecuredOrganizationService`) or **elevated**
(`ElevatedOrganizationService`) — that choice *is* a security decision. Prefer the secured
service so platform security still applies, and reach for elevated only with intent. See
[DIGITALL Assembly](../coding/serverside/digitall-assembly.md).

!!! info "Customer security standards take precedence"
    Where a customer has an existing security model or naming for roles/teams, follow it and
    document the deviation — see [Scope & Principles](../foundation/scope-and-principles.md).
