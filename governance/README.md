# Governance

> Language-agnostic governance layer for LLM Agent collaboration.
> These files are infrastructure defaults — they apply to all projects, all languages, and all team members.
> Team and personal profiles may override specific files. See `override` field in each file's front matter.

---

## Contents

| Directory / File | Purpose | Override |
|---|---|---|
| `system-prompts/` | Base system prompt template and composition guidance | replacement |
| `first-principles-reasoning.md` | Foundational reasoning discipline: decompose to fundamentals before applying patterns or convention. Loaded at every session start, regardless of profile. | replacement |
| `separation-of-concerns.md` | Foundational categorization discipline: sort a problem into logical units by data/information/process/action/responsibility before applying SOLID/GRASP/DRY. Loaded at every session start, regardless of profile. | replacement |
| `lessons-learned/` | Lessons template and accumulation structure | replacement |
| `assignments/` | Assignment template (governance-layer copy) | replacement |
| `workflows/` | Assignment lifecycle and iterative implementation workflows | replacement |
| `agent-context-protocol.md` | Locked-In context protocol: profiles, declarations, delegation workflow | replacement |
| `collaboration-directives.md` | Operating model: autonomy levels, reinforcement loop, reusable principles | replacement |
| `collaboration-patterns.md` | Default collaboration strengths and weaknesses | replacement |
| `agent-roles-guide.md` | Work modes (Discover/Develop/Deliver), agent roles, and mode-role matrix | replacement |
| `testing-standards.md` | Testing standards by environment (local/staging/production) and coverage targets | replacement |
| `engineering-principles.md` | SOLID, DRY, GRASP, and architectural standards | replacement |

---

## Governance as Guardrails

In LLM agent systems, the term **guardrails** refers to constraints that shape and bound agent behavior — hard rules that override any competing instruction. This governance layer is the primary guardrails mechanism for this framework:

- **System prompt** — provides behavioral instructions that establish the agent's operating mode from the start of every session
- **Guardrails section** (in `system-prompts/base-system-prompt.md`) — non-negotiable constraints that cannot be overridden by any other instruction, context, or request
- **Workflows** — constrain the sequence of operations; gates are hard checkpoints where human approval is required before the agent continues
- **Governance files** — define what constitutes acceptable behavior in each domain (collaboration, code quality, testing, etc.)

Governance files should be read as constraints, not suggestions. An agent that follows the communication guidelines but skips the HITL Approval Gate is out of compliance.

---

## Governance vs. Assignment

Governance is *standing* process control — rules that always apply, regardless of the specific work.

An assignment is *scoped* task direction — goals, constraints, and plan for one unit of work.

Governance informs assignments. Assignments do not modify governance directly — lessons from assignments are reviewed and folded back into governance deliberately, at the retrospective.

---

## Maintenance

- Review and update governance after every significant retrospective
- Version governance changes (git commits with meaningful messages)
- When a lessons-learned entry recurs across three or more assignments, it belongs in the base system prompt
- Language-specific lessons belong in `../library/<lang>/governance-overlay.md`
