# Command Bar

The command bar (formerly "the ribbon") is the row of buttons on forms, grids, and subgrids.

## Use the modern Command Bar Designer

**`DGT-CUS-160`**{ #dgt-cus-160 } — Build commands in the **[Command Bar Designer](https://learn.microsoft.com/en-us/power-apps/maker/model-driven-apps/use-command-designer)** in make.powerapps.com, not by hand-editing
ribbon XML. The designer covers the common cases cleanly and keeps the definition inside the
solution. For the rare thing the designer doesn't expose, use **XrmToolBox** (Ribbon Workbench)
rather than editing `customizations.xml` directly — see
[Publisher & Prefix → Third-party components](../architecture/publisher-and-prefix.md#third-party-components).

## Command actions: JavaScript or Power Fx

- **JavaScript command** — calls a function in a [TypeScript ribbon script](../coding/clientside/typescript-webresources.md)
  (the `*.ribbon.ts` entry point). Use this for anything that touches Dataverse, calls an API,
  or shares logic with form scripts. Pass the form/grid context as a parameter rather than
  reaching for it globally.
- **[Power Fx](https://learn.microsoft.com/en-us/power-platform/power-fx/overview) command** — fine for short, self-contained UI logic with no shared code. Don't grow
  a Power Fx command into application logic; once it branches or calls out, move it to a
  JavaScript command backed by a typed handler.

## Visibility: enable and display rules

- **Display rules** decide whether a button shows at all; **enable rules** decide whether it's
  clickable. Keep them cheap — a rule that calls JavaScript runs on every command-bar evaluation.
- Scope each command to the right **location** (main form vs. home grid vs. subgrid) instead of
  one button everywhere guarded by rules.

## Naming and icons

Follow the [command-bar naming pattern](naming-conventions.md#command-bar-formerly-ribbon) for
buttons, commands, and rules, and the icon convention for button SVGs (see
[Themes & Icons](themes-and-icons.md)).

!!! warning "Don't break the out-of-the-box command bar"
    **`DGT-CUS-170`**{ #dgt-cus-170 } — Hiding or overriding standard buttons globally has wide blast radius. Add your own commands;
    only hide a standard one with a specific, documented reason, and scope the change as
    narrowly as possible.
