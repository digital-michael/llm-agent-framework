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

## [Add new lessons here — format: `[date] [context] Lesson. → Action.`]

-
