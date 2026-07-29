# LLM Agent Collaboration Framework — Infrastructure

> **Status:** Reference infrastructure. Use as-is, or use the templates to build your team and personal profiles.
> **Purpose:** The governance, workflow, and standards layer for disciplined, repeatable LLM Agent collaboration.

---

## What This Is

This repository is the **infrastructure layer** of the LLM Agent Collaboration Framework — a portable, language-agnostic governance system for teams and individuals working with LLM Agents in software development.

It is not a runnable project. It is the stable foundation that your project repos reference.

---

## Operating Modes

The framework supports three modes based on which repos are present:

| Mode | Repos Active | When to Use |
|---|---|---|
| **Standalone** | Infrastructure only | Solo developer, single project, getting started |
| **Team** | Infrastructure + team profile | Team sharing governance and learned context |
| **Full** | Infrastructure + team + personal | Individual contributor with personal working preferences |

Each project declares its mode via `.llm-framework.yml` at the project root.

---

## Directory Structure

```
.
├── README.md                          # This file
│
├── docs/                              # Human-facing documentation
│   ├── getting-started.md             # How to adopt the framework
│   ├── enterprise-setup.md            # Team and personal layer setup
│   └── features.md                    # Directory map and feature overview
│
├── governance/                        # Agent-facing: protocols, standards, workflows
│   ├── agent-context-protocol.md      # Context loading protocol (Locked-In, profiles)
│   ├── collaboration-directives.md    # Autonomy levels, reinforcement loop
│   ├── collaboration-patterns.md      # Default collaboration strengths/weaknesses
│   ├── agent-roles-guide.md           # Work modes, role matrix
│   ├── engineering-principles.md      # SOLID, DRY, GRASP, architectural standards
│   ├── testing-standards.md           # Testing by environment, coverage targets
│   ├── system-prompts/                # Base system prompt template
│   ├── workflows/                     # Assignment lifecycle and iterative process
│   ├── lessons-learned/               # Lessons template and structure
│   └── assignments/                   # Assignment template
│
├── library/                           # Language-specific governance overlays
│   ├── bash/
│   ├── go/
│   ├── java/
│   ├── javascript/
│   ├── node/
│   ├── python/
│   └── rust/
│
└── templates/                         # Starting points for profiles and assignments
    ├── .llm-framework.yml             # Project config template
    ├── agent-assignment.md            # Assignment spec template
    ├── session-context.md             # Cross-session handoff template
    ├── personal/                      # Personal profile skeleton
    └── team/                          # Team profile skeleton
```

---

## Quick Start

See [docs/getting-started.md](docs/getting-started.md) for a complete walkthrough.

**In short:**

1. Clone this repo to `~/Projects/infrastructure`
2. Copy `templates/.llm-framework.yml` to your project root and set the `infrastructure` path
3. Fill in `governance/system-prompts/base-system-prompt.md` and activate your language overlays
4. Run your first assignment using `templates/agent-assignment.md`

---

## Override System

Team and personal profiles override infrastructure by placing a file at the matching path in their profile repo. The `override` field in each file's front matter controls behavior:

- `override: replacement` — entire file replaces the infrastructure version
- `override: extend` — named/numbered items in this file replace matching items only; the rest remains in effect

Resolution order: **personal > team > infrastructure**

---

## Separation from Project Repositories

Framework repos live **outside** your project repos — one set of framework repos serves all your projects.

```
~/Projects/infrastructure/          ← this repo (shared across all projects)
~/Projects/domains/my-team/         ← team profile (Team mode and above)
~/Projects/personal/jane/           ← personal profile (Full mode, private)
~/Projects/src/my-project/          ← your project
    └── .llm-framework.yml          ← only coupling point
```

---

## Tooling and Skills

Optional tools and skills that automate common setup tasks (profile scaffolding, template upgrades, project config) are provided in the companion tooling repository:

**`llm-agent-framework-tools/`** — MCP tools, VS Code Copilot skills, and shell script fallbacks.

The tool contracts (what each tool must do) are defined in [docs/tooling.md](docs/tooling.md). The tooling repo provides platform-specific implementations. You can use any implementation — or none; all tool operations can be performed manually.

---

## Related Reading

- [Introduction to Using LLM Agents: From Concepts to Actions](../llm-agent-articles/article-overview.md)
- [Software Solutions with LLM Agents: Governance and Feedback with Success](../llm-agent-articles/article-technical.md)

