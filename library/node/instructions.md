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
- **Operator-facing text is a keyed catalogue (D-041).** Text a person reads (a screen, a message, a dialog, a command title, a setting's description) is defined once as a keyed message with named placeholders, not written inline: it can then be renamed and translated. Whole messages, no joined fragments; plural and select forms inside the message, by the locale's rules; product and company names are placeholders fed from one brand table; numbers and dates are formatted by the locale; one place chooses the language, with English as the fallback. Text a model reads, log events, ids and text that callers or tests match are not catalogue text. A test lists the migrated files and fails if a hand-typed sentence returns, and a pseudo-locale test shows what bypasses the catalogue. Mechanism here: a typed message table with a small ICU-style formatter, or an established library (`i18next`, `Intl`-based formatting; `vscode.l10n` in a VS Code extension).

## Final Gate Additions

- [ ] No hardcoded secrets or environment values in generated code
- [ ] `.env.example` present and complete
- [ ] Graceful shutdown implemented
- [ ] Health check endpoint present (if service)
- [ ] Process-level error handling in place
- [ ] Operator-facing text is in the catalogue; no hand-typed sentence in a migrated file (D-041)
