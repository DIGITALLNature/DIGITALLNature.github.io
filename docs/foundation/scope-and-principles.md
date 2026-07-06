# Scope & Principles

## Purpose

This guideline collects the minimum standards DIGITALL applies to Power Platform / Dataverse
projects, covering customizing, coding, testing, and ALM/deployment. It exists so that:

- a project started by one team can be picked up, reviewed, or extended by another without
  surprises;
- generated code, tooling conventions, and pipeline behavior stay predictable across projects;
- decisions that *do* deviate from the standard are visible and intentional, not accidental.

## Scope

This guideline applies to Dataverse / Power Platform projects delivered or operated by
DIGITALL, regardless of whether the engagement is a managed package built for resale or a
customer-specific implementation. It covers:

- Customizing (schema, forms, processes, security)
- Server-side and client-side coding (plugins, Custom APIs, web resources, PCF)
- Testing
- ALM and deployment (source control, versioning, build, release)

It does **not** cover the Carrier/Workbench solution model in detail — that has its own
documentation, see [Solution Concept](../architecture/solution-concept.md) — and it does not
cover functional/business analysis or project management process.

## Precedence

**`DGT-FND-020`**{ #dgt-fnd-020 } — **If the customer has a defined standard, that standard takes precedence.** Where no customer
standard exists, this guideline applies.

We keep this intentionally: DIGITALL is usually a guest in someone else's tenant, and a
customer's existing conventions (naming, ALM tooling, security model) usually need to be
respected even where they differ from what is written here.

## Deviations

**`DGT-FND-010`**{ #dgt-fnd-010 } — Deviations from this guideline are permitted. They **must** be documented and justified in the
project's architectural documentation, so that:

- a reviewer can tell a deliberate decision apart from an oversight;
- the reasoning survives staff changes on the project;
- recurring deviations can be fed back into this guideline instead of silently multiplying
  across projects.

For fundamental decisions for or against a guideline rule, the required form is an
**Architecture Decision Record** — see
[Architecture Decision Records](architecture-decision-records.md) for the format, location,
and how to reference the [rule ID](rule-notation.md) being deviated from.

## How to read "should" vs. "must"

We currently keep this guideline lightweight rather than fully normative (no RFC 2119
MUST/SHOULD/MAY keywords throughout). Read instructions as strong defaults: if a section
doesn't call out an exception, treat it as something you follow unless you have a documented
reason not to. See [Rule Notation](rule-notation.md) for how individual rules are still made
referenceable despite that.
