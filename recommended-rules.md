# Recommended Additional Rules & Project ToDos (Draft)

Working document — candidate rules and recurring project ToDos identified by comparing the
current guideline (66 rules, `DGT-FND-020` … `DGT-OPS-010`) against Microsoft Learn best
practices (Power Platform Well-Architected, Dataverse developer guidance, ALM guidance,
Success by Design). Items already covered by an existing rule were excluded; where an item
sharpens an existing rule, that rule is referenced.


> **Suggested top 5 by effort/impact:** 8.1 (unmanaged-layer check), 4.1 (429/Retry-After),
> 2.1 (column security instead of hiding fields), all of section 6 (cloud flows chapter),
> 1.1 (backup/restore rules).

---

## 1. Architecture (`ARC`)

- [m] **1.1 Backup & restore strategy is a mandatory project decision.**
  Take a manual backup before every major deployment. Never restore directly onto production
  (switch prod → sandbox, restore, switch back — note: sandboxes only retain 7 days of backups
  vs. up to 28 days for Managed production environments). After a restore, solution flows are
  turned off, connection references need new connections, and canvas app IDs change — this
  belongs in the project runbook.
  Source: <https://learn.microsoft.com/en-us/power-platform/admin/backup-restore-environments>

- [m] **1.2 Define uptime and recovery targets (RTO/RPO) per workload before finalizing the
  environment strategy.** Complements `DGT-ARC-080`.
  Source: <https://learn.microsoft.com/en-us/power-platform/well-architected/reliability/>

- [r] **1.3 Use elastic tables (with a TTL column) for high-volume data such as logs and
  telemetry — never standard tables.** Constraints to document alongside the rule: no
  multi-record transactions, plugin exceptions do not roll back writes (validate in
  PreValidation only), `partitionid` is immutable and must be chosen deliberately up front.
  Source: <https://learn.microsoft.com/en-us/power-apps/developer/data-platform/elastic-tables>

- [m] **1.4 Treat licensing/entitlement as an early architecture checkpoint.**
  Entitlement limits (API requests per day per license) are separate from service protection
  limits; S2S application users don't consume a user license; Managed Environments are not
  included in the Developer plan. Licensing is currently not covered anywhere in the guideline.
  Source: <https://learn.microsoft.com/en-us/power-platform/admin/api-request-limits-allocations>

- [m] **1.5 Run the Power Platform Well-Architected assessment at project start** and document
  the results as backlog items. (Recommended ritual rather than a hard rule.)
  Source: <https://learn.microsoft.com/en-us/power-platform/well-architected/>

## 2. Customizing (`CUS`)

- [r] **2.1 Never implement security through UI visibility.** A field hidden on a form remains
  readable via the API — use column-level security for PII/sensitive columns. Biggest single
  gap in the security chapter.
  Source: <https://learn.microsoft.com/en-us/power-platform/admin/field-level-security>

- [m] **2.2 Configure Dataverse auditing deliberately.** Enable/disable per table, define a
  retention policy (audit logs are a log-capacity driver), and know that a restore does not
  bring audit logs back. Auditing is currently not covered at all.
  Source: <https://learn.microsoft.com/en-us/power-platform/admin/manage-dataverse-auditing>

- [r] **2.3 Security roles are solution components and get deployed** — never rebuilt manually
  in test/prod. Natural companion to `DGT-CUS-220`–`180`.
  Source: <https://learn.microsoft.com/en-us/power-platform/admin/wp-security-cds>

## 3. Server-side Coding (`SRV`)

- [r] **3.1 No `ExecuteMultipleRequest`/`ExecuteTransactionRequest` inside plugins.** It only
  extends the transaction; batching gains nothing in the sandbox. Explicit Microsoft best
  practice, missing from patterns.md.
  Source: <https://learn.microsoft.com/en-us/power-apps/developer/data-platform/best-practices/business-logic/>

- [r] **3.2 No custom threading / `Parallel.*` in plugins** — not supported.
  Source: same best-practice catalog as 3.1.

- [r] **3.3 Synchronous steps on `Retrieve`/`RetrieveMultiple` only in justified exceptions** —
  they slow down every view and read.
  Source: same best-practice catalog as 3.1.

- [r] **3.4 Don't (ab)use `context.Depth` as loop-control logic** — it breaks legitimate
  cascades. Prevent loops via filtering attributes and targeted registration instead; the
  platform's infinite-loop protection is a safety net, not a design element. Complements
  `DGT-SRV-200`.
  Source: <https://learn.microsoft.com/en-us/power-apps/developer/data-platform/understand-the-data-context>

- [r] **3.5 Query only the columns you need (explicit `ColumnSet`, never `AllColumns`)** —
  sandbox worker memory limit and query throttling.
  Sources: <https://learn.microsoft.com/en-us/troubleshoot/power-platform/dataverse/plug-in-execution/dataverse-plug-ins-errors>,
  <https://learn.microsoft.com/en-us/power-apps/developer/data-platform/query-throttling>

