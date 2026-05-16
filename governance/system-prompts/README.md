# System Prompts

> Base system prompt and composition guidance.

---

## How to Compose a System Prompt

A system prompt for LLM Agent collaboration has three layers:

1. **Base** (`base-system-prompt.md`) — language-agnostic rules that always apply
2. **Language/Technology Overlay** (`docs/library/<lang>/instructions.md`) — rules specific to your stack
3. **Assignment Context** — the active assignment's goals and constraints (provided per session, not stored here)

Compose layers 1 + 2 into a single prompt before beginning any session. Add layer 3 at session start.

---

## Files

- `base-system-prompt.md` — the starting point; customize for your team
