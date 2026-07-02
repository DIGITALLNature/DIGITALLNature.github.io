# Client-side Testing

**`DGT-TST-030`**{ #dgt-tst-030 } — Unit-test the logic in [TypeScript web resources](../coding/clientside/typescript-webresources.md)
the same way you'd test any other TypeScript: with **Jest**, mocking the `Xrm` API via
[`xrm-mock`](https://github.com/camsjams/xrm-mock). The
[`webresources-template`](https://github.com/DIGITALLNature/webresources-template) ships the
tooling (Jest, `ts-jest`, `jest-environment-jsdom`, `xrm-mock`).

## What to test — and what not to

- **Test your logic:** branching in form/ribbon handlers, calculations, the decisions a handler
  makes given attribute values.
- **Don't test the platform:** that `addOnSave` registers a handler, or that `getAttribute`
  returns a value, is Microsoft's code — mock it and assert on *your* behavior.
- Factor non-trivial logic into plain functions that take inputs and return outputs; they're the
  easiest thing to test and keep the `Xrm`-bound handler thin.

## Setup

Configure Jest to transform TypeScript with `ts-jest` and run in the `jsdom` environment:

```javascript title="jest.config.js"
export default {
  preset: "ts-jest/presets/default-esm",
  testEnvironment: "jsdom",
  testMatch: ["**/*.test.ts"],
};
```

Run with the template's scripts — `pnpm run test` (once) or `pnpm run test-watch`.

## Example: a form `OnLoad` handler

```typescript title="account.form.test.ts"
import { XrmMockGenerator } from "xrm-mock";
import { Account } from "./account.form";

describe("Account.onLoad", () => {
  beforeEach(() => XrmMockGenerator.initialise());

  it("requires the name when the account is active", () => {
    const name = XrmMockGenerator.Attribute.createString("name", "");
    const context = XrmMockGenerator.getEventContext();

    Account.onLoad(context);

    expect(name.getRequiredLevel()).toBe("required");
  });
});
```

`XrmMockGenerator.initialise()` builds a fresh fake `Xrm`/form context per test, so cases stay
isolated — arrange the attributes and controls the handler reads, invoke the exported handler,
then assert on the resulting form state.

## Where this runs

Client-side tests run in the [Build Pipeline](../alm/build-pipeline.md) alongside the
[server-side unit tests](unit-testing-serverside.md) — both gate the build before packaging.
Treating a green client-side test run as part of the
[Definition of Done](dor-dod.md) keeps form-script regressions out of a release the same way
plugin tests do on the server side.
