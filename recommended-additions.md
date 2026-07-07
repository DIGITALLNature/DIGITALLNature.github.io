# Recommended Additions (Draft)

Working document — chapters, sections, and rules the guideline does **not** cover yet, written
out in full so each block can be reviewed and, if accepted, pasted into the named target page
(assigning the next free rule ID per area where a rule candidate is marked). Topics that were
already reviewed and deliberately dropped from the earlier rules draft (canvas apps,
Copilot Studio / AI Builder) are intentionally not re-proposed here.

Ordering: highest expected value first within each group. Delete what we don't want; the
remaining blocks are ready to merge.

---

## 1. Foundation / Architecture

### 1.1 Define the "project documentation set" (new section, its own page)

The guideline references *"the project's architectural documentation"* in many places
(deviations, security model, monitoring depth, repo layout choice) without ever defining what
that documentation minimally is. Proposed content, including a rule candidate (next free `FND`
or `ARC` ID):

> ## The project documentation set
>
> Several rules in this guideline require decisions to be "documented in the project's
> architectural documentation". Concretely, every project maintains at least:
>
> | Artifact | Contains | Lives in |
> |---|---|---|
> | **Solution design document** | Solution layering, publisher/prefix, environment strategy, deployment approach — the decisions from this chapter, with their reasoning. | Project repository (`docs/`) |
> | **ADR log** | Architecture Decision Records, including guideline deviations. | `docs/decisions/` (see [Architecture Decision Records](../foundation/architecture-decision-records.md)) |
> | **Security & role matrix** | Personas ↔ security roles ↔ business units/teams; column-security profiles and who holds them. | Project repository (`docs/`) |
> | **Integration overview** | Every inbound/outbound integration: direction, identity (application user), protocol, throughput expectations, owning system. | Project repository (`docs/`) |
> | **Environment overview** | Environment list with type, Managed Environment status, backup retention, URL, and purpose. | Project repository (`docs/`) |
> | **Runbook** | Restore procedure, deployment rollback point, hypercare contacts, monitoring alert routing. | Project repository (`docs/`) |
>
> **Rule candidate** — *Every project maintains the documentation set above in its repository;
> "documented in the architectural documentation" in this guideline always refers to one of
> these artifacts.* Keeping them in the repository (not a wiki) means they version with the
> code they describe and pass through pull-request review like everything else.

### 1.2 Security hardening decision checklist (new section, `customizing/security-model.md`)

For compliance-sensitive customers, the platform offers hardening features beyond roles and
DLP. The guideline never names them, so they're never asked about. Proposed content
(recommendation, no rule ID):

> ## Hardening options for compliance-sensitive customers
>
> Raise these platform capabilities explicitly during security design — each is a
> customer-level decision with cost/operations implications, and retrofitting them is harder
> than enabling them up front:
>
> - **Customer-managed key (CMK)** — customer's own Key Vault key encrypts the environment.
>   Constraint worth knowing before committing: a CMK environment can only be restored to an
>   environment using the same key, which shapes the backup/copy strategy. See
>   <https://learn.microsoft.com/en-us/power-platform/admin/customer-managed-key>.
> - **IP firewall** and **IP-based cookie binding** (Managed Environments) — restrict where
>   sessions may come from. See
>   <https://learn.microsoft.com/en-us/power-platform/admin/ip-firewall>.
> - **Customer Lockbox** — explicit approval workflow before Microsoft support can access
>   customer data. See
>   <https://learn.microsoft.com/en-us/power-platform/admin/about-lockbox>.
> - **VNet support for plugins/connectors** — outbound calls from plugins run inside the
>   customer's virtual network instead of over public endpoints. See
>   <https://learn.microsoft.com/en-us/power-platform/admin/vnet-support-overview>.
>
> Record the outcome (enabled / consciously not enabled, and why) in the security section of
> the project documentation set.

---

## 2. Customizing

### 2.1 Localization & multi-language (new page, `customizing/localization.md`)

Multi-language projects are common for DIGITALL and currently unaddressed. Proposed page,
with two rule candidates (next free `CUS` IDs):

