# AI evaluation & testing

Distinguish **testing AI-generated artifacts** from **evaluating runtime AI behavior**.
Generated code and configuration follow the ordinary [quality gates](../testing/index.md).
A runtime model call also needs repeatable behavioral evaluation, even if it is only a
classification or extraction step rather than an agent.

**`DGT-AI-110`**{ #dgt-ai-110 } - Every runtime AI capability must pass versioned behavioral
evaluations and deterministic integration/security tests before production. Agree numerical
acceptance thresholds, sample sizes, and repeat counts with the business owner before testing,
and retain results against the released configuration.

## Build the evaluation set

Use synthetic or approved, de-identified cases. Record inputs, user/access persona, expected
business outcomes, supporting sources where applicable, prohibited outcomes, and scenario risk.
Include typical, boundary, ambiguous, unsupported, adversarial, and failure cases. Keep a
held-out regression set separate from examples used to tune prompts.

Run representative cases repeatedly under the configuration intended for production.
Report failures and variation, not only the best result or an aggregate that hides poor
performance on high-risk cases. Passing an evaluation estimates reliability; it is not proof
that unsafe behavior is impossible.

| Layer | Evidence |
|---|---|
| Deterministic unit / integration tests | Schema and value validation, authorization, business rules, approvals, idempotency, concurrency, and persisted outcomes |
| Behavioral evaluation | Correct classification/extraction, factual support, appropriate abstention, task completion, and allowed tool selection where relevant |
| Permission / adversarial cases | Cross-user isolation, restricted or revoked sources, prompt injection, malformed model output, and attempts to bypass approvals |
| Reliability | Refusal, throttling, timeout, unavailable model/retrieval/tool, unknown write outcome, and successful handoff |

## Define a release gate

Choose metrics appropriate to the task: classification precision/recall, extraction field
accuracy, grounded-answer correctness, unsupported-claim rate, task completion, latency,
and cost per completed task. Define the denominator and scoring rubric.

For example, a **project-specific illustration**, not a universal target: a read-only
classifier could require at least 95% correct routing across 200 held-out cases run three
times each, with each repeat meeting the threshold. Also measure each business-critical class;
overall accuracy can hide failure on a rare class. The project still needs explicit latency,
cost, and unsupported-request criteria appropriate to its risk.

Require zero observed unauthorized actions or data disclosures in the test suite; any
observed violation blocks release regardless of the quality score. Human reviewers validate
high-impact results and the scoring rubric. An LLM judge may assist but is not the sole
acceptance authority; exact-string matching alone is unsuitable for open-ended answers.

## Regression and production feedback

Rerun relevant evaluations after prompt, model/configuration, tool, grounding/source,
permission, or orchestration changes and relevant platform updates. Record model/deployment
identifiers and settings where exposed; if a platform manages model versions, record what is
observable and monitor for changes rather than claiming exact reproducibility.

Review production samples only under the approved privacy and retention controls. Add
sanitized, reviewed failures to the regression set. Follow the
[agent testing strategy](../architecture/agentic-solutions.md#testing-strategy) for
conversation, tool selection, and multi-turn scenarios.
