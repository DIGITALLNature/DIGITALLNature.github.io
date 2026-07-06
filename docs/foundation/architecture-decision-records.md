# Architecture Decision Records

[`DGT-FND-020`](scope-and-principles.md#dgt-fnd-020) requires deviations from this guideline to
be documented and justified — but a requirement without a format and a location tends to decay
into scattered wiki pages and ticket comments. This chapter pins both down: fundamental
decisions are recorded as **Architecture Decision Records (ADRs)**, in the project repository,
in a fixed format.

This also pays off for automation: rule checks (Solution Checker custom rules,
[`dgtp analyze`](../reference/dgtp-commands.md#analyze), pipeline linters) can reconcile a
flagged violation against the project's ADR set and suppress findings that are covered by an
accepted decision, instead of raising the same noise on every run.

## Where ADRs live

**`DGT-FND-030`**{ #dgt-fnd-030 } — Every project maintains an ADR log in its main repository:
one Markdown file per decision under `docs/decisions/`, named `NNNN-short-title.md` with an
ascending, never-reused number. ADRs are version-controlled and reviewed like code — not kept
in a wiki or a ticket system, where they detach from the state of the repository they describe.

## When an ADR is required

**`DGT-FND-040`**{ #dgt-fnd-040 } — Fundamental decisions **for or against a rule of this
guideline** require an ADR in
[MADR format](https://github.com/adr/madr/blob/develop/template/adr-template.md): Context and
Problem Statement, Decision Drivers, Considered Options, and a Decision Outcome with
Consequences and Confirmation. This operationalizes
[`DGT-FND-020`](scope-and-principles.md#dgt-fnd-020) — a deviation without an ADR is not a
documented deviation. It applies equally to rejecting a rule and to adopting one with
project-specific modifications.

Beyond guideline deviations, use the same mechanism for any architecturally significant project
decision (integration patterns, environment topology, third-party components) — the format is
not reserved for deviations.

## Referencing rule IDs

**`DGT-FND-050`**{ #dgt-fnd-050 } — A deviation ADR must reference the affected rule ID
(`DGT-<AREA>-<NUMBER>`) machine-readably — in the ADR's YAML front matter or a dedicated
"Deviates from" line — so tooling can map rule violations to accepted decisions automatically.
One ADR may cover several rule IDs. Maintain the ADR lifecycle (`proposed` → `accepted` →
`deprecated`/`superseded`, per the MADR `status` field): a superseded deviation loses its
suppression effect.

```yaml title="docs/decisions/0007-keep-single-assembly-plugin.md (front matter)"
---
status: accepted
date: 2026-05-04
deviates-from: [DGT-SRV-060]
---
```

## Review

**`DGT-FND-060`**{ #dgt-fnd-060 } — ADRs pass through the existing quality gates: new or changed
ADRs are part of pull-request review (see the
[PR review checklist](../reference/checklists.md)), and the Definition of Done item "updated
documentation" ([`DGT-TST-050`](../testing/dor-dod.md)) explicitly includes ADRs for decisions
made during the story.
