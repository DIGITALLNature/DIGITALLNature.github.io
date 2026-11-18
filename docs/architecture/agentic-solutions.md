# Agentic Solutions & Copilot Studio

For mechanism selection and shared architecture boundaries, see
[AI-enabled Solutions](ai-enabled-solutions.md).

Use an agent when language understanding, conversation, or grounded knowledge retrieval adds
value. Keep authorization, business rules, and transaction execution deterministic. An agent
is an interaction and orchestration layer, not a replacement for the Dataverse application.

Record the agent's purpose, business owner, users, allowed tools, knowledge sources, data
classification, and autonomy limits in the project's
[architectural documentation](../foundation/architecture-decision-records.md).

## When to use an agent vs. traditional automation

The table identifies the primary implementation, not every product that can participate.
**Preferred** means a natural fit; **Supporting** means an integration or additional control
is needed; **Not primary** means choose another layer for that responsibility.

| Requirement | Power Automate | Plugin / Custom API | Copilot Studio agent |
|---|---|---|---|
| Deterministic business process | Preferred for workflow orchestration | Preferred for synchronous rules and transactions | Not primary; invoke a deterministic tool |
| Conversational experience | Supporting as an agent tool | Supporting as a backend | Preferred |
| Knowledge retrieval and synthesis | Supporting for retrieval pipelines | Supporting for structured queries | Preferred with authorized grounding sources |
| Human-in-the-loop decisions | Preferred for durable approvals | Supporting for validation and recording decisions | Preferred for clarification; delegate durable approval |
| Mission-critical transaction processing | Supporting for orchestration, not a cross-step transaction | Preferred within Dataverse transaction boundaries | Not primary; request validated execution |

A fixed trigger, known inputs, and a predictable sequence usually need a flow or Custom API,
not an LLM. A hybrid is often the right answer: conversation gathers intent, a flow coordinates
approval, and a Custom API enforces the final state transition. See
[Power Automate Flows](../coding/flows.md) and
[Custom APIs](../coding/serverside/custom-api.md).

## Agent architecture patterns

| Pattern | Use it for | Boundary to preserve |
|---|---|---|
| Agent -> Dataverse | Narrow, role-scoped queries and low-risk updates through a supported connector/tool | Limit tables, columns, and operations. Dataverse authorization and server-side validation still apply. |
| Agent -> Custom API -> Dataverse | Business commands, complex validation, and controlled state transitions | Expose a typed operation through a connector or agent flow; validate and authorize on the server. |
| Agent -> Azure Function -> external system | Integration requiring custom code, protocol translation, or external credentials | Authenticate the endpoint, validate inputs, and enforce external-system permissions in the integration layer. |
| Multi-agent orchestration | Separate capabilities with distinct owners, tools, or knowledge boundaries | Define delegation contracts, identities, timeouts, and a maximum number of handoffs. Delegation never grants additional permission. |
| Retrieval-augmented generation (RAG) with SharePoint / Dataverse | Answers grounded in approved documents or business records | Enforce source permissions, freshness, and provenance; do not treat retrieved text as trusted instructions. |

Start with one agent and a small tool surface. Add another agent only when the separation has
a clear benefit over a topic, tool, or flow. Verify that the chosen orchestration feature is
supported in the target tenant, region, channel, and release status before depending on it.

For RAG, assign an owner and review cadence to each source. Verify how the selected knowledge
connection applies end-user permissions; uploaded files or a shared identity must not be
assumed to preserve the original document's access controls. Test two users with different
permissions, including column-secured Dataverse data and revoked access. Do not copy restricted
material into a broadly accessible index. Require source references for factual answers and
an explicit "no supported answer" path when evidence is missing or conflicting.

## Governance rules

