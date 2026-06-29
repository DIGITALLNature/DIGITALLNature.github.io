# Governance & Contribution

## Ownership

This guideline is owned collectively by the architects' circle. There is no single approver;
changes are reviewed the same way as any other technical decision the circle makes.

## How to propose a change

1. **Small fixes** (typos, broken links, outdated version numbers) — open a pull request
   directly against the `main` branch of the documentation repository.
2. **Content changes** (a new convention, a changed recommendation, a new chapter) — open a
   GitHub discussion or raise it in the architects' circle first, then follow up with a pull
   request once there's rough agreement. This avoids rewriting a chapter twice.
3. **Recurring deviations** — if you find yourself documenting the same deviation across
   multiple projects, that's a signal the guideline should change rather than the projects.
   Raise it.

## Versioning of this guideline

The guideline itself is versioned via the documentation repository's Git history; there is no
separate release process. If a change is significant enough to need a heads-up (e.g. a target
framework bump, a tooling migration), call it out at the top of the affected chapter with a
short dated note rather than maintaining a separate changelog page.

## Style conventions for contributors

- Write chapters in English — this is a public repository.
- Prefer short, declarative sentences over long compound ones.
- Use [admonitions](https://squidfunk.github.io/mkdocs-material/reference/admonitions/)
  (`!!! tip`, `!!! warning`, `!!! note`) for callouts instead of bold text walls.
- Code samples should be complete enough to copy-paste and run, not fragments that imply
  missing context.
- Link sideways and downwards (to other chapters, to reference pages) rather than duplicating
  content.
