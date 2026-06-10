---
template-version: 1.0.0
override: replacement
---

# Resource Lifecycle Contract (RLC)

> A framework-level consideration lens for any resource across all domains: code, operations, infrastructure, process, and external systems.
> Augments — does not replace — SOLID, GRASP, DRY, IoC, DI, and other established practices.

---

## What RLC Is

The Resource Lifecycle Contract is a **thinking discipline**, not a document requirement. It asks one question before, during, and after any implementation:

> **Have you thought through the full life of this resource — from before it exists to after it's gone?**

"Resource" is intentionally broad:

- In-memory object (struct, class, record)
- I/O handle (file, socket, connection, stream)
- Networked resource (database pool, gRPC channel, message broker, API client)
- Concurrency primitive (goroutine group, thread pool, actor, task queue)
- External service dependency (third-party API, cloud resource, SaaS integration)
- Operational or process resource (a human approval step, a deployment gate, a vendor onboarding step, a manual configuration action)
- Infrastructure resource (container, OS handle, shared memory region, secret store entry)

A "resource" is anything that has a life — something that must be obtained, used, and eventually released, by someone.

---

## The Six Phases — A Thinking Prompt, Not a Checklist

No phase is mandatory. The discipline is in asking whether each phase applies and who owns it. A phase that genuinely doesn't apply requires no notation. The value is in having considered it.

| Phase | Question to ask |
|---|---|
| **Allocation** | How and where is this resource obtained or created? Who is responsible? |
| **Configuration** | How is its behavior parameterized before first use? Is configuration complete before activation? |
| **Activation** | How does it become ready for use? Is activation a separate act from allocation, or are they the same? Who triggers it? |
| **Primary Use** | What is the contracted usage pattern? What invariants must hold during use? Who may use it, and how? |
| **Deactivation** | How is the resource gracefully wound down before release? Is deactivation required before deallocation? |
| **Deallocation** | How is the resource returned, freed, or finalized? Is this paired with allocation? Who is responsible? |

**Key instinct:** Define allocation and deallocation together. Implement within that bounded scope, then verify correctness before expanding outward to usage.

---

## Relationship to Existing Principles

RLC augments — it does not replace — SOLID, GRASP, DRY, IoC, DI, and language-specific idioms. It surfaces lifecycle-specific violations of those principles at the point where they are most likely to be missed.

| Principle | How RLC surfaces violations |
|---|---|
| **SRP** | If two scopes both claim ownership of a lifecycle phase, one has too many responsibilities |
| **DIP / IoC** | The composition root owns allocation/deallocation; consumers own usage only — against an interface, not a concrete lifecycle type |
| **ISP** | A consumer that also controls allocation or deallocation is depending on lifecycle concerns it shouldn't own |
| **Creator (GRASP)** | The RLC names who allocates; if it isn't the aggregator or closely-coupled type, that's a design signal |
| **Low Coupling (GRASP)** | A usage-phase scope that imports allocation details or concrete lifecycle types is a coupling signal |
| **Controller (GRASP)** | Lifecycle orchestration (when to start, when to stop) belongs in the controller layer, not in domain or service objects |

---

## Parent-Child Ownership

Ownership flows in one direction: parent-to-child. A parent scope may own allocation and deallocation while a child scope owns usage only.

Rules:

1. **No phase may be unowned.** Every applicable phase has exactly one owner.
2. **No phase may be owned by two scopes simultaneously.** If two scopes claim the same phase, that is a conflict — flag it.
3. **Ownership flows parent-to-child.** If a child allocates something the parent should own, that is a violation.
4. **Cycles are prohibited.** A scope cannot depend on a lifecycle phase owned by a scope that depends on it.

When a child scope relies on a phase owned by a parent, no additional notation is required unless an exception exists (shared ownership, ownership transfer, or a deliberate deviation). Exceptions are noted once, at the point of deviation — not everywhere the pattern holds normally.

---

## Verification Gradient

Verification scales with size, priority, and criticality. The agent uses judgment — no fixed thresholds.

| Scale | Agent action | Human involvement |
|---|---|---|
| **Small** | Silent adherence check during code review. Flag gaps with a question. No artifact produced. | None required |
| **Medium** | Structured review noting which phases were addressed and which were not. Flags gaps explicitly. | Notified; not required to act unless a gap is significant |
| **Large / Critical** | Explicit lifecycle review. Testing requirements tied to lifecycle phases. Agent code review against RLC principles. | Reviews and confirms; signs off on testing completeness |

**Testing by phase:**

- Unit tests verify individual phases in isolation (e.g., configuration is applied correctly, activation succeeds under normal conditions)
- Integration tests verify paired phases (allocation↔deallocation, activation↔deactivation behave correctly together)
- Agent review verifies that all applicable phases were considered and correctly implemented
- Human review is the final gate for large or critical work

**Operational phases:**

Where a lifecycle phase is owned by a human or an external process, the agent verifies:

1. The phase is named and an owner is assigned
2. An observability signal exists — a log entry, ticket state, flag, or other inspectable artifact — that stands in for direct execution verification

The agent cannot verify that a human-owned phase was executed. It can verify that the signal was designed and is in place.

---

## When RLC Applies

Everything is considered. Not everything requires documentation.

- The agent scans all work through the RLC lens
- If all phases are clearly addressed, nothing is produced
- If a phase is missing, unowned, or ambiguous, the agent flags it — proportionally to scale and criticality
- Documentation of the RLC for a specific resource is produced only when the scale or criticality of the work warrants handing that thinking to another person or session

---

## Language Instantiations

Each language overlay at `library/<lang>/governance-overlay.md` maps the six phases to idiomatic patterns and common failure modes for that language.
