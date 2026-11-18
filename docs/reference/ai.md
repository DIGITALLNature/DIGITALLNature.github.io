# AI tooling & checklists

## Approved tooling

This public guideline is **not an approved-tool register**. Confirm approval for the specific
tool, feature, and data use with the responsible policy/security owner; record its reference
in internal onboarding documentation. If approval is unknown, do not use project material.

Before enabling an engineering assistant or runtime service, verify:

| Check | Evidence to record internally |
|---|---|
| Permitted use | Tool/service, feature/model, intended task, approving owner, and approval reference |
| Account and boundary | Required organization account/tenant, provider routing, region, and permitted data classification |
| Data handling | Retention, training-use settings, telemetry, and customer/contract restrictions |
| Access | Allowed repositories, indexing scope, extensions, tools, identities, and execution permissions |
| Lifecycle | Support status, licensing, review date, and process for feature/provider changes |

Product mentions in this guideline are examples to assess, not endorsements; approval of one
feature does not cover connected models, tools, or data flows.

## Worked examples

### Draft a Dataverse plugin

Give an approved assistant the business rule, relevant coding conventions, and a minimal
synthetic example. Ask for the implementation and negative test cases. The developer checks
SDK APIs and pipeline semantics, reviews the diff, and runs tests independently derived from
the acceptance criteria. No production export or connection string is needed.

### Classify a service request

A bounded model call proposes a category from an allowed list. Deterministic code validates
the output and applies routing rules; ambiguous or invalid results go to manual triage.
Evaluate held-out cases, including rare categories and failures. Do not use a model's
self-reported confidence as the only acceptance control.

### Submit an order through conversation

An agent gathers intent and requests a narrow business command. The backend enforces
permissions, current state, and approval before execution; the agent reports only the
confirmed result. See the [business-command pattern](../architecture/agentic-solutions.md#a-business-command-not-a-generic-write-tool)
for approval binding, concurrency, and retry handling.

## Engineering checklist

- [ ] Tool, feature, account, and data use are approved.
- [ ] Context is task-scoped; no secrets, personal data, production exports, or unrelated files.
- [ ] Generated dependencies, commands, APIs, and platform features are verified.
- [ ] A human understands the complete change and accepts ownership.
- [ ] Independent expected outcomes, relevant tests, security checks, and review are complete.
- [ ] Documentation and normal source-control / ALM evidence are updated.

## Runtime AI release checklist

- [ ] Mechanism choice and data boundary are documented; owners are named.
- [ ] Business rules, authorization, and transaction validation remain deterministic.
- [ ] Grounding and tools enforce least privilege with tested user/source isolation.
- [ ] Untrusted output is validated; high-impact actions have bound confirmation/approval.
- [ ] Versioned evaluations meet predefined thresholds, with no observed unauthorized actions
      or disclosures.
- [ ] Target configuration, permissions, and published execution paths have been tested.
- [ ] Failure, refusal, unknown write outcomes, handoff, and recovery have been rehearsed.
- [ ] Monitoring, privacy/retention controls, execution limits, and cost alerts are in place.

These supplement the shared [Checklists](../reference/checklists.md) and
[Definition of Ready / Done](../testing/dor-dod.md).