> # Localization
>
> Dataverse is multi-language at the platform level; whether a *project* is depends on
> decisions that are cheap at the start and expensive later.
>
> ## Base language is forever
>
> **Rule candidate** — *Decide the environment's base language deliberately at provisioning —
> it cannot be changed afterwards.* The base language is what schema names, default labels,
> and the [early-bound model](../coding/serverside/early-binding.md) (`UseBaseLanguage`)
> resolve against. For multi-country projects, English base language with enabled additional
> languages is the usual safe choice.
>
> ## Labels, not copies
>
> - Enable the required languages in every environment of the pipeline, not just production —
>   otherwise translations can't be tested.
> - Maintain display names, form labels, view names, and choice labels through the
>   **translation export/import** cycle (`Export translations` / `Import translations` on the
>   solution) and keep the exported translation file in source control with the unpacked
>   solution, so label changes are reviewable diffs.
> - **Rule candidate** — *No hard-coded user-facing strings in code: plugin messages and web
>   resource texts come from a localizable source* (RESX resources shipped in the plugin
>   package, localized web resources per language, or a label table) *so that adding a
>   language never requires a code change.*
> - `InvalidPluginExecutionException` messages are user-facing (see
>   [Patterns & Pitfalls](../coding/serverside/patterns.md)) — they are part of the
>   localizable surface, not developer diagnostics.

### 2.2 Duplicate detection & data quality (new section, `customizing/tables-columns-relationships.md`)

Proposed content (recommendation, no rule ID):

> ## Duplicate detection
>
> Decide per core table whether duplicate detection rules apply (typically account/contact),
> and remember their limits: they warn interactively but do not block API writes unless the
> caller opts in (`SuppressDuplicateDetection`), and they don't run for all operation types.
> For integration scenarios, enforce identity through
> [alternate keys](tables-columns-relationships.md) and upserts instead — duplicate detection
> is a user-experience aid, not a data-integrity mechanism.

---

## 3. ALM & Deployment

### 3.1 Hotfix & rollback process (new section, `alm/versioning.md`)

The pieces exist (Revision segment, patches, manual backups) but no page connects them into a
process. Proposed content, with one rule candidate (next free `ALM` ID):

> ## Hotfix & rollback
>
> **Hotfix path.** A production defect that can't wait for the next release ships as a hotfix
> from a branch off the released tag: bump the solution **Revision** segment
> (`dgtp maintenance solution-version <solution> --revision`), ship through the same pipeline
> and gates as a regular release (Solution Checker and tests included — a hotfix is not an
> excuse to skip the gates), and merge the fix back into `develop` immediately so the next
> release doesn't regress it. Alternatively, use a solution **patch** layered on the managed
> solution — then reconcile it into the next full release per
> [Solution Architecture](../architecture/solution-architecture.md).
>
> **Rollback path.** Decide *before* deploying which of these applies if the release goes
> wrong, and note it in the deployment plan:
>
> 1. **Fix forward** (default) — most schema changes can't be un-imported; a fast hotfix is
>    usually cheaper and safer than any rollback.
> 2. **Re-import the previous artifact** — possible while the change is additive-only; the
>    previous managed solution version can be imported as a downgrade only in limited cases,
>    so treat this as an option to verify, not to assume.
> 3. **Restore the pre-deployment backup** — the last resort; loses all data written since
>    the backup and triggers the full post-restore runbook (flows off, connections,
>    canvas app IDs — see [Environment Strategy](../architecture/environment-strategy.md)).
>
> **Rule candidate** — *Every production deployment names its rollback option (fix forward /
> re-import / restore) and the decision point at which it is invoked, before the deployment
> starts.*

### 3.2 Data migration methodology (new section, `alm/config-data-migration.md` — extend)

The existing page covers *config/reference* data and delegates bulk migration to "a dedicated
data migration tool" without any methodology. Proposed addition (recommendations, no rule ID):

