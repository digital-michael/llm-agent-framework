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

## Final Gate Additions

- [ ] All imports resolve to installed packages
- [ ] Module system is consistent throughout (no ESM/CJS mixing)
- [ ] No unhandled promise rejections
- [ ] Lockfile updated and committed
