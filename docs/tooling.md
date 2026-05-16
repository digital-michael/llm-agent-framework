# Tooling and Skills

> This document defines the **contracts** for tools and skills that extend the LLM Agent Collaboration Framework.
> Implementations live in the companion tooling repository: `llm-agent-framework-tools`.

---

## Option C: Interface Here, Implementation Separate

The infrastructure repo stays tooling-agnostic. It defines **what** each tool or skill must do — the contract. The tooling repo provides implementations for specific platforms (MCP, VS Code Copilot skills, shell scripts). Teams may provide their own implementations as long as they satisfy the contract.

```
llm-agent-framework/          ← contract definitions (here)
llm-agent-framework-tools/    ← platform-specific implementations
```

---

## Tool and Skill Catalog

### `init-profile`

**Purpose:** Scaffold a new personal or team profile repo from infrastructure templates.

**Contract:**
- Accept: profile type (`personal` | `team`), target path, infrastructure path
- Action: copy the appropriate `templates/<type>/` directory to the target path as plain files (no git relationship to infrastructure)
- Action: optionally run `git init` at the target path
- Action: write or prompt for `.llm-framework.yml` at the specified project path
- Output: confirm created files, next steps

**When to use:** First-time setup of a personal or team profile repo.

---

### `upgrade-template`

**Purpose:** Detect and apply updates when an infrastructure template version is newer than a profile's filled-out copy.

**Contract:**
- Accept: path to a filled-out profile file, path to the current infrastructure template for that file
- Compare: `template-version` fields
- If same version: report no action needed
- If infrastructure is newer: present a diff of structural changes (new sections, renamed fields, removed prompts); propose which changes to apply; apply only what the user confirms
- Never overwrite user-supplied content without confirmation
- Output: updated file with new `template-version` stamp

**When to use:** After infrastructure templates are updated; when the agent warns of a version mismatch at session start.

---

### `scaffold-project`

**Purpose:** Set up `.llm-framework.yml` for a new or existing project repository.

**Contract:**
- Accept: project path, infrastructure path, optional team path, optional personal path
- Action: write `.llm-framework.yml` to the project root with the provided paths
- Validate: confirm each declared path exists before writing
- Output: written config file, summary of operating mode, next steps

**When to use:** When starting a new project and connecting it to the framework.

---

### `context-hydration` *(agent-side, not user-invoked)*

**Purpose:** At session start, read `.llm-framework.yml` and resolve the framework layer stack for the agent.

**Contract:**
- Read: `.llm-framework.yml` from the project root (or a declared path)
- Resolve: which layers are present (infrastructure, team, personal)
- Determine: operating mode (Standalone / Team / Full)
- For each governance file the agent needs: resolve the correct version using override order (personal > team > infrastructure), respecting `override: replacement` vs `override: extend` semantics
- Output: resolved layer map available to the agent for the session

**When to use:** Automatically, at every session start. Part of the Locked-In protocol defined in `governance/agent-context-protocol.md`.

---

## Platform Implementations

Implementations of the above contracts are provided in `llm-agent-framework-tools/`:

| Platform | Directory | Notes |
|---|---|---|
| MCP (Model Context Protocol) | `mcp/tools/` | JSON schema + implementation |
| VS Code Copilot Skills | `skills/` | SKILL.md format |
| Shell scripts (platform-agnostic) | `scripts/` | Bash fallback for any platform |

Any implementation that satisfies the contract above is valid. Teams may substitute their own.

---

## Adding New Tools

When a new tool or skill is identified:

1. Define the contract here in `docs/tooling.md` (name, purpose, inputs, outputs, contract)
2. Implement in `llm-agent-framework-tools/` under the appropriate platform directory
3. Reference the new tool in the relevant workflow file under `governance/workflows/` if it is part of the assignment lifecycle

Contracts are versioned alongside the rest of the infrastructure. Breaking contract changes increment `template-version`.
