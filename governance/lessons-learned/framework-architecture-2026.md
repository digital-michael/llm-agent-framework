# Lessons Learned — Framework Architecture (2026)

> Gaps and corrections discovered through active use of the framework across multiple projects.
> Format: `[Date] [Context] Lesson. → Action required.`

---

## LLM Agent

- [2026-06-23] [RTK + local-lab-ai-stack] Agents have no way to discover the load order from first principles. Without a framework entry point, each domain repo README must hardcode the full absolute-path load sequence. When paths change or new layers are added, every domain repo README breaks silently. → A framework agent entry point is needed (see Technologist section below).

---

## Technologist

- [2026-06-23] [Framework architecture] **`.llm-framework.yml` is declared but never read at runtime.** The file correctly declares `infrastructure:` and `team:` paths, but no framework document tells an agent to read it first and derive the load cascade from it. `agent-context-protocol.md` defines the Locked-In protocol but does not reference `.llm-framework.yml` at all. The result: the framework defines the layer concept (infrastructure → domain → project) but does not provide a working entry point to execute it. The actual working mechanism is the domain repo README hardcoding a full 10-step absolute-path load order — which is manual composition disguised as a protocol.

  **What is missing:** A framework entry point — a single file an agent can load that says: "read `.llm-framework.yml`; from `infrastructure:`, load the framework base rules in order; from `team:`, load the domain overlay chain; the domain repo README tells you what repo-specific things come next." Until this exists, every domain repo README is load-order documentation doing the job the framework should do.

  → **Action required:** Create `llm-agent-framework/governance/agent-entry-point.md` that gives agents a concrete, executable cascade:
  1. Read `.llm-framework.yml` in the project root
  2. Load `<infrastructure>/governance/system-prompts/base-system-prompt.md`
  3. Load `<infrastructure>/library/<lang>/governance-overlay.md` for each language in the project
  4. Load `<team>/governance-overlay.md`
  5. Load `<team>/library/<lang>/governance-overlay.md` for each language
  6. Load `<team>/<repo-name>/README.md` (the domain repo context — this file specifies what repo-specific files to load next)

  Until this is created, domain repo READMEs must continue to enumerate the full load order. This is a known workaround, not the intended design.

---

## Candidates for Promotion to Governance

- The agent entry point gap (above) is blocking clean integration for every project. It should be the highest-priority framework addition — without it, the retrofit workflow's "point to the framework entry point" instruction cannot be implemented, and every domain repo README is load-order documentation rather than repo context.
