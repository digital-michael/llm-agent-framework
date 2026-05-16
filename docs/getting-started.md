# Getting Started

> This guide walks you through adopting the LLM Agent Collaboration Framework for a new or existing project.

---

## Prerequisites

- Git installed
- A project repository (existing or new)
- An LLM Agent tool (VS Code Copilot, Cursor, Claude, or similar)

---

## Step 1: Choose Your Operating Mode

The framework supports three operating modes. Start with the one that fits your current situation and upgrade as your usage matures.

| Mode | Repos Involved | When to Use |
|---|---|---|
| **Standalone** | Infrastructure only (this repo) | Solo developer, single project, getting started |
| **Team** | Infrastructure + team/org profile | Team sharing governance and context |
| **Full** | Infrastructure + team + personal | Individual contributor with personal working preferences |

You can start in Standalone mode and add layers later. The framework degrades gracefully — each layer is optional except infrastructure.

---

## Step 2: Set Up Your Directory Layout

Recommended layout (framework repos live outside your project repos):

```
~/Projects/
├── infrastructure/          ← this repo (or a sparse checkout)
├── domains/
│   └── <your-team>/         ← team profile (Team mode and above)
├── personal/
│   └── <your-name>/         ← personal profile (Full mode)
└── src/
    └── <your-project>/      ← your assignment repo
        └── .llm-framework.yml
```

### Standalone mode (simplest start)

Clone or download this repository:

```bash
git clone https://github.com/<org>/agent-collaboration-framework ~/Projects/infrastructure
```

### Scaffold your first project

In your project repository, create `.llm-framework.yml`:

```yaml
infrastructure: ~/Projects/infrastructure
# team: ~/Projects/domains/my-team      # uncomment when ready
# personal: ~/Projects/personal/jane    # uncomment when ready
```

---

## Step 3: Configure Your System Prompt

1. Open `governance/system-prompts/base-system-prompt.md`
2. Fill in the `[TEAM-SPECIFIC ADDITIONS]` section with your team's rules
3. Paste the completed system prompt into your LLM Agent's configuration

For language-specific additions, find your language in `library/<lang>/instructions.md` and append it to the base prompt.

---

## Step 4: Start Your First Assignment

1. Copy `templates/agent-assignment.md` into your project directory
2. Fill in: goals, constraints, out-of-scope items
3. Follow the assignment workflow: `governance/workflows/assignment-workflow.md`
4. At close: capture lessons learned using `governance/lessons-learned/template.md`

---

## Step 5: Build the Habit

After each assignment:
- Update your lessons-learned file
- Note any decisions made
- Review whether any lessons should update the governance layer

The framework improves through use. Governance that isn't updated doesn't govern.

---

## Next Steps

- [Enterprise and Team Setup](enterprise-setup.md) — adding the team layer
- [Features Overview](features.md) — what's in each directory and why
