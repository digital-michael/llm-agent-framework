# Node.js — Governance Overlay

> Node.js-specific lessons learned and pitfalls.
> Update at every retrospective involving Node.js work.

---

## Environment and Configuration

- Never hardcode secrets, API keys, or environment-specific values. Use environment variables. LLM Agents sometimes hardcode values for convenience — always review generated config.
- Use a `.env` file for local development with a corresponding `.env.example` committed to the repo. Never commit `.env`.
- Validate required environment variables at startup. A missing variable should cause a clear error, not a silent failure.

## Server Lifecycle

- Implement graceful shutdown: listen for `SIGTERM` and `SIGINT`, close connections cleanly before exiting.
- Handle uncaught exceptions and unhandled promise rejections at the process level — log them and exit cleanly rather than leaving the process in an undefined state.
- Health check endpoints (`/health` or `/healthz`) should be present in any service intended for production.

## Framework

- Specify the HTTP framework before generating any route or middleware code. Express, Fastify, and Hono have meaningfully different conventions.
- Middleware order matters in Express. LLM Agents sometimes generate middleware registrations in the wrong order (e.g., body parser after routes).
- Error handling middleware in Express must have four parameters `(err, req, res, next)`. LLM Agents sometimes generate three-parameter error handlers that don't behave correctly.

## Node.js Version

- Specify the Node.js version (LTS recommended). `package.json` should include an `engines` field.
- LTS-only APIs differ from current. Verify that generated code uses APIs available in your target version.

## Testing

- Use supertest (or equivalent) for HTTP integration tests — test the server without binding a port.
- Mock external services (HTTP calls, databases) in unit tests. LLM Agents sometimes generate tests that make real network calls.

---

## Resource Lifecycle Contract (RLC)

See `governance/rlc.md` for the full definition. Node.js-specific phase mappings:

| Phase | Node.js idioms |
|---|---|
| **Allocation** | constructor, factory function, `require`/`import` at module load |
| **Configuration** | options object passed to constructor, environment variables |
| **Activation** | `listen()`, `connect()`, `.start()`, first async call |
| **Primary Use** | method calls, event listeners; verify listener cleanup plan at activation |
| **Deactivation** | graceful shutdown handler (`SIGTERM`/`SIGINT`), `.close()`, `.end()` |
| **Deallocation** | GC + explicit `.destroy()` for streams; remove all event listeners |

**Common Node.js failures:**
- Event listeners added during activation never removed during deactivation — memory leak
- Server allocated but graceful shutdown (`SIGTERM`/`SIGINT`) not implemented
- Database pool or connection opened at module load with no corresponding close on process exit
- Agent check: verify `SIGTERM`/`SIGINT` handlers call deactivation for every server and connection resource

---

## [Add new lessons here — format: `[date] [context] Lesson. → Action.`]

-
