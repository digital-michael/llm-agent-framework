# Lessons Learned — Retrofit Project Workflow (2026)

> Accumulated from three retrofit applications: RTK (2026-06-10), local-lab-ai-stack (2026-06-22), RTK second pass (2026-06-23).
> These lessons directly shaped the workflow at `governance/workflows/retrofit-existing-project.md`.
> Format: `[Date] [Context] Lesson. → Action taken.`

---

## LLM Agent

- [2026-06-10] [RTK retrofit] Agent wrote project-level governance files before verifying the domain profile existed, and placed repo-context content inside `docs/governance/` instead of the domain profile. → Step 2 is now a hard block; `docs/governance/` holds exactly four files; all repo-context and agent behavior rules go in the domain profile.
- [2026-06-22] [local-lab-ai-stack retrofit] Agent did not audit existing agent governance files for framework conflicts before writing new governance files — produced two competing governance systems. → Step 1 now requires a section-by-section conflict map for any pre-existing agent guidelines file before any new governance content is written.
- [2026-06-22] [local-lab-ai-stack retrofit] Agent referenced files in governance docs that did not exist on disk; no scan was done to verify. → Step 1 now includes a broken-reference scan; Step 6 now includes a verification checklist for all paths in the domain repo context file.
- [2026-06-23] [RTK second pass] Agent left pre-existing governance file sections that duplicated domain overlay content with no stated tiebreaker — agents reading both had no way to resolve conflicts. → Partially-superseded sections must have a visible "⚠ Partially Superseded" header at the section level in the file itself, pointing to the authoritative overlay.

---

## Technologist

- [2026-06-23] [RTK layer classification] Pre-framework projects accumulate generic content (SOLID guides, architecture blueprints, god object guides) in their `docs/` trees because the domain/framework layers did not exist yet. This content is never reclassified because no retrofit step required it. → A Layer Classification step (Step 1b) was added to the workflow: classify every project doc as project-specific / domain-level / framework-level and produce an action plan before doing anything else.
- [2026-06-23] [RTK layer classification] The "framework governs how work runs" principle was present in the workflow but not dominant enough to function as the primary filter. Retrofits that completed all steps still left over-scoped project docs intact. → This principle is now the first thing stated in the workflow (Core Principle section), before the flowchart and steps.
- [2026-06-23] [RTK layer classification] The Layer Classification step (Step 1b) defines three buckets for *content* (project-specific / domain-level / framework-level) but has no bucket for *work coordination artifacts* — planning files, completed work logs, refactoring notes, feature drafts, in-progress work (`docs/planning/`, `docs/completed/`, `docs/work/`, `docs/refactoring/`, `docs/features/`, etc.). These directories were present in RTK and not classified. Planning artifacts are not project-specific in the content sense (they don't describe what the project is); they describe what work is being done. The `meta/<repo-name>/` pattern (invented during local-lab-ai-stack retrofit for session state files) may be the right home for these, or they may warrant a fourth classification bucket. → Step 1b needs a "work coordination artifacts" category and an explicit disposition for planning/work/refactoring directories. Currently unresolved — needs a decision on home location before the next retrofit where these exist.
- [2026-06-22] [local-lab-ai-stack retrofit] Projects with existing doc directories added after the initial retrofit (e.g., `docs/guides/`) will not appear in the domain profile placement table unless that table is re-verified. → Step 6 verification checklist now requires confirming all doc placement locations exist on disk.
- [2026-06-22] [local-lab-ai-stack retrofit] Git-tracked meta files resist .gitignore migration. Session-state files previously committed to git are still tracked even after being added to .gitignore. → During meta migration, run `git rm --cached <file>` for each historically-tracked file; verify with `git ls-files <path>` before assuming a file is untracked.
- [2026-06-22] [local-lab-ai-stack retrofit] The README-agent.md within-repo scoping convention and the framework's cross-repo layer resolution operate at different scopes and are compatible — but agents treat them as competing systems unless this is stated explicitly in the domain repo context file. → Domain repo context file should clarify that README-agent.md governs directory authority within the repo while the framework governs cross-repo load order.
- [2026-06-22] [local-lab-ai-stack retrofit] Project enforcement rules (credentials policy, script structure standards, config SSOT rules) have no standard home in the framework's four `docs/governance/` files. Without an explicit placement decision, these rules become homeless and risk being lost during retrofit. → Decide before Step 4: inline in `docs/governance/README.md` or create `docs/governance/project-rules.md`.
- [2026-06-22] [local-lab-ai-stack retrofit] Moving files to `./tmp` (tracked in git) provides a safe staging area for content under review during a retrofit. Preferable to deleting content that has not yet been re-homed. → Use `./tmp` as a staging area; restore the gitignore entry when the retrofit is complete.
- [2026-06-10] [RTK retrofit] For existing architecture docs, add an addendum rather than rewriting. The addendum (Current-State Package Map) is the agent-facing artifact; the full doc is human reference. → Step 5b documents this pattern.
- [2026-06-10] [RTK retrofit] External reusable components (sibling repos, shared libraries) must be explicitly listed in both the architecture addendum and the domain repo context. Agents will re-implement rather than discover existing components without this. → Required in both Step 5b and Step 6.
- [2026-06-10] [RTK retrofit] Composition rules ("all dialogs must use DialogManager") must include explicit correct/wrong examples, not just a rule statement. → Examples are required in Step 5b output.

## Tech Stack

- [2026-06-22] [local-lab-ai-stack retrofit] A `meta/<repo-name>/` directory at the domain repo root — for session state files (review logs, dynamics, agent-context) that are project-specific but not tracked in the project repo — was invented during this retrofit. → If this pattern holds across multiple projects, it should be added to the domain template. Currently unvalidated beyond one project.

---

## Candidates for Promotion to Governance

- The "framework governs how work runs" principle has been validated across two retrofits (local-lab-ai-stack and RTK second pass) and is now the Core Principle of the workflow. Candidate for inclusion in the base system prompt's project onboarding section if it recurs in a third context.
- The Layer Classification step (Step 1b) is new as of 2026-06-23 and unvalidated beyond RTK. Revisit after the next retrofit to confirm bucket definitions are clear and the output format is useful.

---

*Related: framework entry point gap (why retrofitted projects still enumerate load order manually) — [`framework-architecture-2026.md`](./framework-architecture-2026.md)*
