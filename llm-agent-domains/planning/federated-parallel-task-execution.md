# Federated Parallel Task Execution

> Architecture for distributing large AI-assisted tasks across federated nodes using parallel execution, model routing, event-driven coordination, and structured rollup patterns.

**Last Updated:** 2026-06-23
**Status:** Design — active exploration, not yet prototyped
**Work Mode:** Discover → moving toward Develop

---

## Table of Contents

1. [Goals](#goals)
2. [Features](#features)
3. [Architecture Breakdown](#architecture-breakdown)
   - [Task Graph and Plan](#1-task-graph-and-plan)
   - [Branch Naming Convention](#2-branch-naming-convention)
   - [Model Annotation — Two-Layer](#3-model-annotation--two-layer)
   - [LLM Skill: Model Router](#4-llm-skill-model-router)
   - [Capability Registry](#5-capability-registry)
   - [Event System](#6-event-system)
   - [Early Interface Contracts](#7-early-interface-contracts)
   - [Leaf Promotion Pattern](#8-leaf-promotion-pattern)
   - [Integration Skill](#9-integration-skill)
   - [Human Review Gates](#10-human-review-gates)
4. [What's Left to Consider](#whats-left-to-consider)
5. [Glossary of Terms](#glossary-of-terms)
6. [Context and References](#context-and-references)

---

## Goals

- **Narrow frontier model use.** Reserve large, expensive models for tasks that genuinely require them — planning, ambiguous reasoning, cross-cutting architecture decisions, and final coherence checks.
- **Distribute to smaller and local models.** Route well-scoped, concrete subtasks to local models (Ollama) or mid-tier API models based on annotated capability requirements.
- **Parallelize across federated nodes.** Make full use of all participating nodes in the local-lab-ai-stack simultaneously rather than processing serially.
- **Maintain quality through structured verification.** Each unit of work passes automated gates (lint, type-check, compile, test) and LLM verifier checks before integration.
- **Make integration a first-class, monitored process.** Rollup is structured, asynchronous, and recoverable — failures are learning events, not restarts.
- **Support human oversight at any point.** Review gates are insertable by both LLM suggestion and human request, at any level of the hierarchy.

---

## Features

| Feature | Description |
|---|---|
| Annotated task graph | DAG produced by planner; each node carries model annotation, interface contract, dependencies, and branch name |
| Two-layer model annotation | Ambient context (language/domain) + capability assertion (reasoning, generation, verification) drives routing |
| Federated parallel dispatch | Independent subtasks fan out to available nodes simultaneously; no serial waiting |
| Capability registry | Nodes advertise available models, GPU/CPU tier, and queue depth; router queries before dispatching |
| Event-driven coordination | Git as state store; lightweight event broker for notifications — no polling |
| Early interface contracts | Type signatures, API shapes, and schemas committed before implementation begins; dependents code to contracts, not implementations |
| Leaf promotion pattern | Successful rollup promotes the merged branch to a new leaf; failed rollup keeps leaves open and issues a structured failure report for retry |
| Integration skill | Async, parallel skill triggered by rollup events; handles semantic conflict resolution; produces structured failure reports |
| LLM-suggested review gates | Integration skill flags rollups that warrant human review based on conflict density, cross-cutting changes, or repeated failures |
| Human-insertable review gates | Any stakeholder can pause downstream work and queue a review at any branch or rollup point |
| Retry with learning | Failed rollup context becomes a prompt addendum for the next attempt; annotation may be updated if mis-annotation is identified |

---

## Architecture Breakdown

### 1. Task Graph and Plan

A **frontier model** produces the DAG at the start of a task. This is the primary justified use of a large model — the planning output shapes everything downstream.

Each node in the graph carries:

```
task:
  id:                   unique identifier
  description:          what this task must produce
  branch:               deterministic branch name (see convention below)
  annotation:
    ambient:            Go | Dart/Flutter | React | medical-domain | ...
    capability:         reasoning+abstraction | code-generation | verification | integration | ...
  contract:             path to interface contract file committed at branch creation
  dependencies:         list of task ids whose contracts must exist before this starts
  verification:         criteria for automated and LLM verifier checks
```

The graph is the source of truth for the dispatcher. It is committed to the repository at plan time and does not change during execution — only retry metadata is appended.

---

### 2. Branch Naming Convention

Branch names encode position in the task hierarchy. The parent path is always the rollup target.

```
{plan-id}/{parent-path}/{task-slug}
```

**Examples:**
```
auth-service/root
auth-service/user-model/schema
auth-service/user-model/persistence
auth-service/api-layer/endpoints
auth-service/api-layer/middleware/jwt
```

Rules:
- `root` is always the integration trunk for the plan
- Parent path is literal — rollup target is derived by dropping the last segment
- Slugs are kebab-case, max 3 words, descriptive of output not process
- Plan ID matches the assignment ID from `governance/workflows/`

---

### 3. Model Annotation — Two-Layer

Annotations are intentionally coarse at Layer 1 and targeted at Layer 2. The combination is what drives routing precision.

**Layer 1 — Ambient Context** (set at plan level, inherited by all nodes unless overridden):

```
Go | Dart/Flutter | React | Python | medical-domain | financial-compliance | ...
```

**Layer 2 — Capability Assertion** (specific to each task node):

```
reasoning+abstraction      — architectural decisions, pattern selection, cross-cutting concerns
code-generation            — well-scoped implementation against a defined contract
verification               — checking output against spec; does not require generation capability
domain-retrieval           — medical, legal, financial knowledge grounding
integration                — semantic merge of parallel work; requires reasoning
```

**Combined routing examples:**

| Annotation | Routes To |
|---|---|
| `Go` + `reasoning+abstraction` | Mid-tier or frontier — Go idioms + architectural reasoning |
| `Dart/Flutter` + `code-generation` | Fast local model with Flutter context loaded |
| `medical-domain` + `reasoning+abstraction` | Frontier or specialized fine-tune; never a generic small local |
| `Go` + `verification` | Small local model; deterministic check against schema |
| `*` + `integration` | Mid-to-strong model; semantic conflict resolution required |

**Annotation refinement:** if a rollup fails and the failure report identifies a mis-annotation (e.g., task needed reasoning but was sent to a generation model), the annotation for the retry is updated. This is a feedback loop — annotations improve as the system runs.

---

### 4. LLM Skill: Model Router

An **LLM Skill** in the orchestration layer (not an MCP protocol-level concern). It:

1. Reads the two-layer annotation from the task graph node
2. Queries the Capability Registry for matching available nodes (model loaded, tier, queue depth)
3. Dispatches the task to the best match
4. Falls back up the tier ladder on no match: local small → mid-tier API → frontier
5. Records dispatch metadata (which node, which model, timestamp) for cost and quality tracking

The routing logic lives in the skill, not in any protocol layer. MCP may be used as a read interface to fetch task graph data — it is not the router.

---

### 5. Capability Registry

Each node running Ollama (or any inference endpoint) runs a lightweight heartbeat process that writes to a shared store (Redis or Postgres table):

```
node:
  id:             centauri-node | headscale-host | tc25 | ...
  endpoint:       http://100.64.0.X:11434
  models_loaded:  [qwen2.5-coder, mistral, llama3.2, ...]
  tier:           gpu-high | gpu-mid | cpu
  queue_depth:    current number of in-flight requests
  last_seen:      timestamp
```

The router treats nodes with `last_seen` older than a threshold as offline. Health-check semantics are required — registration alone is not sufficient.

**Open design question:** how does the registry handle a node dropping mid-task? See [What's Left to Consider](#whats-left-to-consider).

---

### 6. Event System

Git is the **state store**. A lightweight event broker (Redis Streams, webhooks, or similar) is the **notification layer**. Agents never poll — everything is reactive.

| Event | Fired When | Triggers |
|---|---|---|
| `contract.committed` | Interface contract pushed to branch | Dependent branches unblocked; dispatcher queues dependent tasks |
| `leaf.committed` | Leaf branch work committed | Verification skill runs against output |
| `verification.passed` | Automated + LLM verifier both pass | Rollup skill queued for parent branch |
| `verification.failed` | Any verifier fails | Failure report issued to leaf agent; retry queued |
| `rollup.succeeded` | Merge to parent completes cleanly | Parent branch promoted to leaf; downstream notified |
| `rollup.failed` | Merge conflict or integration check fails | Structured failure report issued; affected leaves re-opened; retry dispatched |
| `review.requested` | LLM suggestion or human insertion | Downstream work paused; review surfaced |
| `review.approved` | Human approves | Downstream work resumes |

---

### 7. Early Interface Contracts

Before any leaf work begins, the **interface contract** for that task is committed to the branch:

- Type signatures and data shapes
- API endpoint definitions (path, method, request/response schema)
- Expected output format (JSON schema, protobuf, struct definition)
- Invariants and constraints

**Why this matters:** dependents code against contracts, not implementations. An in-progress implementation on branch A can change freely — branch B, which depends on A, is stable as long as A's contract doesn't change. Contract changes are breaking events and require re-notification to all dependents.

A contract-drafting step can be automated: the task spec + ambient context → a lightweight model produces the contract as a structured file → committed before dispatch. The planner or a human reviews contracts at high-risk nodes.

---

### 8. Leaf Promotion Pattern

The core coordination mechanic. Work always happens at leaf branches. The hierarchy is a rollup ladder.

```
Leaf branches
    → do work
    → commit
    → verification passes
    → rollup attempt to parent
            │
            ├── SUCCESS
            │       merged branch becomes new leaf ✓
            │       promotion event fired
            │       next wave of work dispatched
            │
            └── FAILURE
                    leaves stay open (work is not discarded)
                    integration skill produces structured failure report:
                        - what specifically failed
                        - which leaves contributed to the conflict
                        - which contract was violated
                        - suggested fix scope
                    failure context appended to retry prompt
                    affected leaf agents re-tasked with surgical fixes
                    annotation reviewed and updated if mis-annotation identified
                    retry dispatched
                    after N failures → human review gate auto-inserted
```

**The ratchet property:** on success, the system only moves forward. Failed rollups do not lose work — they pause, diagnose, and resume with more information.

---

### 9. Integration Skill

A first-class skill, not a secondary concern.

- **Triggered by:** `verification.passed` events (rollup queued)
- **Runs:** asynchronously, in parallel — multiple rollups at different hierarchy levels can run simultaneously if they don't share a parent
- **Model tier:** mid-to-strong — semantic conflict resolution requires reasoning about intent, not just textual diff
- **On success:** fires `rollup.succeeded`, promotes branch, notifies downstream
- **On failure:** fires `rollup.failed`, produces structured failure report (see Leaf Promotion Pattern above)
- **Does not block** leaf work on unrelated branches while running

The integration skill is the only place that sees multiple branches' output simultaneously. It carries the context of what each contributing branch was trying to accomplish — not just the diff.

---

### 10. Human Review Gates

Two origins, one mechanism:

**LLM-Suggested Gates** — the integration skill recommends human review when it detects:
- High conflict density in a rollup
- Cross-cutting changes (contract changes affecting multiple branches)
- Annotation escalation (task required a stronger tier than planned)
- Repeated rollup failures at the same node (N threshold)

**Human-Requested Gates** — any stakeholder inserts a gate at any branch or rollup point at any time. No permission required. Always available.

Both gate types:
- Pause all downstream work from that point
- Surface the diff, failure report (if applicable), and task context cleanly
- Resume on explicit approval
- Are logged as part of the plan execution record

LLM suggestions are advisory and visible — humans can override in either direction.

---

## What's Left to Consider

These are known open questions. They should be resolved before prototyping begins.

### 1. Deadlock Detection
If two leaf branches are waiting on each other's contracts, neither can start. The orchestrator needs cycle detection on the task DAG at plan time, and a resolution path: either reorder contract commitments, or surface to human for restructuring.

### 2. Capability Registry — Node Failure Mid-Task
If a node drops while executing a task, the task must be re-queued to another available node. The dispatcher needs:
- Heartbeat timeout threshold
- In-flight task tracking (which task is on which node)
- Re-queue logic that avoids re-dispatching to the failed node until it recovers

### 3. Cost and Quality Feedback Loop
Track per-task: which node, which model, wall-clock time, token count, whether output passed verification on first attempt or required retry, and whether annotation was updated post-failure. This data feeds back into annotation refinement — over time, routing decisions improve based on actual outcomes, not just upfront estimates.

### 4. Retry Limit and Escalation Path
A maximum retry count per rollup must be set. After N failures: auto-insert human review gate. After human review: either restructure the subtasks, escalate to a higher model tier for the affected leaves, or manually resolve the conflict. The escalation path needs explicit design.

### 5. Flowise Integration Point
Flowise is the current orchestration surface. Its native execution model is sequential. The async dispatcher (the fan-out component) sits outside Flowise as a custom tool or service that Flowise calls. The boundary between Flowise's visual workflow and the dispatcher's parallel execution needs to be explicitly defined — specifically: what does Flowise own, and what does the dispatcher own?

### 6. Minimal Walking Skeleton
Before building the full system, a minimal prototype should validate the core loop:
- One planner producing a small task graph (3–5 nodes)
- Two nodes receiving parallel dispatch
- One rollup attempt (success and failure paths)
- Events firing and being received

This validates the event system, the dispatcher, and the rollup mechanic before investing in the full annotation/routing system.

### 7. Contract Schema Standard
The interface contract format needs a defined schema. Options to evaluate:
- JSON Schema
- OpenAPI fragment
- TypeScript types / Go interfaces committed as actual source files
- A custom structured format

The choice affects what the contract-drafting skill produces and what dependent agents read.

---

## Glossary of Terms

| Term | Definition |
|---|---|
| **Ambient Context** | Layer 1 of model annotation. The project-level technology domain (e.g., Go, Dart/Flutter, medical-domain). Inherited by all task nodes unless overridden. |
| **Capability Assertion** | Layer 2 of model annotation. The specific cognitive capability required for a task (e.g., reasoning+abstraction, code-generation, verification). Combined with ambient context to drive model routing. |
| **Capability Registry** | A shared store where each federated node advertises its available models, hardware tier, and current queue depth. Queried by the Model Router before dispatch. |
| **Contract** | See *Interface Contract*. |
| **Dispatcher** | The async service that reads the task graph, fans out independent tasks to available nodes in parallel, and collects results. Sits outside Flowise as a custom tool. |
| **Event Broker** | A lightweight pub/sub system (e.g., Redis Streams, webhooks) that carries events between components. Git is the state store; the broker is the notification layer. No polling. |
| **Failure Report** | A structured output produced by the Integration Skill when a rollup fails. Contains: what failed, which leaves contributed, which contract was violated, and suggested fix scope. Becomes a prompt addendum for the retry. |
| **Federated Node** | Any machine in the local-lab-ai-stack running an Ollama inference endpoint and registered in the Capability Registry. Examples: centauri-node, headscale-host, tc25. |
| **Integration Skill** | An LLM skill responsible for merging parallel branch work into the parent branch. Runs asynchronously, requires mid-to-strong model tier for semantic conflict resolution. |
| **Interface Contract** | Types, API shapes, schemas, and invariants committed to a branch before implementation begins. Dependents code against the contract, not the implementation. Breaking the contract is a re-notification event. |
| **Leaf Branch** | The current frontier branch where active work is happening. After a successful rollup, the merged parent branch becomes the new leaf. The concept is dynamic — it is not a fixed hierarchy level. |
| **Leaf Promotion** | The event where a successful rollup causes the merged parent branch to become the new leaf, advancing the stable frontier of the work. |
| **LLM Skill** | A composable capability unit in the orchestration layer that an LLM can invoke. Distinct from MCP (which is a context/data protocol). The Model Router is implemented as an LLM Skill. |
| **Model Annotation** | A two-layer tag on each task node specifying what kind of model is needed: ambient context (Layer 1) + capability assertion (Layer 2). |
| **Model Router** | The LLM Skill responsible for reading task annotations, querying the Capability Registry, and dispatching tasks to the best available node/model. |
| **Ollama** | Local LLM inference server running on federated nodes. Exposes a standard REST API. The primary inference target for small and mid-tier model execution. |
| **Plan ID** | A unique identifier for the task graph, matching the assignment ID from `governance/workflows/`. Used as the root prefix in branch names. |
| **Rollup** | A merge attempt from one or more leaf branches into their parent branch. Managed by the Integration Skill. Successful rollup = leaf promotion. Failed rollup = failure report + retry. |
| **Rollup Ladder** | The branch hierarchy as a rollup target chain. Work progresses from leaves toward root through successive rollups. |
| **Speculative Execution** | An optional pattern where the same task is dispatched to two nodes simultaneously; the first result to pass verification is accepted, the other cancelled. Trades compute for latency and quality insurance. |
| **Task Graph** | The DAG produced by the planner at the start of an assignment. Each node is a task with annotation, contract, dependencies, and branch name. The source of truth for the dispatcher. |
| **Verification** | A two-stage check applied to each leaf output: (1) automated — lint, type-check, compile, test; (2) LLM verifier — does output satisfy the task spec and output contract? Both must pass before rollup is queued. |

---

## Context and References

### Session Context

This architecture was developed in a single design session on 2026-06-22/23. It has not yet been prototyped. All components are at design stage.

**What was agreed:**
- Git as state store + event broker for notifications (not polling)
- Two-layer model annotation as the routing signal
- Model routing as an LLM Skill (not MCP)
- Early interface contracts as the primary mechanism for unblocking parallel work
- Leaf promotion pattern as the core rollup mechanic
- Integration as a first-class async parallel skill
- Human review gates: both LLM-suggested and human-insertable

**What was revised mid-session:**
- "Needs a code model" annotations were identified as too coarse → replaced with two-layer (ambient + capability assertion) system
- MCP as router was pushed back → repositioned as a read interface only; routing moved to LLM Skill
- Proactive dependency monitoring (reading sibling branch implementations) was flagged as a dirty-read risk → replaced with early interface contracts as the coordination mechanism

### Stack Context

| Component | Role |
|---|---|
| Flowise | Human-facing workflow designer; visual orchestration surface |
| Ollama | Local inference on federated nodes |
| Headscale | Tailnet connecting all federated nodes (photondatum.space) |
| Redis (proposed) | Capability Registry store + Event Broker |
| Git | State store for all task outputs, contracts, and plan artifacts |

**Federated nodes (local-lab-ai-stack):**
- `centauri-node` — 100.64.0.4 (primary AI stack node; runs Open WebUI, Traefik)
- `headscale-host` — 100.64.0.5 (on photondatum.space)
- `tc25` — 100.64.0.3 (this workstation; runs Ollama, Promtail, GitHub Actions runner)
- `sol` — 100.64.0.2 (offline as of 2026-06-22)

### Recommended Next Steps for a New Context

1. **Read this document in full** before proposing any implementation.
2. **Resolve open questions** in [What's Left to Consider](#whats-left-to-consider) — particularly deadlock detection, registry failure handling, and the Flowise integration boundary — before writing any code.
3. **Design the minimal walking skeleton** (see item 6 in What's Left to Consider) as the first implementation milestone. Validate the event system, dispatcher, and rollup mechanic with a 3–5 node task graph across two real nodes before building routing or annotation systems.
4. **Define the Interface Contract schema** (item 7) early — everything downstream depends on it.
5. **Do not conflate MCP with routing.** MCP reads plan data. The LLM Skill routes. These are separate concerns.

### Related Framework Files

- `governance/workflows/assignment-workflow.md` — assignment lifecycle this architecture extends
- `governance/agent-roles-guide.md` — work mode and role definitions relevant to planner/integrator/verifier roles
- `glossary-of-terms.md` — root glossary; terms here should be proposed for addition if they graduate from design to framework standard
- `docs/features.md` — framework directory map
