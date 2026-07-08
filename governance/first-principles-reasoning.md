---
template-version: 1.0.0
override: replacement
---

# First Principles Reasoning

> Foundational reasoning discipline — read at session start alongside the base system prompt.
> This governs *how* the agent reasons, before any pattern, convention, or prior solution is applied.

---

## Purpose

`engineering-principles.md` defines patterns for shaping a solution (SOLID, DRY, GRASP). `collaboration-patterns.md` defines the human-agent dynamic. Neither governs how the agent arrives at *what the solution should be* before a shaping pattern is selected. This file fills that gap.

First principles reasoning is a **guiding principle above the others** — it determines the fundamental requirements and constraints a solution must satisfy. SOLID/DRY/GRASP then shape how that solution is expressed. Applying a shaping pattern without first-principles grounding risks solving the wrong problem well.

This directly addresses `collaboration-patterns.md` weakness **W-5** — "Agent doesn't recognize reusable principles... doesn't abstract the underlying principle." First-principles reasoning is the discipline of naming the fundamental truth behind a decision instead of pattern-matching to the nearest precedent.

---

## What It Is

Breaking a problem down to foundational truths — facts verifiably true independent of convention, precedent, or assumption — and reasoning up from those truths, rather than reasoning by analogy to an existing solution or "how it's always been done."

**Reasoning by analogy** (pattern-matching to precedent) is fast but silently inherits the precedent's assumptions and constraints — some of which may not hold, or may never have held, for the current problem.

**First principles reasoning** rebuilds the solution from what is actually true, then checks the result against precedent — rather than starting from precedent and adjusting.

---

## Discovery Method

1. **State the actual outcome required** — not the currently assumed method of achieving it.
2. **Enumerate what is currently believed true** about the problem: requirements, constraints, design choices, "rules."
3. **Classify each item:**
   - **Fundamental** — true independent of team, tool, or convention (a protocol spec, a physical/data constraint, a documented business rule, a proven invariant).
   - **Inherited/assumed** — true because "it's how it's done here," a legacy decision, an unexamined convention, or something copied from a similar-looking prior case.
4. **Interrogate every inherited/assumed item** — ask "why" until reaching either a fundamental constraint or an unjustified assumption. Trace to source (spec, ADR, comment, person) where possible.
5. **Rebuild the solution from the fundamentals** identified in step 3 — do not automatically re-include inherited assumptions just because they were present before.
6. **Cross-check against known patterns and conventions only after** the fundamentals-based solution is derived. Use SOLID/DRY/GRASP and existing precedent to validate or refine the design — not to originate it.
7. **If the first-principles solution converges with existing convention, adopt the convention** — it was already correct. Don't manufacture novelty for its own sake.
8. **If it diverges, surface the divergence explicitly** to the human before proceeding — this is a design-level decision point (see Autonomy Level 2+ in `collaboration-directives.md`), not something to resolve silently.

---

## When to Invoke Explicitly

- Any architecture, system-shaping, or "why do we do it this way" decision (Autonomy Level 2+, per `collaboration-directives.md`).
- Before adopting an existing pattern or convention wholesale in a new or unfamiliar context.
- When a design decision appears arbitrary, cargo-culted, or unjustified by anything in the code or docs.
- When the human explicitly questions an assumption or asks what the real constraint is.
- When reasoning about legacy code whose original rationale is undocumented (retrofit work).
- During Assignment Intake — ADR check and Plan creation (see `workflows/assignment-workflow.md`, Step 3).

**Signal phrases** — human language indicating first-principles mode should be entered explicitly:
- "Why do we do it this way?"
- "What's actually required here?"
- "Let's start from scratch."
- "Is that actually true, or just how it's always been done?"

Do not invoke this as ritual on mechanical, Level 0 work — it is a cost paid for catching wrong-problem solutions, and that payoff is highest at design-level decisions, not routine implementation.

---

## Relationship to Other Governance

| File | Relationship |
|---|---|
| `engineering-principles.md` | SOLID/DRY/GRASP are downstream shaping patterns, applied *after* fundamentals are established here. |
| `collaboration-directives.md` | Autonomy Level 3 ("Challenge": question assumptions) is first-principles reasoning applied to the collaboration itself. |
| `collaboration-patterns.md` | Directly counteracts weakness W-5 by naming the fundamental principle behind a decision rather than just applying a pattern. |
| `workflows/assignment-workflow.md` | Applied during Assignment Intake (ADR check, Plan) — see Step 3. |

---

## Agent Application Notes

- Read this file at session start as part of the standard governance load (see `agent-context-protocol.md`) — it applies regardless of profile depth, the same as the base system prompt.
- When first-principles reasoning surfaces a divergence from existing convention, name it explicitly and let the human decide (per the HITL Approval Gate) rather than silently defaulting to either the convention or the novel solution.
- When a pattern from `engineering-principles.md` is applied, note the fundamental requirement it satisfies — this is what makes the principle reusable in a future, differently-shaped context.
