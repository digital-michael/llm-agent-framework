---
template-version: 1.0.0
override: replacement
---

# Best Practices

> Standing practices for every project and every language, distilled from lessons learned.
> Each is a rule, not a suggestion. They are loaded through the base system prompt (Process and Workflow).
> Project and domain profiles may add detail (see their own `best-practices.md`) but may not weaken a rule here.

---

## BP-1 — Tool drift: re-read tool definitions after a long gap

**Rule.** Tool definitions change between sessions. After a long gap, never write with a remembered parameter name.

**When it applies.**

- A session resumes after a few days or more, or after the context was summarized or compacted.
- The host reports updated tool definitions.
- A tool that was working starts returning results that differ from what was sent.

**How to apply.**

1. Reload the schema of every tool that **writes** (creates, updates, deletes, sends, publishes) before the first call. Reading tools can be checked on first use.
2. After the first write of each kind, **read it back** and confirm every field landed (a get, or a verbose list). Do this before batching.
3. Treat silence as no evidence. A tool that drops an unrecognised parameter without an error looks identical to one that accepted it.
4. If a tool silently drops unknown parameters, record a defect asking for a warning or an error. Do not rely on remembering the difference.

**Why.** A bulk create used a parameter name that the tool had since renamed. The unknown name was dropped silently, every item was created without its parent, and the loss was found only later from a verbose listing. A single read-back after the first create would have caught it.

---

## BP-2 — Read before fixing a failing build: intentional failures

**Rule.** A failing build or test is not proof of a defect. Some builds fail on purpose (for example a fail-closed build that must refuse to compile without an explicit variant selected). Establish that the failure is unintended before changing anything.

**How to apply (the agent).**

1. **Read** the build file header (Makefile, build script, CI definition) and **list the folder** before touching code.
2. Search for the marker `INTENTIONAL-FAILURE` (below) in the files you are about to change, and in the build file.
3. **Reproduce with the repository's own gate** (its documented build and test commands), not a bare language command. A bare `build ./...` can fail where the gate passes, and the reverse.
4. If the documentation says the failure is deliberate, stop. Do not add a fallback, default or stub to make it build. If the documentation and the behaviour disagree, ask.
5. **Report only what the check supports.** Say exactly which command ran and what it printed. "It has not built since X" requires having built with the real gate.

**How to apply (the author).** Mark every intentional failure so the next reader, human or agent, cannot mistake it for a bug. Use the greppable token `INTENTIONAL-FAILURE` at three levels:

| Where | What to write |
|---|---|
| The build file header | Which targets fail on purpose, under what condition, and which command is the real gate. |
| Every file that causes or depends on the failure | A comment at the point a reader would be tempted to add the missing piece. |
| A gate or test | A check that the failure still happens, so removing it is also caught. |

Comment form (use the language's comment syntax):

```text
INTENTIONAL-FAILURE: <what fails> when <condition>.
Do not add a default or fallback to make it build. Verify with: <the repository's gate command>.
```

**Why.** A fail-closed example was seen failing under a bare build command. It was "fixed" by adding a default file, which broke the repository's real gate and reached the public main branch. It was found sixteen days later. The Makefile header already said the failure was deliberate; it was not read.

---

## Promotion

These two were promoted to governance at the practitioner's direction (2026-11-23), ahead of the usual three-recurrence threshold, because both caused damage that reached shared or public state. Source entries: the retrospective of 2026-11-04 to 2026-11-22 in the originating project's lessons-learned file.
