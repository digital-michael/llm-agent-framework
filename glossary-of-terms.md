# Glossary of Terms

> Key terms used in the LLM Agent Collaboration Framework, with definitions and cross-references.
> Covers both framework-specific concepts and industry-standard terms as they apply here.

---

## Framework Terms

### Assignment
The unit of scoped work submitted to the agent. Captured in `templates/agent-assignment.md`. Includes goals, constraints, change risk level, implementation plan, and quality gates. See `governance/workflows/assignment-workflow.md`.

### Autonomy Levels (0–4)
A scale defining how much initiative the agent takes in a given work context — from pure execution (0) to co-creation (4). The agent selects a level based on work context; the user may override at any time.

| Level | Label | Initiative |
|---|---|---|
| 0 | Execute | None — do the work without comment |
| 1 | Inform | Flag deviations; note at breakpoints |
| 2 | Advise | Surface alternatives; push back on contradictions |
| 3 | Challenge | Question assumptions; surface adjacent risks |
| 4 | Co-create | Drive at system level; propose unsolicited structures |

Defined in `governance/collaboration-directives.md`.

### Change Risk Level
Classification of the risk and scope of a proposed change: **Low**, **Medium**, **High**, **Breaking**. Used during assignment intake to determine the appropriate level of rigor, review, and documentation. Replaces "impact classification." See `governance/workflows/assignment-workflow.md`.

### Context Hydration
The act of loading framework context at the start of a session — reading `.llm-framework.yml`, resolving the layer stack (infrastructure → team → personal), and making the resulting governance layer available to the agent. See `governance/agent-context-protocol.md` and `docs/tooling.md`.

### Governance
The standing process control layer — rules that always apply, regardless of the specific work underway. In this framework, governance files function as the agent's **guardrails**: behavioral constraints that shape and bound what the agent does. See `governance/README.md`.

### Guardrails
Non-negotiable behavioral constraints that apply regardless of any instruction, user request, or workflow step. Cannot be overridden by context or phrasing. Distinct from instructions, which describe preferred behavior. Defined in `governance/system-prompts/base-system-prompt.md`. Examples: no `git push` without explicit permission; no fabrication.

### HITL (Human-in-the-Loop)
A mandatory checkpoint where a human reviews and approves before the agent continues. In this framework, the primary HITL point is the **HITL Approval Gate** (Step 3b of the assignment workflow) — no implementation begins until the practitioner approves the plan. See `governance/workflows/assignment-workflow.md`.

### Instructions
Technology-specific behavioral instructions for the agent, composed with the base system prompt to provide language/stack-level guidance. Located at `library/<lang>/instructions.md`. Loaded as an overlay — language-specific rules extend the base without replacing it.

### Locked-In
The state in which an agent has loaded the minimum context required to work with quality and continuity. Three aspects must be engaged: governance, lessons learned, and work tracking. The agent declares its state at the start of every session. See `governance/agent-context-protocol.md`.

### Mode-Role Matrix
A lookup table mapping the active work mode (row) and topic domain (column) to the appropriate agent role. Enables consistent, predictable agent behavior without per-task negotiation. See `governance/agent-roles-guide.md`.

### Profile
A named configuration specifying which context sources are loaded at session start. Options: `minimal`, `standard`, `full`, `subagent`. The agent selects a profile based on task scope; the user may override. See `governance/agent-context-protocol.md`.

### ReAct Pattern
The underlying execution model of the iterative implementation loop: **Reason** about what to do → **Act** (implement) → **Observe** the outcome → reason about the next step. Each iteration is one complete ReAct cycle. See `governance/workflows/assignment-iterative-process.md`.

### Resource Lifecycle Contract (RLC)
A framework-level thinking discipline applied to any resource at any level — code, operations, infrastructure, process, or external system. Asks whether the full lifecycle of a resource has been considered across six phases: Allocation, Configuration, Activation, Primary Use, Deactivation, and Deallocation. Augments SOLID, GRASP, DRY, IoC, and DI — does not replace them. Applied as a consideration lens by the agent during review; documentation is produced only when scale or criticality warrants it. Verification scales on a gradient from silent agent check (small) to human sign-off with paired unit and integration tests (large/critical). See `governance/rlc.md`.