**`DGT-AI-010`**{ #dgt-ai-010 } — Agents must never directly perform irreversible business
transactions without deterministic server-side validation and authorization. Require explicit
confirmation or durable approval for high-impact actions, bound to the exact operation,
parameters, and authenticated approver. Revalidate permissions and current record state at
execution time; a prompt asking "Are you sure?" is not an approval control.

Classify tools by impact: read-only, reversible write, or high-impact/irreversible write.
Document which may run autonomously. Payment, deletion, and legally binding decisions need a
reviewed execution path; the model cannot approve its own request.

**`DGT-AI-020`**{ #dgt-ai-020 } — Business rules and authorization belong in deterministic
services, not in prompts. For Dataverse business operations, use plugins or Custom APIs to
enforce invariants for every caller. Prompts can explain a rule, but cannot be its only
implementation.

**`DGT-AI-030`**{ #dgt-ai-030 } — Prompts and agent instructions must be source controlled and
reviewed together with topics, tool contracts, knowledge-source configuration, and evaluation
datasets. Keep the unpacked solution and any separately maintained prompt assets in Git; record
model/orchestration settings and the released artifact version so evaluations are traceable.
Do not commit credentials, customer conversations, or restricted knowledge content.

Apply the existing [Governance & DLP](../operations/governance-dlp.md) controls to connectors,
HTTP endpoints, knowledge sources, publishing channels, and data movement. Agree data residency,
retention, permitted AI services, and licensing before connecting production data.

## Security guidance

**`DGT-AI-040`**{ #dgt-ai-040 } — Every agent tool and knowledge source must use an explicitly
documented identity and least-privilege access. Enforce authorization outside the model and
verify that users cannot obtain data or execute operations beyond their permitted scope.

- **End-user identity.** Prefer delegated user authentication for user-scoped data and actions.
  Agent sign-in does not automatically mean every connector runs as that user. Check the
  authentication mode of each tool and knowledge source in the published channel.
- **Managed identities.** Prefer managed identity or workload identity federation for supported
  service-to-service paths, such as a Function accessing Azure resources. Do not assume every
  Copilot Studio connector supports managed identity; verify the exact integration. See
  [Custom API managed identity](../coding/serverside/custom-api.md#managed-identity).
- **Service principals.** Where an application identity is required, use a dedicated identity
  per workload/environment and a purpose-built Dataverse application-user role. A shared
  service connection does not preserve the end user's permissions: enforce an explicit,
  trusted caller-to-operation authorization mapping, or do not expose that operation.
- **Secrets.** Store application-managed secrets only in Azure Key Vault, accessed through a
  supported secret reference or backend integration. Platform-managed connector credentials
  stay in the platform's credential store. Never put secrets in prompts, topic variables,
  source control, ordinary environment-variable values, or telemetry. If a connector cannot
  meet the required credential policy, choose a supported integration path instead.
- **Untrusted input.** Treat user messages, retrieved documents, tool outputs, and other agents'
  responses as untrusted data. Prompt injection must not change authorization, reveal secrets,
  or add tools. Validate tool schemas and values server-side; never execute model-generated
  code or arbitrary queries.
- **Audit logging.** Record the authenticated requester, execution identity, operation, target,
  approval reference, result, timestamp, release version, and correlation ID for business
  actions. Enable relevant Dataverse auditing and correlate it with tool/backend telemetry;
  conversation transcripts alone are not a transaction audit. Redact sensitive payloads,
  restrict log access, and agree retention.

Reuse the [Security Model](../customizing/security-model.md); do not introduce a parallel
agent-only permission model that bypasses it. Microsoft's
[Copilot Studio security and governance overview](https://learn.microsoft.com/en-us/microsoft-copilot-studio/security-and-governance)
describes the platform controls to verify.

## Copilot Studio + Dataverse enterprise patterns

### A business command, not a generic write tool

For example, expose `SubmitOrder(orderId, expectedVersion, requestId)` rather than allowing an
agent to set any order status. The backend checks the caller, record access, valid transition,
required approval, and current version before committing. Use a typed result containing the
persisted outcome and operation ID; the agent reports success only after confirmed execution.

For retriable writes, persist an idempotency key and reject reuse with different parameters.
Bind approval to the same command and parameter values. On timeout, query the operation status
before resubmitting: a lost response does not prove that the transaction failed.

Keep related Dataverse updates within the appropriate server-side transaction boundary.
External systems are not part of that transaction; use durable orchestration and explicit
compensation/reconciliation where needed. See
[Cloud & Integration](../coding/cloud-integration.md).

### Environment strategy and security roles

Use the existing [DEV / TEST / UAT / PROD baseline](environment-strategy.md), adding Build when
the selected pipeline needs it:

- **DEV:** unmanaged agent and dependent solutions; synthetic or approved test data.
- **TEST:** managed artifacts; automated evaluations, integration tests, and permission tests.
- **UAT:** the release candidate with business-owned scenarios and approval journeys.
- **PROD:** the approved artifact, production identities and sources, restricted maker access,
  and operational monitoring.

Separate maker, deployment, runtime application-user, and end-user roles. Runtime identities
never receive System Administrator as a shortcut. Deploy role definitions with the solution;
assign users/application users and bind connections separately per environment. Non-production
agents must not call production write endpoints or use production credentials.

### Deployment pipeline

Follow [Source Control](../alm/source-control.md),
[Build Pipeline](../alm/build-pipeline.md), and the chosen
[Deployment Approach](deployment-approach.md):

1. Export/unpack and review the agent solution, prompts, tool dependencies, and evaluation
   changes. Keep each component in one owning solution and preserve the existing
   [dependency direction](solution-architecture.md).
2. Deploy schema, roles, and Custom APIs before the agent components that depend on them.
   Include required solution-aware flows, connectors, connection references, and environment
   variable definitions. Build managed artifacts and promote the same approved artifacts.
3. Supply target-specific configuration using
   [environment variables and connection references](../customizing/envvars-connectionrefs.md).
   Provision identities, connections, permissions, and knowledge-source access separately;
   verify that no development URL or credential remains.
4. Apply documented post-deployment configuration. Copilot Studio settings that are not
   solution-aware include manual authentication, deployed channels, channel security, sharing,
   and Application Insights settings. Verify current behavior for the selected agent features;
   solution import alone is not proof of a ready-to-use agent.
5. Run evaluations and published-channel smoke tests under real user roles. Verify tool
   authorization and knowledge access in the target, obtain release approval, and publish
   through the supported process. Do not expose a production channel before its checks pass.
6. Record the solution/release version and configuration evidence. Rehearse disabling tools
   or the agent and redeploying a known-good release through the supported solution
   upgrade/recovery path; do not assume importing an older solution is a safe rollback.

Microsoft's [Copilot Studio ALM guidance](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/alm)
and [solution management overview](https://learn.microsoft.com/en-us/microsoft-copilot-studio/authoring-solutions-overview)
describe the supported transport and post-deployment boundaries.

## Testing strategy

**`DGT-AI-050`**{ #dgt-ai-050 } — Every agent release must pass a versioned evaluation dataset
and deterministic tool/authorization tests before production. Define measurable acceptance
thresholds with the business owner before testing, and retain results against the released
agent, prompt, knowledge, and model/configuration versions.

| Test layer | What to verify |
|---|---|
| Prompt and conversation testing | Intent selection, clarification, refusal, multilingual inputs where supported, and stable tool/parameter selection across paraphrases and multi-turn conversations |
| Grounding validation | Factual claims supported by cited sources, correct record identity, freshness, conflicting sources, and abstention when no authorized evidence exists |
| Tool unit and integration tests | Server-side business rules, schema validation, permissions, approval enforcement, idempotency, concurrency, and persisted outcomes |
| Adversarial and permission testing | Direct/indirect prompt injection, cross-user data leakage, malicious tool output, and attempts to bypass confirmation or escalate privilege |
| Reliability testing | Throttling, LLM/connector outages, timeouts after commit, duplicate delivery, exhausted capacity, and human handoff |

Use synthetic or approved, de-identified examples, including expected answers/source IDs,
allowed and forbidden tool calls, user persona, expected outcome, and scenario risk. Separate
tuning examples from a held-out regression set. Include successful, ambiguous, unsupported,
and adversarial requests. Run representative cases repeatedly: one successful chat is not
evidence of reliable behavior, and exact-string matching alone is unsuitable for generated
answers.

Track grounded-answer correctness, unsupported-claim rate, task completion, tool-call
correctness, latency, and cost per completed task. Set project-specific numerical thresholds,
sample sizes, and repeat counts; require zero unauthorized actions or data disclosures in
the test suite, and block release on any observed violation. Human review remains necessary
for high-impact cases; an LLM judge is supplementary, not the sole acceptance authority.

Rerun the regression set after prompt, tool, source/permission, model, or orchestration changes
and relevant platform updates. Sample production answers under the agreed privacy policy to
monitor unsupported claims and grounding regressions; add reviewed failures to the evaluation
dataset. Reuse [server-side unit tests](../testing/unit-testing-serverside.md) and the existing
[Definition of Ready / Done](../testing/dor-dod.md) for the deterministic parts.

## Operational excellence

**`DGT-AI-060`**{ #dgt-ai-060 } — Every production agent needs a named operational owner,
usage/cost alerts, bounded execution and retries, and a tested failure/handoff runbook. Never
report a business action as successful without a confirmed backend result.

- **Consumption and cost.** Monitor Copilot credit/capacity consumption and, for separately
  hosted model calls, token usage where exposed and Azure charges. Tokens are not a universal
  Copilot Studio billing unit. Track consumption per environment/agent and cost per completed
  task; budget for connector calls, search, Functions, and telemetry as well. Set alerts,
  spending controls where supported, and a defined response when budgets are approached.
- **Rate limits.** Account for Copilot Studio, connector, Dataverse, and external API limits
  separately. Honor `Retry-After` where available; use capped exponential backoff with jitter
  only for transient failures. Bound retries, total duration, tool calls, and agent handoffs.
  Queue long-running work with a status endpoint rather than keeping a chat turn open.
- **Outages and fallback.** If the LLM or grounding service is unavailable, state that clearly
  and offer a deterministic form, approved static information, or human handoff as appropriate.
  Do not invent an answer or silently switch to an unapproved model/service. An unavailable
  approval or authorization service blocks the write.
- **Error handling.** Distinguish invalid input, access denied, throttling, transient failure,
  and unknown transaction outcome. Retry only when safe; use idempotency and status lookup for
  writes. Give users a safe error message and correlation ID, not stack traces or credentials.
- **Monitoring and recovery.** Alert on failed actions, latency, handoff rate, unsupported
  answers, and permission failures. Provide an emergency path to disable write tools, revoke
  connections, or unpublish the agent. Record incident ownership and recovery verification.

Use [Monitoring & Telemetry](../operations/monitoring.md) for shared logging practices.
Check Microsoft's current [Copilot Studio quotas and limits](https://learn.microsoft.com/en-us/microsoft-copilot-studio/requirements-quotas)
and licensing/capacity terms at design time and before go-live rather than hard-coding changing
limits into the architecture.
