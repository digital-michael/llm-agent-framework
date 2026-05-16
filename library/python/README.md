# Python — Overview

LLM Agents are highly capable Python collaborators. The main governance challenges are environment isolation, dependency management tooling (which has fragmented significantly), type annotation consistency, and async pattern correctness. Python's permissiveness makes it easy to generate code that works but is structurally unsound.

## Active Files

- `governance-overlay.md` — Python-specific lessons and pitfalls
- `instructions.md` — Python additions for the base system prompt

## Key Concerns

- Environment isolation: always use a virtual environment; specify the tooling (venv, poetry, uv)
- Dependency management: pip, poetry, and uv have different lockfile and config conventions
- Type annotations: specify whether type hints are required; mypy or pyright configuration affects generation
- Async: asyncio patterns are non-trivial; mixing sync and async code causes subtle bugs
- Python version: 3.10+ type syntax differs from 3.8/3.9; specify the target version
