# AI ALM & operations

AI does not introduce an alternative delivery track. Use the existing
[Source Control](../alm/source-control.md), [Build Pipeline](../alm/build-pipeline.md), and
[Deployment Approach](../architecture/deployment-approach.md) for Power Platform, Azure,
Azure DevOps, GitHub Actions, or hybrid workloads.

## AI-assisted delivery

Generated code, scripts, formulas, and pipeline configuration follow normal pull requests,
tests, approvals, and artifact promotion. Coding agents do not bypass protected branches
or gain production access to make deployment easier. Keep the approved artifacts and test
evidence; do not commit sensitive prompts or chat transcripts as a substitute.

## Runtime AI release assets

**`DGT-AI-120`**{ #dgt-ai-120 } - Version and review runtime AI instructions, tool contracts,
grounding configuration, model settings where exposed, and evaluation assets alongside the
solution. Production use requires a named operational owner, bounded execution, usage/cost
alerts, and tested failure and recovery procedures.

Record which combination was evaluated and released. Keep credentials, restricted source
content, and customer conversations out of Git. Version source/index configuration and
approved dataset references rather than copying protected content into the repository.

1. Review the change and its evaluation impact; build and promote the same approved artifacts.
2. Separate development, test, UAT, and production identities, connections, endpoints, and
   knowledge access. Non-production must not invoke production write endpoints.
3. Bind target configuration through supported environment variables, connection references,
   secret stores, and deployment tooling. Document settings that are not transported with
   the artifact rather than assuming import completes setup.
4. Run evaluations and permission/integration tests in the target environment. Verify the
   actual published interface or application path, not only an authoring preview.
5. Obtain release acceptance, record artifact/configuration versions and test results, and
   verify monitoring and recovery readiness before exposing the capability.

For solution-aware agents, follow the detailed
[Copilot Studio deployment pipeline](../architecture/agentic-solutions.md#deployment-pipeline).

## Operate and recover

- **Ownership and signals.** Assign an operational owner and escalation route. Monitor
  task failures, unsupported answers, invalid output, permission failures, latency,
  handoffs, and consumption using [Monitoring & Telemetry](../operations/monitoring.md).
- **Safe telemetry.** Record correlation IDs, version/configuration identifiers, and outcomes.
  Redact sensitive payloads, restrict access, and apply agreed retention. Do not log full
  prompts, retrieved documents, or responses by default.
- **Budgets and execution bounds.** Set alerts and a response for approaching capacity/cost
  limits. Bound retries, total duration, tool calls, and handoffs. Include retrieval,
  connector, compute, and telemetry costs, not only model consumption.
- **Explicit failure.** Distinguish invalid requests, refusal, access denial, service failure,
  and unknown transaction outcomes. Never report success without a confirmed backend result.
  Use a tested deterministic fallback or human handoff; do not silently change providers.
- **Recovery.** Rehearse disabling the AI feature or write tools, revoking access, and
  restoring a known-good configuration through the supported deployment process.
  Model rollback may not be available; retain a safe non-AI path or disable the capability.
  Verify permissions and behavior after recovery.

Monitor provider/platform changes and rerun [evaluations](../testing/ai-evaluation.md) when
behavior or dependencies change. An unchanged Git commit does not guarantee unchanged model
behavior.
