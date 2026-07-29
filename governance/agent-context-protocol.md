---
template-version: 1.0.0
override: replacement
---

# Agent Context Protocol

> Adapted from: `meta-framework/meta_context_protocol.md`
> General-purpose. Tooling-agnostic. Adapt path references for your platform.

---

## Purpose

This document defines how an LLM agent manages its working context at session start, across an ongoing conversation, and when delegating work to a subagent. The concept and vocabulary are defined here; platform-specific implementation (file paths, memory systems) belongs in your tooling configuration.

---

## Core Concept: Locked-In

**Locked-In** is the state in which an agent has loaded the minimum context required to work with quality and continuity on the current task.

| Aspect | Engaged When |
|---|---|
| **Governance** | Agent has read the relevant governance files (system prompt, applicable overlays) and knows where canonical sources live |
| **Lessons learned** | Agent has scanned the project's lessons-learned record for entries relevant to the current task domain before beginning work |
| **Work tracking** | Agent has either created an active implementation plan / todo list (multi-step work) or explicitly noted the task is single-step and tracking is unnecessary |

If all three are satisfied: **Locked-In**. If any are missing: **Not Locked-In** — the agent must name which aspects are absent and why.

---

## Profile System

A **profile** specifies which context sources are loaded. Profiles scale from narrow (minimal load, targeted delegation) to broad (full continuity, architecture work).

| Profile | Contents | Typical Use |
|---|---|---|
| `minimal` | Lessons-learned (scanned for relevant sections only) | Narrow bug fix, single-file task, targeted delegation |
| `standard` | Lessons-learned + active implementation plan + governance (relevant overlays) | Most interactive work sessions |
| `full` | Standard + all decisions + patterns + full governance | Architecture, L3/L4 design, cross-cutting changes |
| `subagent` | Custom subset provided by parent — receiver declares exactly what arrived | Delegation where parent controls content |

**Always loaded, regardless of profile:** `system-prompts/base-system-prompt.md`, `first-principles-reasoning.md`, `separation-of-concerns.md`, and the personal profile if one is configured (see below). These define the agent's operating stance — not domain-specific overlay content — so even a `minimal` profile includes them.

**Overlay:** any profile can include `+component:<name>` to additionally load that component's guidance docs. Maximum two overlays per profile; beyond that use `full`.

**Profile selection authority:**
- **User ↔ Agent:** agent proposes profile based on task scope; user may override
- **Agent ↔ Agent:** parent always specifies; subagent confirms receipt

---

## Personal Profile Resolution

If the project's `.llm-framework.yml` has a `personal:` field set, load the personal profile **first, every session, regardless of which profile (`minimal`/`standard`/`full`) is selected for the task.** Personal profile is a mode-level setting (who you're working with), not a task-scoped one (how much governance this task needs) — the two are independent.

**Mechanic:**
1. Read `.llm-framework.yml`. If `personal:` is absent, skip this section entirely — no personal layer, no error.
2. If present, also read `personal_identity:` if set (a stable identity key — see below). Load, in order:
   - `<personal>/context-protocol.md` — overrides this document's declaration/profile-selection preferences for this session
   - `<personal>/collaboration-preferences.md` — overrides `governance/collaboration-directives.md`'s autonomy-level defaults
   - `<personal>/collaboration-patterns.md` — extends `governance/collaboration-patterns.md` (named/numbered entries replace matching ones; new entries add to the set)
   - `<personal>/roles-guide.md` — overrides `governance/agent-roles-guide.md`
3. Load domain/team files next, then project-specific files — personal is the most general "who," domain/project narrow to "what."

**`personal_identity`** is a single stable resolution key — deliberately independent of any OS login name (varies per workstation for the same person) and of any single git platform's username (GitHub/GitLab/Forgejo/Bitbucket usernames may differ from each other for the same person). Per-platform username mapping, if needed, belongs inside the personal profile repo itself (e.g. its README), not duplicated into every project's `.llm-framework.yml`.

