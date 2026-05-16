# Templates

> Starting points for your profiles and per-assignment artifacts.

---

## What's Here

| File / Directory | Purpose |
|---|---|
| `.llm-framework.yml` | Config template — tells the agent where your repos live |
| `agent-assignment.md` | Per-assignment spec, tracking, and quality gates |
| `session-context.md` | Cross-session handoff and context preservation |
| `personal/` | Personal profile skeleton — copy to your private personal repo and fill in |
| `team/` | Team profile skeleton — copy to your team repo and fill in |

---

## How Templates Work

Templates are starting points. You copy them to the appropriate location and fill them in. They are never modified in-place in the infrastructure repo.

- **`.llm-framework.yml`** → copy to each project root (`~/Projects/src/<project>/`)
- **`agent-assignment.md`** → copy to each project for each assignment
- **`session-context.md`** → copy to each project, update as sessions progress
- **`personal/`** → copy the directory contents to your personal profile repo
- **`team/`** → copy the directory contents to your team profile repo

---

## Template Versioning

Each template file carries a `template-version` stamp in its front matter. When infrastructure templates are updated, your filled-out copies may be on an older version. The agent will flag version mismatches when loading context.

Upgrading is a manual process: review what changed and decide what to adopt. A future upgrade skill will assist with this.
