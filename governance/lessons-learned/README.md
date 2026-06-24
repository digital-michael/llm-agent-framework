# Lessons Learned

> Accumulated knowledge from assignments, retrospectives, and tech stack experience.
> Three sources: LLM Agent, Technologist, Tech Stack.

---

## Structure

Lessons learned are organized by source. Each entry is short, specific, and actionable.

**Format per entry:**
```
- [Date] [Context] Lesson. → Action to take.
```

Example:
```
- [2026-04] [Python/FastAPI] Pydantic v2 has breaking changes from v1 — field validators work differently. → Always check Pydantic major version before generating models.
```

---

## Files

- `template.md` — blank template for a new lessons-learned document
- `retrofit-project-2026.md` — lessons from retrofit workflow applications (RTK, local-lab-ai-stack, 2026)
- `framework-architecture-2026.md` — gaps in framework architecture itself: entry points, load cascade, yml integration

---

## When to Update

- At every retrospective (required, not optional)
- When an issue is encountered mid-assignment that is likely to recur
- When a proven pattern emerges that should be reused

---

## When to Promote to Governance

When a lesson recurs across **three or more assignments**, it belongs in the base system prompt — not just the lessons-learned file. Move it deliberately at a retrospective.
