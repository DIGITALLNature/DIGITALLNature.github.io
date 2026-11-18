# AI engineering principles

AI can accelerate engineering and power solutions, but does not reduce our standards for
ownership, review, testing, or operation.

## Principles & corporate-policy boundary

Corporate and customer policies determine **whether** a tool, service, and data use are
permitted. These guidelines define **how** to engineer responsibly within that permission.
It does not approve any product or override restrictions on confidentiality, privacy,
intellectual property, residency, retention, or contractual obligations. Resolve unclear
approval with the responsible policy/security owner before using the tool.

1. **Know what leaves the boundary.** Understand which provider receives context, code,
   data, telemetry, and attachments, including through extensions and connected tools.
2. **Constrain context.** Give AI what the task needs, not everything it can access.
3. **Verify generated artifacts.** Compilation is not validation; verify behavior,
   dependencies, platform support, and security.
4. **Keep critical behavior deterministic.** Prompts do not replace business rules,
   authorization, or transaction validation.
5. **Test AI probabilistically.** A successful single execution is not evidence of
   reliable behavior. Evaluate representative cases repeatedly against agreed thresholds.
6. **Design for failure.** Account for incorrect answers, refusals, timeouts, unavailable
   services, and behavior changes after model or platform updates.
7. **Maintain human ownership.** A named person remains accountable for the artifacts,
   architectural decisions, and deployed behavior.

## Applying the principles

Using AI to write a plugin does not make the deployed plugin an AI-enabled solution.
It still needs the ordinary deterministic tests and review. A solution that calls a model
at runtime needs those controls **plus** behavioral evaluation and AI operational controls.

Practical guidance sits with the work it supports:
[coding and customizing](../coding/ai-assisted-engineering.md),
[solution architecture](../architecture/ai-enabled-solutions.md),
[testing](../testing/ai-evaluation.md), and
[delivery](../alm/ai-delivery-lifecycle.md).
