# Repo Context — `<repo-name>`

> Per-repo session-start context for the `<repo-name>` project.
> Copy this file to `<domain-profile>/<repo-name>/README.md` and fill in the sections.
> Load this after the domain governance overlay and before any assignment.

---

## Project Identity

**`<repo-name>`** — *[one sentence: what is this project?]*
- Language: *[e.g., Go]*
- Frameworks: *[e.g., Fyne, gRPC]*
- File format(s): *[if applicable]*
- Current version: *[if applicable]*

---

## Session Start Load Order

1. Infrastructure base system prompt: `<infrastructure-path>/governance/system-prompts/base-system-prompt.md`
2. **If `personal:` is set in this project's `.llm-framework.yml`:** personal profile (`<personal-path>/{context-protocol,collaboration-preferences,collaboration-patterns,roles-guide}.md`) — see `agent-context-protocol.md` §Personal Profile Resolution. Loaded every session regardless of task profile; skip entirely if `personal:` is absent.
3. Infrastructure language overlay: `<infrastructure-path>/library/<lang>/governance-overlay.md`
4. Domain governance: `<domain-path>/governance-overlay.md`
5. Domain language overlay: `<domain-path>/library/<lang>/governance-overlay.md`
6. **This file**
7. Project lessons-learned: *[list paths to all lessons files in the project repo]*
8. Active assignment (if resuming): `docs/governance/agent-assignment.md`
9. Session context (if resuming): `docs/governance/session-context.md`

---

## Documentation Placement Rules

*If the project has a strict doc structure, describe it here. Otherwise remove this section.*

| Work type | Location |
|---|---|
| | |

❌ *[List prohibited locations]*

---

## Git Workflow

*Project-specific git constraints beyond the infrastructure guardrails.*

- Work in the current feature branch; do not merge without explicit authorization
- `git push` requires explicit per-session permission (infrastructure guardrail)
- *[Add any repo-specific rules]*
