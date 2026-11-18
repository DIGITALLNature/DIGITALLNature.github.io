# AI in the delivery lifecycle

AI assistance does not transfer decision authority or acceptance responsibility.
Agree owners and required evidence using the existing delivery process.

| Activity | Reasonable AI usage | Human responsibility |
|---|---|---|
| Requirements | Structure approved notes, identify gaps, draft stories | Validate requirements and acceptance criteria with stakeholders; do not treat inferred requirements as agreed scope |
| Architecture | Explore alternatives and critique designs | Select the design, verify constraints, and record the decision in an ADR |
| Development / customizing | Generate or refactor code, formulas, configuration, and tests | Understand and approve the implementation; verify platform support and behavior |
| Code review | Identify smells, defects, and security concerns | Verify findings and own acceptance; preserve independent review |
| Testing | Generate candidate test cases and synthetic data | Ensure business coverage, correct expected outcomes, and trustworthy execution evidence |
| Documentation | Draft technical documentation and release notes | Verify against the implementation and keep it current |
| Troubleshooting | Analyze sanitized traces and propose hypotheses | Verify the diagnosis and test the change before applying it |
| Deployment | Draft scripts, configuration, and pipeline changes | Apply normal review, environment separation, approvals, and ALM controls |

The [engineering context and data rules](../coding/ai-assisted-engineering.md#data-and-context-boundaries)
apply to every row, including meeting notes, support tickets, and deployment logs.

## Planning and acceptance

For AI-assisted work, the ordinary [Definition of Ready / Done](../testing/dor-dod.md) remains
unchanged: generated work is not exempt from quality gates. Preserve decisions, reviewed
artifacts, and validation evidence in the project's normal source control and work-tracking
system. A chat history is not a substitute for an ADR, a requirement, or a test result.

For a runtime AI feature, also agree before implementation:

- the permitted use, affected users, data boundary, and responsible business/technical owners;
- supported and unsupported scenarios, deterministic controls, and human escalation;
- measurable evaluation thresholds, test sample sizes, and repeat counts;
- release evidence, operating budget, monitoring, and recovery responsibilities.

At acceptance, retain [evaluation results](../testing/ai-evaluation.md) against the release
configuration and verify [operational readiness](ai.md). A convincing demo
does not satisfy these gates.
