# Building AI-enabled solutions

Start with the business outcome, not a model or agent. Record the chosen mechanism, why AI
is needed, data boundaries, alternatives, and accountable owner in an
[ADR](../foundation/architecture-decision-records.md).

## Choose the right mechanism

| Need | Primary mechanism | Example |
|---|---|---|
| Deterministic business rules or a fixed process | Workflow / Power Automate / plugin / Custom API | Validate a state transition or route a known approval |
| Probabilistic interpretation or generation | A bounded AI capability | Extract candidate fields, classify a request, or draft a summary |
| Conversation, reasoning, and tools | An agent with constrained capabilities | Clarify a request and invoke an authorized business command |

Use the simplest mechanism that meets the requirement. A single classification step does
not require an agent. Copilot Studio, approved Azure AI capabilities, Dataverse, and Custom
APIs are implementation choices. Apply the shared
[AI engineering principles](../foundation/ai-principles.md) and verify current feature
availability, region, licensing, and support status for the target environment.

## Separate the responsibilities

| Layer | Responsibility | Not its responsibility |
|---|---|---|
| Instructions | Describe the task, response format, and interaction limits | Enforce permissions or guarantee valid transactions |
| Grounding | Supply relevant, authorized evidence with provenance | Grant access or issue trusted instructions |
| Tools / actions | Expose narrow, typed operations with authenticated identities | Allow arbitrary generated code, queries, or unrestricted writes |
| Business logic | Enforce rules, authorization, approvals, and state transitions deterministically | Trust the model's claim that validation already happened |

The [deterministic business-rule boundary](../architecture/agentic-solutions.md#dgt-ai-020)
applies whether a model is reached through an agent, a flow, or application code.
An agent is not a security boundary. Keep authorization in the underlying system and
revalidate important transactions server-side at execution time.

## Grounding

Assign an owner, permitted audience, refresh process, and provenance to each source.
Enforce access during retrieval and prevent restricted content from leaking through indexes,
caches, or generated responses. Verify the selected connection's permission behavior; a
shared identity or uploaded copy does not automatically preserve source permissions.

For factual answers, require evidence that can be traced to authorized sources. Define
abstention or clarification when evidence is missing, stale, or contradictory. Source
citations help verification but do not by themselves prove that an answer is correct.

## Actions, business logic, and security

Treat user, model, retrieved, and tool content as untrusted. Validate schemas, values, and
business constraints; encode output for its rendering context; never execute generated code
or arbitrary queries. Enforce least privilege at the identity/source and server-side command
layers; never rely on prompts for authorization. Bind high-impact approval to the exact
operation.

Apply the existing [Security Model](../customizing/security-model.md) and
[Governance & DLP](../operations/governance-dlp.md); runtime data access does not authorize
sharing that data with an engineering assistant. See
[agent security guidance](../architecture/agentic-solutions.md#security-guidance) for
identity, approval, and prompt-injection controls.

## Failure behavior

Define separate outcomes for a valid result, unsupported request, insufficient evidence,
refusal, invalid output, access denial, timeout, and service failure. Choose a safe response:
clarification, deterministic processing, explicit unavailability, or human handoff.
Do not silently substitute an unapproved provider or invent a successful result.

For writes, distinguish a failed operation from an unknown outcome after a lost response.
Use idempotency and status lookup rather than blindly retrying. Bound execution time, tool
calls, and retries; unavailable authorization or approval blocks execution.

## Implementation patterns

[Agentic Solutions & Copilot Studio](../architecture/agentic-solutions.md) details
agent-to-Dataverse, Custom API and Azure Function integrations, RAG, multi-agent boundaries,
and enterprise deployment. Apply [Evaluation & testing](../testing/ai-evaluation.md) and
[ALM & operations](../alm/ai.md) to all runtime AI capabilities, including those
without a conversational interface.
