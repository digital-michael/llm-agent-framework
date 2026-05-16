# TODO — Future Refinements

> Items identified during framework review that are deferred for future inclusion.
> These represent known gaps relative to industry standards — address when the framework matures or when a concrete need arises.

---

## Evals (Evaluation Framework)

**What it is:** Systematic, reproducible measurement of LLM agent output quality. Evals are automated test suites that score model responses against expected outputs, rubrics, or ground-truth references. They are a major pillar of responsible AI development and production agent systems.

**Why it matters:** The framework has retrospectives and quality gates, but these are human-assessed and per-assignment. Evals provide a repeatable, objective quality signal across sessions and across model versions.

**Future work:**
- Define what an "eval" looks like for an LLM agent collaboration framework (e.g., does the agent follow the workflow? Does it emit correct declarations? Does it apply lessons-learned?)
- Consider lightweight eval sets for: HITL gate compliance, Guardrails adherence, context protocol correctness
- Reference: LangSmith, Braintrust, Inspect AI, OpenAI Evals

---

## Prompt Injection

**What it is:** A security attack where adversarial content in agent inputs (files read by the agent, tool output, web fetches, user-provided content) overrides the agent's system instructions. Critical risk for agents that consume external content or use tools.

**Why it matters:** As agents become more autonomous and read more external content (code files, documentation, APIs), prompt injection becomes an active attack surface. The framework currently has no guidance on this.

**Future work:**
- Add a Prompt Injection section to `governance/system-prompts/base-system-prompt.md` Guardrails
- Add guidance to `library/` instructions for languages where agents read external files (bash, python especially)
- Define what constitutes untrusted input in this framework's operating context
- Reference: OWASP LLM Top 10 (LLM01), NIST AI Risk Management Framework

**Note:** An infrastructure-level solution (input sanitization, content filtering) should be addressed at the deployment layer. This item covers the governance/awareness layer.

---

## Agent Observability

**What it is:** Tracing, logging, and monitoring of agent behavior in production — what decisions the agent made, what tools it called, what it cost, and how long it took. Standard tooling includes OpenTelemetry, LangSmith, Arize Phoenix, and Helicone.

**Why it matters:** Without observability, debugging agent behavior in production is guesswork. Cost tracking, latency analysis, and decision auditing require structured traces.

**Future work:**
- Define what observability events are meaningful in this framework's context (session start, context hydration, HITL gate, retrospective completion)
- Consider a lightweight `governance/observability.md` that defines what to log and when
- Connect to the assignment workflow: each step is an observable event

**Note:** The infrastructure-level implementation (trace collection, dashboards) is an external concern. This item covers the governance/contracts layer — what the framework says agents should emit.
