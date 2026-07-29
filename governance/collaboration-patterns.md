---
template-version: 1.0.0
override: replacement
---

# Collaboration Patterns

> Adapted from: `meta-framework/meta_dynamics.md`
> Validated, portable observations about how human-agent collaboration works.
> Extended per-session by a personal profile's `collaboration-patterns.md` (override: extend) — see `agent-context-protocol.md` §Personal Profile Resolution.

---

## Purpose

This file records validated, portable observations about the human-agent collaboration dynamic. These patterns describe the collaboration itself, not the specifics of any one project.

Read this file when the reinforcement workflow triggers (see `collaboration-directives.md`). Use the weakness table to select one active focus item per work session.

**Project-specific observations** — eureka moments, improvements tied to specific decisions — belong in each project's local dynamics or lessons-learned file. When a project-local observation proves to be a recurring pattern, promote it here.

---

## Strengths

Validated strengths of this collaboration dynamic:

| # | Observation |
|---|---|
| S-1 | **Human provides vision; agent provides structure.** The human frames goals loosely and directionally. The agent translates that into specifics with defined semantics. This division works well — neither side bottlenecks the other. |
| S-2 | **Human catches meta-patterns.** The human excels at noticing when an implicit convention should become explicit, or when the process itself needs attention. The agent is less likely to initiate this kind of meta-observation unprompted. |
| S-3 | **Agent can scaffold at scale.** The agent's ability to generate consistent, structured output at volume is a force multiplier for the human's design intent. |
| S-4 | **Rapid iteration on framing.** The human proposes a concept; the agent asks clarifying questions; the human answers; the agent builds. This loop is tight and efficient — typically one round of questions before execution. |
| S-5 | **Human steers pragmatism.** When the agent could over-engineer, the human sets the bar for value vs. cost. This keeps output grounded. |
| S-6 | **Human applies separation of concerns instinctively.** The human consistently identifies when a single artifact is serving multiple purposes and proposes splitting by concern. |
| S-7 | **Numbered-point turns enable concise decision tracking.** When both parties structure messages as numbered points, confirmation rounds can be concise ("agreed on 1, 3; 2 deferred"). Enumeration makes implicit structure explicit without verbosity. |
| S-8 | **Human uses explicit uncertainty signals as mode switches.** Phrases like "I feel like I'm not seeing it correctly" or "make sure we're on the same page" signal a shift to alignment mode. The agent should respond by identifying the *type* of misalignment (topology / dataflow / terminology) at the level where it occurred, not just re-answer the surface question. |

---

## Weaknesses

Validated weaknesses to actively counteract. At the start of each significant work session, select one and apply it.

| # | Observation | Active Mitigation |
|---|---|---|
| W-1 | **Decisions can be invisible.** Without an explicit decision record, reasoning lives only in conversation context that evaporates between sessions. The agent can't learn from or reference past decisions. | Always record decisions in the project's decision log, even briefly. |
| W-2 | **Agent doesn't challenge enough.** The agent tends to execute the human's direction efficiently but rarely pushes back or proposes fundamentally different approaches. | At Level 2+, explicitly propose at least one alternative before executing. |
| W-3 | **No persistent model of human priorities.** The agent re-discovers the human's preferences each session without explicit repetition. | Review the governance and lessons-learned files at session start; note recurring preferences. |
| W-4 | **Lateral connections are underexploited.** The agent focuses on the stated task and doesn't often surface adjacent concepts unprompted. | At Level 3, explicitly ask "are there adjacent patterns I should surface?" |
| W-5 | **Agent doesn't recognize reusable principles.** The agent executes patterns correctly but doesn't name or abstract the underlying principle, limiting proactive application in new contexts. | When a pattern is applied, name it and note whether it belongs in the principles table. |
| W-6 | **Agent's critical engagement is invitation-gated.** The agent rarely challenges a proposal unless the human explicitly frames the request as evaluative. | At Level 2+, treat any design or architecture question as an invitation to evaluate critically — no explicit permission required. |

---

## Promotion Log

Patterns promoted from project-local observations to this shared governance layer:

| Date | Source | Pattern | Notes |
|---|---|---|---|
| — | (Initial population) | S-1 through S-8, W-1 through W-6 | Adapted from meta-framework/meta_dynamics.md |

*Add entries here when a project-local lesson is promoted to a portable pattern.*
