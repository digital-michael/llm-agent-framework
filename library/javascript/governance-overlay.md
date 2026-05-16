# JavaScript — Governance Overlay

> JavaScript-specific lessons learned and pitfalls.
> Update at every retrospective involving JavaScript work.

---

## Module System

- Always specify ESM or CommonJS at project start. LLM Agents default inconsistently. Mixing both in the same project causes runtime errors.
- ESM requires `"type": "module"` in `package.json` and `.mjs` or explicit extension handling. Verify this is correct before testing.
- Dynamic `import()` in CommonJS projects must be handled carefully — not all Node.js versions support it identically.

## Async Patterns

- Use async/await consistently. LLM Agents sometimes mix Promise chains and async/await in the same codebase. Establish a convention and enforce it.
- Unhandled promise rejections are silent failures. Ensure all async code has proper error handling.
- `await` inside a `forEach` does not behave as expected. Use `for...of` or `Promise.all` for async iteration.

## Type Safety

- If TypeScript is not in use, specify whether JSDoc type annotations are desired. LLM Agents can generate typed JSDoc comments that improve IDE support without requiring a TypeScript build.
- Without type constraints, LLM Agents may generate loosely typed code that is difficult to refactor.

## Dependencies and Package Management

- Specify the package manager (npm, yarn, pnpm). Do not mix lockfile formats.
- After any dependency change, verify `package-lock.json` / `yarn.lock` / `pnpm-lock.yaml` is updated.
- LLM Agents sometimes generate imports for packages not in `package.json`. Verify all imports resolve.

## Testing

- Specify the test framework: Jest, Vitest, Mocha, or other. LLM Agents default to Jest; if your project uses Vitest, the syntax differs.
- Mocking behavior differs significantly between frameworks. Establish the mock pattern before generating tests.

---

## [Add new lessons here — format: `[date] [context] Lesson. → Action.`]

-
