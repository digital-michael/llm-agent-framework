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

## Resource Lifecycle Contract (RLC)

See `governance/rlc.md` for the full definition. JavaScript-specific phase mappings:

| Phase | JavaScript idioms |
|---|---|
| **Allocation** | constructor, factory function, module-level initialization |
| **Configuration** | options object, environment variables, module constants |
| **Activation** | `.connect()`, `.open()`, first async invocation, `addEventListener` |
| **Primary Use** | method calls, event-driven callbacks; verify listener cleanup plan |
| **Deactivation** | `.close()`, `.disconnect()`, `removeEventListener`, `AbortController.abort()` |
| **Deallocation** | GC; explicit cleanup for DOM nodes, event listeners, and timers |

**Common JavaScript failures:**
- `setInterval` or `setTimeout` allocated with no stored reference — cannot be cleared
- DOM event listeners added without a paired `removeEventListener` — leak on component unmount
- Fetch/async operations not cancelled via `AbortController` when the consuming scope is torn down
- Agent check: verify every timer and event listener has a defined deactivation path

---

## [Add new lessons here — format: `[date] [context] Lesson. → Action.`]

-