- [r] **3.6 External calls from plugins: set an explicit timeout and `KeepAlive=false`.**
  Sharpens `DGT-INT-010`.
  Source: <https://learn.microsoft.com/en-us/power-apps/developer/data-platform/best-practices/business-logic/set-timeout-for-external-calls-from-plug-ins>

- [r] **3.7 A plugin assembly belongs to exactly one solution** — avoids duplicate
  registration and layer conflicts.
  Source: same best-practice catalog as 3.1.

## 4. Cloud & Integration (no area code yet — candidate: `INT`)

- [r] **4.1 Every integration must handle HTTP 429 / `Retry-After`.** Service protection
  limits per user and web server in a 5-minute sliding window: 6,000 requests, 20 minutes
  combined execution time, ~52 concurrent requests. Use `ServiceClient`, which retries
  automatically. The most important missing integration rule.
  Source: <https://learn.microsoft.com/en-us/power-apps/developer/data-platform/api-limits>

- [m] **4.2 Large batches are not a remedy for throttling.** Prefer small batches (~10) with
  controlled parallelism, use the bulk APIs (`CreateMultiple`/`UpdateMultiple`), and let the
  server dictate throughput via `Retry-After`.
  Sources: <https://learn.microsoft.com/en-us/power-apps/developer/data-platform/optimize-performance-create-update>,
  <https://learn.microsoft.com/en-us/power-apps/developer/data-platform/send-parallel-requests>

- [r] **4.3 Never use the Dataverse Search API for bulk queries** (~1 request/second/user).
  Source: <https://learn.microsoft.com/en-us/power-apps/developer/data-platform/api-limits> (FAQ)

## 5. Client-side Coding (`CLI`)

- [r] **5.1 Deprecation deny-list as a rule:** no `Xrm.Page`, no `parent.Xrm` in HTML web
  resources, no references to platform scripts (jQuery has been removed from the platform),
  no OData v2 — only `formContext` and the Web API.
  Source: <https://learn.microsoft.com/en-us/power-platform/important-changes-coming>


## 6. Power Automate / Cloud Flows (new chapter — biggest structural gap)

Flows currently appear only as a decision option and via connection references. Rule candidates:

- [r] **6.1 Always create flows solution-aware** — otherwise no ALM, no pipelines, no
  connection references.

- [r] **6.2 No production flow without error handling:** try/catch pattern via scopes and
  "Configure run after", plus centralized failure notification.
  Source: <https://learn.microsoft.com/en-us/power-automate/guidance/coding-guidelines/error-handling>

- [r] **6.3 Use trigger conditions instead of a condition as the first action;** set
  column/row filters on Dataverse triggers — saves runs and prevents update → trigger → update
  loops.
  Source: <https://learn.microsoft.com/en-us/power-automate/dataverse/create-update-delete-trigger>

- [m] **6.4 Configure the retry policy per action deliberately** — disable it for
  non-idempotent actions and compensate instead.
  Source: <https://learn.microsoft.com/en-us/power-automate/guidance/coding-guidelines/error-handling>

- [r] **6.5 Document the concurrency pitfalls:** variables inside "Apply to each" are not
  thread-safe with concurrency enabled; raise the pagination default (100 items) explicitly.
  Source: <https://learn.microsoft.com/en-us/power-automate/guidance/coding-guidelines/understand-limits>

- [m] **6.6 Flows are not a high-volume ETL tool** — respect Power Automate's own limits
  (actions per day, payload sizes, run duration).
  Source: <https://learn.microsoft.com/en-us/power-automate/limits-and-config>

## 7. Testing & Quality (`TST`)

- [m] **7.1 Performance test under realistic production load as a go-live gate** (form load
  times, search, integrations as non-functional acceptance criteria). There is currently no
  load/performance testing strategy at all.
  Source: <https://learn.microsoft.com/en-us/dynamics365/guidance/implementation-guide/prepare-to-go-live>

- [r] **7.2 Release-wave regression test:** activate Early Access of each wave (2×/year) in a
  sandbox first and run a regression before the wave reaches production.
  Source: <https://learn.microsoft.com/en-us/dynamics365/get-started/release-schedule>

## 8. ALM & Deployment (`ALM`)

- [m] **8.1 Recurring unmanaged-layer check in test/prod** ("Solution layers" view; remove
  active custom layers). Unmanaged layers override every deployment — the most common cause of
  "the change didn't arrive". `dgtp analyze noactivelayer` / `activelayer` is the natural
  anchor for this.
  Source: <https://learn.microsoft.com/en-us/power-platform/alm/solution-layers-alm>

- [r] **8.2 Never put the same unmanaged component into multiple solutions;** export tables
  segmented only ("include table metadata only", never "Add all assets"). Complements
  `DGT-ARC-010`.
  Source: <https://learn.microsoft.com/en-us/power-platform/alm/organize-solutions>

