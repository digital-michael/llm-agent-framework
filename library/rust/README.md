# Rust — Overview

LLM Agents can generate competent Rust but require careful governance around ownership/borrow checker patterns, error handling idioms, and Cargo conventions. Rust's compiler enforces correctness strictly; LLM-generated Rust that compiles is generally sound, but generated code that fights the borrow checker or avoids it with excessive cloning warrants review.

## Active Files

- `governance-overlay.md` — Rust-specific lessons and pitfalls
- `instructions.md` — Rust additions for the base system prompt

## Key Concerns

- Ownership and borrowing: LLM Agents sometimes work around borrow checker errors by adding `.clone()` excessively — review these for design implications
- Error handling: `Result<T, E>` and the `?` operator are idiomatic; panicking with `unwrap()` in production code is not
- Async: tokio and async-std have different APIs; specify the runtime
- Cargo: workspace setup, feature flags, and dependency management require explicit tracking
