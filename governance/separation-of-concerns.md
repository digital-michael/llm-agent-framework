---
template-version: 1.0.0
override: replacement
---

# Separation of Concerns

> Foundational reasoning discipline — read at session start alongside the base system prompt and `first-principles-reasoning.md`.
> This governs how the agent *categorizes* a problem early, so architectural boundaries follow natural seams instead of arbitrary ones.

---

## Purpose

`engineering-principles.md` previously carried Separation of Concerns as one bullet alongside Loose Coupling and High Cohesion under "Architectural Standards" — implying it was a peer of those, applied at the same stage as SOLID/GRASP/DRY. It is not. Those patterns shape a unit's internals and its relationships to other units *after* the units themselves have been identified. Separation of Concerns is the categorization act that identifies the units in the first place.

Used early and well, it prevents an entire class of downstream problems: God objects, services that mix orchestration with business logic, files that serve two audiences, and "obvious" scaffolding that silently entangles unrelated responsibilities. Used late, it becomes a refactor rather than a design decision.

This sits alongside `first-principles-reasoning.md` as a second higher-order guiding principle:

- **First principles reasoning** asks *what is fundamentally true and required* about the problem.
- **Separation of concerns** asks *how do those fundamentals sort into distinct logical units* — by category of data, information, process, action, and responsibility — so the architecture can be organized along real seams.

Both are applied before SOLID, GRASP, and DRY. Those three then shape the units this discipline identifies.

---

## What It Is

The discipline of recognizing, early in a design process, that a problem is not one undifferentiated thing — it contains distinct categories of concern that should not be collapsed into a single unit, file, service, or role just because they arrived together or are convenient to co-locate.

**Categories to sort by** (not exhaustive, but the recurring ones):

| Category | Question it answers | Example seam |
|---|---|---|
| **Data** | What is stored, and in what shape? | Schema/model definitions vs. the logic that operates on them |
| **Information** | What is derived, computed, or presented from data? | A view/report vs. the underlying record |
| **Process** | What sequence of steps transforms inputs to outputs? | A workflow/pipeline vs. the individual operations it calls |
| **Action** | What is a discrete, triggerable operation? | A command/mutation vs. a query |
| **Responsibility** | Who or what is accountable for a decision or outcome? | An authorization decision vs. the business logic it gates |

A design that conflates two or more of these categories in one unit has usually collapsed a seam prematurely — not because it's stylistically messy, but because the categories tend to *change for different reasons and at different rates*. That mismatch in rate of change is the actual cost of ignoring the seam (see High Cohesion / Low Coupling in `engineering-principles.md`, which describe the same cost from the shaping-pattern side).

---

## Discovery Method

1. **List the concerns present**, not the components. Before naming any class, service, or file, enumerate what categories of data, information, process, action, and responsibility the problem actually involves.
2. **Ask, for each pair of concerns, whether they change for the same reason.** If two concerns can change independently of each other (a new business rule vs. a new storage format; a new authorization policy vs. a new computation), they belong in different units — even if today's implementation would be simpler combined.
3. **Ask who the audience is for each concern.** A concern read/modified by a different role, team, or system than another concern is a signal for a separate boundary, not just a separate function.
4. **Provisionally assign each concern to a logical unit or role.** This is the architectural organization — modules, services, layers — derived directly from the categorization, not from an existing template or the shape of a similar-looking prior solution.
5. **Only then apply SOLID/GRASP/DRY** to shape each unit's internals and its interfaces to the others. If a shaping pattern fights the categorization from step 4, re-examine the categorization first — don't force-fit the pattern.
6. **Re-check when scope changes.** A concern boundary that was correct at initial design can decay as a problem grows (a "simple" action gate accretes into a policy engine); re-run this discovery when a unit outgrows its original category.

---

## When to Invoke Explicitly

- At the very start of any design or architecture task — before naming files, classes, services, or interfaces (paired with `first-principles-reasoning.md`, ideally the same pass).
- When a single file, class, or service is proposed to "just handle" more than one of: data shape, derived information, a process/workflow, a discrete action, or a decision/responsibility.
- When scaffolding or "obvious" setup code is being generated — this is where concerns get silently merged by convenience.
- When reviewing existing code for a change: if the code to be touched already mixes concerns, decide (and log) whether untangling it is in scope, per `engineering-principles.md`'s SoC quality-gate note.
- When a human proposes splitting an artifact — `collaboration-patterns.md` S-6 notes this is a strength the human brings instinctively; the agent should recognize and act on it without needing it spelled out twice.

---

## Relationship to Other Governance

| File | Relationship |
|---|---|
| `first-principles-reasoning.md` | Peer, higher-order principle. First-principles reasoning establishes what's fundamentally true; this file categorizes those fundamentals into logical units. Apply both before reaching for shaping patterns. |
| `engineering-principles.md` | SOLID, GRASP, DRY, Loose Coupling, and High Cohesion shape and validate the units this discipline identifies — they do not identify the units themselves. |
| `collaboration-patterns.md` | Strength **S-6** ("human applies separation of concerns instinctively") — this file is the discipline that lets the agent do the same, proactively. |
| `collaboration-directives.md` | Reusable Principle "Applied separation of concerns" points here as the canonical source. |
| `workflows/assignment-workflow.md` | Applied during Assignment Intake (ADR check, Plan) — see Step 3 — alongside first-principles reasoning. |

---

## Agent Application Notes

- Read this file at session start as part of the standard governance load (see `agent-context-protocol.md`) — it applies regardless of profile depth, the same as the base system prompt and `first-principles-reasoning.md`.
- Run this categorization pass *before* proposing file/class/service structure, not as a review step after code exists. Retrofitting concern boundaries is a refactor; identifying them up front is design.
- When the agent notices a concern-mixing violation in existing code that is in scope for modification, note it even if fixing it is out of scope for the current unit of work (same rule as SOLID violations in `engineering-principles.md`).
