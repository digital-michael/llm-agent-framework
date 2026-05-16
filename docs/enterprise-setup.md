# Enterprise and Team Setup

> How to add the team and personal layers to the framework.

---

## Overview

The framework uses a layered model. Each layer is optional and adds specificity:

```
personal > team/org > infrastructure
```

More specific always wins. Infrastructure provides the defaults. Team overrides what the team has agreed to change. Personal overrides what the individual prefers. Layers not present are simply skipped.

---

## The Team / Organization Layer

The team layer is a profile repo that lives outside any individual project. It is shared by all team members and all team projects.

### What goes in the team profile

| Content | Purpose |
|---|---|
| `governance/collaboration-patterns.md` | Team's validated working patterns (overrides infrastructure default) |
| `governance/system-prompts/team-additions.md` | Team-specific rules appended to the base system prompt |
| `governance/lessons-learned/` | Team-level lessons accumulated over time |
| `library/<lang>/governance-overlay.md` | Team's language-specific extensions |

### Setting up the team profile

1. Use the team template: `templates/team/`
2. Create a new repository (recommend: private)
3. Initialize from the team templates:
   ```bash
   cp -r ~/Projects/infrastructure/templates/team ~/Projects/domains/my-team
   cd ~/Projects/domains/my-team && git init
   ```
4. Fill in the templates (see `templates/team/README.md`)
5. Reference it from each project's `.llm-framework.yml`

### .llm-framework.yml with team layer

```yaml
infrastructure: ~/Projects/infrastructure
team: ~/Projects/domains/my-team
```

---

## The Personal Layer

The personal layer is a private profile repo per individual. It captures working style, interaction preferences, and personal collaboration patterns. It is never shared with the team repo and never flows back to infrastructure.

### What goes in the personal profile

| Content | Purpose |
|---|---|
| `context-protocol.md` | Personal defaults: which profile to load, session preferences |
| `collaboration-preferences.md` | Autonomy level defaults, triggers, what you want from an LLM |
| `collaboration-patterns.md` | Your personal strengths and failure modes as a collaborator |
| `roles-guide.md` | How you prefer the agent to adapt to your working style |

### Setting up the personal profile

1. Use the personal template: `templates/personal/`
2. Create a new **private** repository — this repo should never be public
3. Initialize from the personal templates:
   ```bash
   cp -r ~/Projects/infrastructure/templates/personal ~/Projects/personal/jane
   cd ~/Projects/personal/jane && git init
   ```
4. Fill in the templates honestly — this data improves your sessions directly
5. Reference it from each project's `.llm-framework.yml`

### .llm-framework.yml with all layers

```yaml
infrastructure: ~/Projects/infrastructure
team: ~/Projects/domains/my-team
personal: ~/Projects/personal/jane
```

---

## Override Mechanics

When the agent loads context, it resolves files in priority order: personal > team > infrastructure.

Each governance file declares its override behavior in its front matter:

```yaml
---
template-version: 1.0.0
override: replacement   # entire file replaces the infrastructure version
---
```

```yaml
---
template-version: 1.0.0
override: extend        # named/numbered rules in this file replace matching rules only
---
```

**Extend semantics:** An `extend` file only replaces rules it explicitly names or numbers. Unnamed rules from the infrastructure version remain in effect. Use `extend` when you want surgical changes to a few items without duplicating the entire file.

**Default:** `replacement` when no `override` field is present.

---

## Template Versioning

Each template file carries a `template-version` stamp. When infrastructure templates are updated, your team and personal profile files may be on an older version.

The agent will warn when a profile file's `template-version` is older than the current infrastructure version. Updating is a manual process: review the diff between versions and decide which changes to adopt.

A future upgrade skill will assist with this process.

---

## Division / Enterprise Structure

For large organizations with multiple teams under a shared division:

```
~/Projects/
├── infrastructure/
├── domains/
│   ├── division/          ← division-level overrides (optional)
│   └── team-a/            ← team-specific overrides
└── personal/
    └── jane/
```

`.llm-framework.yml` supports multiple team layers resolved in order. See the template for the full field reference.

> **Note:** Enterprise division structures are a recognized extension point. The framework does not prescribe a specific hierarchy — adapt the layering to match your organization's actual governance structure.
