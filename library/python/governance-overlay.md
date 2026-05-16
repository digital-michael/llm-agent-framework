# Python — Governance Overlay

> Python-specific lessons learned and pitfalls.
> Update at every retrospective involving Python work.

---

## Environment and Dependencies

- Always work within a virtual environment. Never install packages globally for a project.
- Specify the dependency management tool (pip + requirements.txt, poetry, uv) and do not mix conventions.
- After any dependency change, update the lockfile (poetry.lock, uv.lock) and verify it is committed.
- LLM Agents sometimes generate imports for packages not in the project dependencies. Verify all imports resolve before marking work complete.
- Pydantic v1 and v2 have breaking differences (field validators, model config, JSON serialization). Specify the major version before generating any Pydantic models.

## Type Annotations

- Specify whether type annotations are required. Without explicit instruction, LLM Agents may generate untyped code.
- Python 3.10+ uses `X | Y` union syntax; Python 3.8/3.9 requires `Optional[X]` and `Union[X, Y]`. Use the correct syntax for your target version.
- Dataclasses and Pydantic models should have fully annotated fields.
- Run `mypy` or `pyright` if type checking is part of the project's quality gates.

## Async

- Mixing synchronous and asynchronous code is a common LLM-generated mistake. An `async def` function cannot be called from a synchronous context without an event loop.
- Use `asyncio.run()` only at the top-level entry point; not inside other async functions.
- For async database or HTTP calls, ensure the libraries in use have async-native support. Wrapping sync libraries in threads is a workaround, not a solution.

## Common Pitfalls

- Mutable default arguments (`def f(x=[])`) are shared across calls — a classic Python trap. LLM Agents generate these occasionally. Review all default argument values.
- `except Exception` is too broad. Catch specific exceptions unless there is a documented reason to catch broadly.
- String formatting: use f-strings (Python 3.6+) consistently. Avoid mixing %-format and `.format()`.

## Virtual Environment Policy (Agent Rules)

The agent must use a project-local virtual environment as the default execution model. Global Python installs are for tooling only.

**Agent rules:**
- Always prefer a project-local environment over global Python installs
- Create one environment per repository or application
- Keep the environment directory (`.venv/`) out of version control
- Install and run project dependencies only after activation
- Treat `requirements.txt` (or the project's lockfile) as the reproducible dependency source
- Do not mix dependencies from unrelated projects
- When done, deactivate the environment and leave the workspace clean

**Standard setup workflow:**
```bash
# 1. Create the environment in the project root
python3 -m venv .venv

# 2. Activate it (macOS/Linux)
source .venv/bin/activate

# 3. Upgrade packaging tools inside the environment
python -m pip install --upgrade pip

# 4. Install project dependencies
pip install -r requirements.txt

# 5. Run tests and tooling inside the environment
pytest

# 6. Deactivate when finished
deactivate
```

If the project does not yet have a dependency file, install only the packages required for the task, then record them: `pip freeze > requirements.txt`

---

## Testing

- Specify pytest as the test framework (preferred) unless the project uses unittest.
- Use `pytest.fixture` for setup/teardown, not setUp/tearDown from unittest.
- Mock external calls with `unittest.mock` or `pytest-mock`. LLM Agents sometimes generate tests that make real network or file system calls.

---

## Lessons Learned

### Session-Scoped Fixture for Module-Level Skip Gating

When an entire test module depends on a single precondition (a service being available, a model being loaded, an environment variable being set), use a session-scoped fixture that calls `pytest.skip()` inside itself when the precondition is absent. Any test that requests the fixture is automatically skipped with a single, consistent SKIP record:

```python
@pytest.fixture(scope="session")
def service_available(http_client: httpx.Client) -> str:
    resp = http_client.get("/health")
    if resp.status_code != 200:
        pytest.skip("Service is not available — start the service first")
    return resp.json()["version"]
```

**Rule:** Prefer a session-scoped fixture over repeating `@pytest.mark.skipif(...)` on every test function. This produces one skip record per session rather than N, and keeps the precondition check in one place.

For module-level gating (entire module skipped if a dependency is unavailable), use `autouse=True` with `scope="module"`:

```python
@pytest.fixture(scope="module", autouse=True)
def require_service(http_client):
    try:
        r = http_client.get(f"{SERVICE_URL}/health")
        if r.status_code != 200:
            pytest.skip(f"Service not ready (status {r.status_code})")
    except Exception:
        pytest.skip("Service not reachable")
```

---

### Set `temperature=0.0` for Deterministic LLM Assertions

When writing tests that make exact-string assertions on LLM output (specific text, numbers, or JSON structure), always set `temperature=0.0`. At default temperature, sampling noise can make tests flaky even on simple, correct prompts.

```python
body = call_llm(..., temperature=0.0)
assert body["answer"] == "42"  # deterministic at temp=0
```

Some backends clamp temperature to a small positive floor but this is still deterministic enough for test assertions.

**Rule:** Set `temperature=0.0` for all tests asserting on specific model output. Reserve non-zero temperatures for tests that explicitly verify output variety.

---

## [Add new lessons here — format: `[date] [context] Lesson. → Action.`]

-