### Retrospective
A required step at the end of every assignment. Captures lessons learned (from three sources: LLM agent, practitioner, tech stack), identifies patterns that recur, and updates governance when warranted. See `governance/workflows/assignment-workflow.md`.

### Session Context
A file capturing the state of active work for cross-session or cross-model handoff. Functions as the agent's compressed episodic memory. Updated at session end; loaded at session start when resuming. See `templates/session-context.md`.

### Work Modes
Three modes describing the nature of the current work — detected from context signals, not declared explicitly:

| Mode | Nature | Goal |
|---|---|---|
| **Discover** | Divergent — open-ended exploration | Discover *what* to build or *how* to think about the problem |
| **Develop** | Convergent-experimental — spikes and prototypes | Test whether an idea works |
| **Deliver** | Convergent-permanent — execution against a locked spec | Build what was agreed |

Defined in `governance/agent-roles-guide.md`. Map to the Double Diamond design process.

### Agent Roles
Three roles defining the agent's stance within a given mode and topic: **Lead** (agent drives), **Partner** (shared initiative), **Support** (agent follows). Applied via the Mode-Role Matrix. See `governance/agent-roles-guide.md`.

---

## Industry Terms Used Here

### ADR (Architecture Decision Record)
A short document capturing an architectural decision, its context, and its consequences. Standard format from adr.github.io. Required for changes classified as High or Breaking. See `governance/workflows/assignment-workflow.md`.

### Double Diamond
A design process framework from the UK Design Council: Discover → Define → Develop → Deliver. The framework's three work modes (Discover / Develop / Deliver) align with the outer three phases.

### Memory Taxonomy (Agent)
Standard classification of agent memory types:
- **Working memory** — in-context, bounded by context window
- **External/procedural memory** — durable rules (governance files, system prompt)
- **Episodic memory** — session history (`session-context.md`, assignment records)
- **Semantic memory** — accumulated knowledge (lessons-learned, principles)

Mapped in `governance/agent-context-protocol.md`.

### Override System (replacement / extend)
Mechanism by which team and personal profiles customize infrastructure defaults. Each governance file carries `override: replacement` (entire file replaces) or `override: extend` (named items replace matching items only). Resolution order: personal > team > infrastructure.

### Sparse Checkout
A git feature for checking out only a subset of a repository's files. Used in the initial profile scaffolding pattern to copy template files from infrastructure without establishing a submodule relationship.

### System Prompt
The persistent instruction block provided to an LLM at the start of every session. Sets behavioral baseline before any user input. In this framework: base system prompt + language instructions + governance context. See `governance/system-prompts/`.

---

## Terms Intentionally Not Used

| Avoided Term | Used Instead | Reason |
|---|---|---|
| "Reinforcement" (for the feedback loop) | "Reinforcement workflow" is kept as-is | Industry "reinforcement" implies RL reward signals; the concept here is a feedback/improvement cycle. May be renamed in a future revision. |
| "Posture" | **Role** | "Posture" is uncommon in software development contexts; "role" is universally understood. |
| "Interaction levels" | **Autonomy levels** | Maps to industry-standard agent autonomy classification; more precisely describes what the scale measures. |
| "Human Review Gate" | **HITL Approval Gate** | HITL is the standard industry term for mandatory human checkpoints in automated pipelines. |
| "Impact classification" | **Change risk level** | Aligns with ITIL change management vocabulary; more precisely describes the scale. |
| "Load context" (tool) | **Context hydration** | "Context hydration" is the emerging standard term in agentic systems for populating working context before a session. |
| "system-prompt-snippet" | **instructions** | Cleaner and aligns with how platforms (GitHub Copilot, Cursor) name these files. |
