# Definition of Ready / Definition of Done

## Definition of Ready

A user story is ready for a sprint when it:

- is internally consistent and independent — not blocked by other stories;
- is prioritized and estimable;
- is small enough to fit in one sprint;
- has at least one detailed, testable acceptance criterion;
- describes the *what*, not a prescribed solution;
- is tagged appropriately for the teams/roles it touches;
- is assigned to at least one user role, if security-relevant;
- is assigned to at least one subject-matter expert;
- is attached to a parent epic/feature;
- is understood by the development team, including its acceptance criteria;
- has its important related documents linked (and a snapshot attached after sprint planning,
  so later changes to the linked document don't silently change what was actually agreed).

## Definition of Done

A user story is done when:

- all acceptance criteria are met and verified by the development team;
- unit/developer tests pass — see [Server-side Unit Testing](unit-testing-serverside.md);
- developer test cases are documented in the work-tracking system;
- this guideline's relevant chapters have been followed, or any deviation is documented per
  [Scope & Principles](../foundation/scope-and-principles.md);
- release documentation (the list of included stories) is updated;
- deployment instructions are updated, if the change needs any beyond the standard
  [build pipeline](../alm/build-pipeline.md) steps;
- system documentation (high-level/architecture) is updated;
- code and customizing are committed — see [Source Control](../alm/source-control.md);
- the change is deployed to the test environment;
- the [Solution Checker](solution-checker.md) passes;
- developer tests have been executed against the test environment.
