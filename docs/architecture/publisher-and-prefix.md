# Publisher & Prefix

## Choosing a publisher

- For components developed and packaged **at DIGITALL** for later deployment into customer
  environments — i.e. reusable, productized work — use the **DIGITALL** publisher and its
  `dgt_` prefix.
- For **customer-specific projects**, use a customer-specific publisher and a customer-specific
  prefix.

This distinction matters beyond naming: it signals ownership to anyone looking at the
environment later, and it is also a Power Platform Pipelines best practice — using a dedicated
publisher/prefix per project keeps automated deployments unambiguous about what owns what. See
[Power Platform Pipelines](../alm/power-platform-pipelines.md).

## Picking a customer prefix

- Short (ideally 3-5 characters), lowercase, and unlikely to collide with other ISV or
  in-house prefixes already in the tenant.
- Once chosen for a project, it does not change — schema name prefixes cannot be renamed after
  components are created without recreating them.
- Document the chosen prefix in the project's architectural documentation alongside the
  publisher record details (display name, description, default language).

All naming conventions in [Customizing](../customizing/naming-conventions.md) use `prfx_` as a
placeholder for whichever prefix applies to the project.

## Microsoft's customization rules as the baseline

We follow Microsoft's customization guidance by default, and deviate only where there's a
concrete development advantage or a real risk otherwise — for example, avoiding name
collisions in generated early-bound classes. A deviation here follows the same rule as any
other: document it, with the reasoning, in the project's architectural documentation. See
[Scope & Principles](../foundation/scope-and-principles.md).

## Third-party components

Before installing a special-purpose third-party solution into the Power Platform, check
whether the same outcome is achievable with an **XrmToolBox** plugin first — it's usually
lighter-weight and doesn't add a long-term solution dependency. This applies in particular to
command bar / ribbon customization: use XrmToolBox rather than hand-editing ribbon XML.