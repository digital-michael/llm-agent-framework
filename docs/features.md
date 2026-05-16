# Features

> What's in this repository and why each piece exists.

---

## Directory Map

```
infrastructure-repo/
├── README.md
├── docs/                    ← you are here (human-facing documentation)
├── governance/              ← agent-facing: protocol definitions and standards
├── library/                 ← agent-facing: language-specific overlays
└── templates/               ← starting points for your profiles and assignments
```

---

## `governance/` — Protocol and Standards

Language-agnostic rules and protocols. Every project using this framework works from these defaults.

| File / Directory | What It Contains |
|---|---|
| `agent-context-protocol.md` | How the agent loads and declares its context at session start; Locked-In protocol; profile system |
| `collaboration-directives.md` | Autonomy levels (0–4), the reinforcement feedback loop, lateral thinking scope |
| `agent-roles-guide.md` | Work modes (Discover / Develop / Deliver), agent roles (Lead / Partner / Support), mode-role matrix |
| `collaboration-patterns.md` | Default collaboration strengths and weaknesses; overridable by team and personal profiles |
| `engineering-principles.md` | SOLID, DRY, GRASP, and architectural standards applied to LLM-generated code |
| `testing-standards.md` | Environment-stratified testing model (local / staging / production), coverage targets |
| `system-prompts/` | Base system prompt template and composition guide |
| `workflows/` | Assignment lifecycle (end-to-end) and iterative implementation loop (step/phase model) |
| `lessons-learned/` | Template and structure for accumulating lessons from assignments |
| `assignments/` | Governance-layer assignment template |

---

## `library/` — Language Overlays

One directory per language. Each contains:

| File | Purpose |
|---|---|
| `README.md` | Key concerns for this language when working with LLM Agents |
| `governance-overlay.md` | Language-specific pitfalls, standards, lessons learned |
| `instructions.md` | Append to the base system prompt for this language |

**Languages included:** Java, Go, JavaScript, Node.js, Bash, Python, Rust

---

## `templates/` — Starting Points

Templates for your profiles and per-assignment artifacts.

| File / Directory | What It Is |
|---|---|
| `.llm-framework.yml` | Config template — declares where your infrastructure, team, and personal repos live |
| `agent-assignment.md` | Full assignment spec template (goals, constraints, plan, quality gates, lessons) |
| `session-context.md` | Cross-session handoff template |
| `personal/` | Personal profile skeleton — fill this out in your private personal repo |
| `team/` | Team profile skeleton — fill this out in your team repo |

---

## `docs/` — Human-Facing Documentation

| File | Audience |
|---|---|
| `getting-started.md` | New adopters — how to set up and run your first assignment |
| `enterprise-setup.md` | Teams and organizations — adding the team and personal layers |
| `features.md` | This file — directory map and feature overview |

---

## Override System

Team and personal profiles can override any governance file by placing a matching file at the same path within their profile repo. The override behavior is declared in the file's front matter:

- `override: replacement` — the entire file replaces the infrastructure version
- `override: extend` — only named/numbered rules in the file replace matching rules; the rest of the infrastructure version remains in effect

Resolution order: **personal > team > infrastructure**

---

## Template Versioning

All template files carry a `template-version` stamp. When infrastructure templates evolve, profile files on older versions will be flagged. Upgrading is manual — review the diff and adopt what's relevant.

---

## What This Repository Is Not

- Not a runnable application
- Not a project repository — keep your project code separate
- Not prescriptive about tooling — works with any LLM Agent platform
- Not a replacement for engineering judgment — it supports it