**Team/shared repos:** a personal profile is cross-repo — the same `personal:`/`personal_identity:` values are committed into any project's `.llm-framework.yml` regardless of whether that project's domain is solo or team-shared, as long as there is currently one real operator across those repos. This does not yet solve the case of a repo with more than one actual contributor, each needing their own personal profile loaded from the same committed dotfile — that requires a per-developer local override mechanism (e.g. a gitignored `.llm-framework.local.yml`) that does not exist yet. Build it when a second real contributor actually needs it, not preemptively.

---

## Declaration

At the start of every new conversation, the agent emits a **Locked-In declaration** in its first response. It is not repeated on subsequent turns within the same conversation.

```
Locked-In | <context-type> | profile:<name>
   loaded: <aspect> ✓  <aspect> ✓  ...
```

```
Not Locked-In | <context-type> | profile:none
   missing: <aspect>  — [brief reason]
```

**Context types:**

| Type | Meaning |
|---|---|
| `user-request` | Human opened a new session |
| `session-resume` | Human returning after a break; prior state or timestamp available |
| `delegation` | Agent invoked as subagent by a parent agent |
| `unknown` | Cannot determine — treat as cold start |

---

## Idempotency

Context is loaded **once per conversation**, on the first response. It is not reloaded on subsequent turns unless:
- The user explicitly requests a reload, or
- The agent detects a significant state change mid-session (e.g., a key file was changed)

When staleness is suspected but not confirmed, the agent notes `[may be stale — loaded N turns ago]` inline rather than silently reloading.

---

## User ↔ Agent Workflow

```
New conversation
      │
      ▼
Detect context type (user-request / session-resume / unknown)
      │
      ▼
Select profile based on task scope:
  - single-step narrow task    → minimal
  - standard work session      → standard
  - architecture / deep design → full
      │
      ▼
Load context sources for profile
      │
      ▼
Emit Locked-In declaration (first response only)
      │
      ▼
Work proceeds — no reload unless explicitly triggered
```

---

## Agent ↔ Agent (Delegation) Workflow

**Parent responsibilities:**
1. Select a profile appropriate for the delegated task scope
2. Inject relevant context into the subagent prompt
3. Declare the profile in the prompt so the subagent can confirm it

**Subagent responsibilities:**
1. Confirm receipt of the injected context — do not load context autonomously
2. Emit a delegation receipt at the start of its result
3. If a blocking context gap is detected, flag it immediately and do not attempt the task

**Delegation receipt format:**
```
Locked-In | delegation | received-profile:<name>
   received: <aspect> ✓  <aspect> ✓
```

```
Not Locked-In (gap) | delegation | received-profile:<name>
   missing: <aspect>  — NEEDED; parent should re-delegate with richer profile
```

---

## Memory Taxonomy

The framework maps to the standard agent memory taxonomy. Understanding which type each file represents helps you reason about what the agent "knows" and when.

| Memory Type | Description | Framework Equivalent |
|---|---|---|
| **Working memory** | In-context: what the agent holds during the current conversation | Active conversation + currently loaded governance files |
| **External / procedural memory** | Durable rules and standards the agent always applies | `governance/` files, `library/` instructions, system prompt |
| **Episodic memory** | Record of past sessions, decisions, and completed work | `session-context.md`, assignment history, decision log |
| **Semantic memory** | Accumulated knowledge — lessons, patterns, validated principles | `governance/lessons-learned/`, `collaboration-patterns.md`, `engineering-principles.md` |

Working memory is bounded by the model's context window. The other types persist in files and are loaded selectively at session start based on the active profile.

---

## Context Window Management

LLM context windows are finite. Long sessions, large governance loads, and verbose conversation history can exhaust available context and degrade response quality. The following practices apply:

**At session start:**
- Load governance files selectively, not exhaustively — use the minimum profile that serves the task
- The `standard` profile is sufficient for most sessions; `full` is reserved for architecture and design work

**During a session:**
- When a session is long and the agent references earlier content with reduced accuracy, treat it as a context window signal
- Use `session-context.md` to capture state and resume in a new, clean context rather than continuing in a degraded one

**At session end:**
- Update `session-context.md` before closing a long session — this is the compression mechanism
- A well-maintained `session-context.md` makes context window limits a non-issue: the next session loads only what it needs

**Cross-session handoff:**
- When switching models or resuming after a break, always load `session-context.md` first — it is the compressed representation of prior working memory
