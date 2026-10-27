# JavaScript — System Prompt Snippet

> Compose this with `docs/governance/system-prompts/base-system-prompt.md` for JavaScript projects.

---

## JavaScript-Specific Governance

- Module system: [INSERT — ESM | CommonJS]
- Package manager: [INSERT — npm | yarn | pnpm]
- Type approach: [INSERT — plain JS | JSDoc types | TypeScript]
- Test framework: [INSERT — Jest | Vitest | Mocha | other]

## Rules

- Use [ESM | CommonJS] exclusively. Do not mix module systems in the same project.
- Use async/await consistently. Do not mix with raw Promise chains unless there is a documented reason.
- Always handle rejected promises. Unhandled promise rejections are failures.
- Do not use `await` inside `forEach`. Use `for...of` or `Promise.all` for async iteration.
- Verify all generated imports exist in `package.json` before marking a unit complete.
- Do not add or modify lockfiles manually. Use the package manager CLI only.
- **Operator-facing text is a keyed catalogue (D-041).** Text a person reads (a screen, a message, a dialog, a command title, a setting's description) is defined once as a keyed message with named placeholders, not written inline: it can then be renamed and translated. Whole messages, no joined fragments; plural and select forms inside the message, by the locale's rules; product and company names are placeholders fed from one brand table; numbers and dates are formatted by the locale; one place chooses the language, with English as the fallback. Text a model reads, log events, ids and text that callers or tests match are not catalogue text. A test lists the migrated files and fails if a hand-typed sentence returns, and a pseudo-locale test shows what bypasses the catalogue. Mechanism here: a typed message table with a small ICU-style formatter, or an established library (`i18next`, `Intl`-based formatting).

## Final Gate Additions

- [ ] All imports resolve to installed packages
- [ ] Module system is consistent throughout (no ESM/CJS mixing)
- [ ] No unhandled promise rejections
- [ ] Lockfile updated and committed
- [ ] Operator-facing text is in the catalogue; no hand-typed sentence in a migrated file (D-041)
