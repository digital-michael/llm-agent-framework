# Node.js — System Prompt Snippet

> Compose this with `docs/governance/system-prompts/base-system-prompt.md` for Node.js projects.
> Also compose with `docs/library/javascript/instructions.md` for JS-layer rules.

---

## Node.js-Specific Governance

- Node.js version: [INSERT — e.g., 22 LTS]
- HTTP framework: [INSERT — Express | Fastify | Hono | none]
- ORM / database client: [INSERT — Prisma | Drizzle | pg | mongoose | none]

## Rules

- Never hardcode secrets or environment-specific values. All configuration via environment variables, validated at startup.
- Do not commit `.env` files. `.env.example` (with placeholder values) is committed instead.
- Implement graceful shutdown: handle `SIGTERM` and `SIGINT`, close connections before exit.
- Handle process-level uncaught exceptions and unhandled rejections: log and exit cleanly.
- All HTTP routes must have error handling. Unhandled errors in async route handlers cause silent failures.
- Middleware order is significant. Body parsing middleware must be registered before route handlers.

## Final Gate Additions

- [ ] No hardcoded secrets or environment values in generated code
- [ ] `.env.example` present and complete
- [ ] Graceful shutdown implemented
- [ ] Health check endpoint present (if service)
- [ ] Process-level error handling in place
