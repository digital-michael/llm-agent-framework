# JavaScript — Overview

LLM Agents are prolific JavaScript generators. The primary governance challenges are module system consistency (ESM vs CommonJS), runtime assumptions, and the breadth of the ecosystem. Without explicit constraints, agents may generate code that mixes conventions or targets the wrong environment.

## Active Files

- `governance-overlay.md` — JavaScript-specific lessons and pitfalls
- `instructions.md` — JavaScript additions for the base system prompt

## Key Concerns

- Module system: ESM (`import/export`) and CommonJS (`require/module.exports`) are not interchangeable; specify which is in use
- Async patterns: callback, Promise, and async/await may be mixed inconsistently
- Type safety: vanilla JS has no type enforcement; TSDoc, JSDoc types, or TypeScript should be specified if desired
- Package management: npm, yarn, and pnpm have different lockfile formats; mixing them causes issues