- [r] **8.3 Use Upgrade (not Update) when components were removed** — otherwise orphaned
  managed components remain. Document the Upgrade / Stage-for-Upgrade / Update differences.
  Source: <https://learn.microsoft.com/en-us/power-platform/alm/solution-import-export-alm>

- [r] **8.4 Use a deployment settings file for connection references and environment-variable
  values** as the standard mechanism in automated deployments. Operationalizes
  `DGT-CUS-200`/`200` at pipeline level.
  Source: <https://learn.microsoft.com/en-us/power-platform/alm/conn-ref-env-variables-build-tools>

- [m] **8.5 Settle flow ownership after service-principal deployments:** after an import via
  service principal, flows are owned by the SP — its connections must exist, and run-as /
  ownership may need to be re-assigned.
  Source: <https://learn.microsoft.com/en-us/power-platform/alm/delegated-deployments-setup>

## 9. Operations (`OPS`)

- [m] **9.1 Harden the default environment:** rename it (e.g. "Personal Productivity"),
  enable environment routing, restrictive DLP, no business solutions there.
  Source: <https://learn.microsoft.com/en-us/power-platform/guidance/adoption/manage-default-environment>

- [m] **9.2 Set the DLP default group for new connectors to Blocked/Non-Business** — new
  connectors otherwise land in the default group automatically. The most important sharpening
  of `DGT-OPS-030`.
  Source: <https://learn.microsoft.com/en-us/power-platform/admin/dlp-connector-classification>

- [m] **9.3 Capacity monitoring as an operations baseline:** watch database/file/log capacity
  separately; exceeding storage blocks environment copy/restore/create.
  Source: <https://learn.microsoft.com/en-us/power-platform/admin/capacity-storage>

- [m] **9.4 Deprecation radar as a recurring ritual:** review "Important changes coming"
  against the project regularly (currently relevant: BYOK → CMK by 2026-01-06, legacy grid
  deprecated March 2026, legacy Dataverse connectors).
  Source: <https://learn.microsoft.com/en-us/power-platform/important-changes-coming>

- [m] **9.5 Don't leave the plugin trace log permanently enabled in production** (log
  capacity) — telemetry belongs in Application Insights. Complements `DGT-OPS-010`.
  Source: <https://learn.microsoft.com/en-us/power-platform/admin/overview-integration-application-insights>

- [m] **9.6 Tenant isolation / cross-tenant restrictions** as a baseline recommendation for
  compliance-sensitive customers.
  Source: <https://learn.microsoft.com/en-us/power-platform/admin/cross-tenant-restrictions>

## 10. Reference / Checklists

- [m] **10.1 Add a go-live/cutover checklist** (checklists.md has pre-/post-deployment but no
  go-live): data-migration dry runs with throughput measurement (factor API limits into the
  cutover window!), version parity test ↔ prod, documented go/no-go decision, manual backup
  plus a rollback decision point, hypercare plan with escalation path, admin-mode handling.
  Source: <https://learn.microsoft.com/en-us/dynamics365/guidance/implementation-guide/prepare-go-live-checklist>

## 11. Foundation (`FND`) — Architecture Decision Records

Not covered anywhere today: `DGT-FND-020` requires deviations to be "documented and justified
in the project's architectural documentation", but prescribes no format, no location, and no
repository. Pinning this down to ADRs makes deviations reviewable — and machine-checkable:
automated rule checks (Solution Checker custom rules, `dgtp analyze`, pipeline linters) can
reconcile a flagged violation against the ADR set and suppress findings that are covered by an
accepted decision instead of raising noise.

- [r] **11.1 Every project maintains an ADR log in its main repository.**
  One Markdown file per decision under `docs/decisions/` (MADR convention), named
  `NNNN-short-title.md` with an ascending, never-reused number — version-controlled and
  reviewed like code, not buried in a wiki or ticket system.
  Source: <https://adr.github.io/madr/>

- [r] **11.2 Fundamental decisions for or against a guideline rule require an ADR** in
  [MADR format](https://github.com/adr/madr/blob/develop/template/adr-template.md)
  (Context and Problem Statement, Decision Drivers, Considered Options, Decision Outcome with
  Consequences and Confirmation). This operationalizes `DGT-FND-020`: a deviation without an
  ADR is not a documented deviation. Applies to adopting a rule with project-specific
  modifications as well as to rejecting one.

- [r] **11.3 A deviation ADR must reference the affected rule ID (`DGT-<AREA>-<NNN>`)
  machine-readably** — e.g. in the ADR's front matter or a dedicated "Deviates from" line —
  so tooling can map rule violations to accepted decisions automatically. One ADR may cover
  several rule IDs; the ADR lifecycle (proposed → accepted → deprecated/superseded, per MADR
  `status`) must be maintained so that superseded deviations lose their suppression effect.

- [r] **11.4 ADR review belongs to the existing quality gates:** new/changed ADRs are part of
  pull-request review (extend the PR checklist in checklists.md), and the DoD item "updated
  documentation" (`DGT-TST-050`) explicitly includes ADRs for decisions made during the story.