> ## Bulk / legacy data migration
>
> Tooling is project-specific (SSIS/KingswaySoft, Azure Data Factory, custom loaders), but the
> method is not:
>
> - **Mapping document first** — source field → target column, transformation, and owner for
>   every migrated attribute; reviewed by the business, versioned in the repository.
> - **Dry runs with throughput measurement** — measure records/hour against a production-like
>   environment, with [service protection limits](../coding/cloud-integration.md#dataverse-service-protection-limits)
>   and [entitlement limits](https://learn.microsoft.com/en-us/power-platform/admin/api-request-limits-allocations)
>   factored in, before committing to a cutover window.
> - **Use the platform's bulk facilities**: `CreateMultiple`/`UpdateMultiple`, upsert against
>   [alternate keys](../customizing/tables-columns-relationships.md), and
>   `overriddencreatedon` for historical timestamps; run under a dedicated application user so
>   throttling and auditing attribute the load correctly.
> - **Deactivate what must not fire**: audit the plugin steps and flows that react to the
>   migrated tables; disable (or filter via a "migration mode" environment variable) the ones
>   that would fire per record, and document what was disabled so it's re-enabled at cutover
>   end.
> - **Validation queries** — record counts and spot checks per table, agreed with the
>   business, executed after every run (not only the final one).

---

## 4. Operations

### 4.1 Environment refresh & data anonymization (new section, `operations/index.md`)

Sandbox refreshes from production are routine and currently unmentioned; with EU customers the
anonymization question is not optional. Proposed content, with one rule candidate (next free
`OPS` ID):

> ## Refreshing test environments from production
>
> Copying production into a sandbox gives realistic test data at the price of exposing real
> personal data to a wider audience.
>
> **Rule candidate** — *After every copy of production data into a non-production environment,
> personal data is anonymized or masked before the environment is handed to the team, unless
> the customer's data protection assessment explicitly allows production data in that
> environment.*
>
> Post-copy checklist (pair with the platform's copy behavior):
>
> - Environment comes up in **administration mode** — keep it there until anonymization and
>   reconfiguration are done, then disable.
> - Re-point [connection references](../customizing/envvars-connectionrefs.md) and
>   [environment variable values](../customizing/envvars-connectionrefs.md) at test targets —
>   they still point at production systems after the copy.
> - Verify outbound integrations are disabled or redirected (a copied environment happily
>   keeps calling the production endpoints it knows).
> - Re-enable only the flows the test environment needs.

### 4.2 Incident response runbook skeleton (extend `operations/monitoring.md`)

Operations is deliberately thin, but a minimal incident skeleton costs little and is missing.
Proposed content (recommendation, no rule ID):

> ## When an alert fires
>
> The monitoring baseline is only useful if someone knows what to do with a hit. Projects
> document, as part of the runbook (see the project documentation set):
>
> - **Who is first responder** per alert category (plugin failures, flow failures, capacity,
>   deployment), and the escalation path to the customer.
> - **First diagnostic steps** per category — e.g. plugin failure: correlate the Application
>   Insights operation ID with the plugin trace log; flow failure: open the run history of the
>   owning service account.
> - **What may be done directly in production** — aligned with the production-access rules in
>   [Governance & DLP](governance-dlp.md): diagnosis yes, unmanaged hotfixing no.

---

## 6. Coding

### 6.1 Dependency & supply-chain hygiene (new section, `coding/serverside/project-setup.md` + note in `alm/build-pipeline.md`)

The sample `.csproj` uses `Version="*"` wildcards with `RestorePackagesWithLockFile` — the
policy behind that combination is nowhere stated, and npm-side hygiene is unstated too.
Proposed content, with one rule candidate (next free `SRV` or `ALM` ID):

> ## Dependencies
>
> - **Rule candidate** — *Lock files are committed and CI restores in locked mode*
>   (`packages.lock.json` via `RestorePackagesWithLockFile` with
>   `dotnet restore --locked-mode`; `pnpm-lock.yaml` with `pnpm install --frozen-lockfile`).
>   Wildcard `Version="*"` references are acceptable *only* because the lock file pins the
>   actual version — without locked-mode restore in CI they make builds non-reproducible.
> - Run a dependency vulnerability audit as a pipeline step
>   (`dotnet list package --vulnerable`, `pnpm audit`) and treat new criticals like failing
>   tests.
> - Keep dependency updates flowing through an automated PR tool (Renovate/Dependabot) rather
>   than ad-hoc bumps during feature work.

---

## Explicitly not proposed (previously reviewed and dropped)

- Canvas apps chapter / rules
- Copilot Studio & AI Builder governance section

If either becomes project-relevant later, the earlier research notes can be revived from the
Git history of `recommended-rules.md` (deleted after incorporation; see commit history).
