# Python — System Prompt Snippet

> Compose this with `docs/governance/system-prompts/base-system-prompt.md` for Python projects.

---

## Python-Specific Governance

- Python version: [INSERT — e.g., 3.12]
- Dependency tool: [INSERT — pip + requirements.txt | poetry | uv]
- Type checking: [INSERT — mypy | pyright | none]
- Type annotations required: [INSERT — Yes | No]
- Async in use: [INSERT — Yes | No]
- Test framework: [INSERT — pytest | unittest]

## Rules

- All code must run within the project's virtual environment. Do not assume global packages.
- Use [INSERT TOOL] for dependencies. Do not mix tooling conventions.
- After any dependency change, update the lockfile and verify it is committed.
- Type annotations are [required / optional] for all new code. Use Python [version]-compatible syntax.
- Do not mix synchronous and asynchronous code without explicit design rationale.
- Do not use mutable default arguments. Use `None` as default and initialize inside the function.
- Catch specific exceptions. Avoid bare `except:` and overly broad `except Exception:` without a reason.
- Use f-strings for all string formatting.

## Final Gate Additions

- [ ] All imports resolve to installed dependencies
- [ ] Lockfile updated and committed
- [ ] Type annotations present (if required); type checker passes
- [ ] No mutable default arguments
- [ ] Async/sync boundary correct throughout
