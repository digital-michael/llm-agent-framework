# Rust — Governance Overlay

> Rust-specific lessons learned and pitfalls.
> Update at every retrospective involving Rust work.

---

## Ownership and Borrowing

- When LLM-generated code uses `.clone()`, review whether the clone is justified or whether a design adjustment (reference, lifetime, restructuring) would be more appropriate.
- Excessive `Arc<Mutex<T>>` wrapping is a smell that the data model may need revision. Review shared-state patterns.
- Lifetime annotations should be minimal. If the agent generates complex lifetime annotations, consider whether the ownership model can be simplified.

## Error Handling

- Production code must not use `.unwrap()` or `.expect()` in paths that can fail with real input. Use `?` propagation or explicit `match`/`if let`.
- Define a project-level error type (or use `thiserror` / `anyhow`) rather than returning `Box<dyn Error>` everywhere.
- Use `anyhow` for application-level error handling; use `thiserror` for library error types. Specify which applies before generating error types.

## Async

- Specify the async runtime before generating any async code: `tokio` or `async-std`. The two are not interchangeable.
- `tokio::main` is the standard entry point for tokio-based projects. Verify it is present and correctly configured.
- Async traits require `async-trait` or Rust 1.75+ (`async fn` in traits stable). Specify the Rust edition and version.

## Cargo and Dependencies

- After any `Cargo.toml` change, verify `Cargo.lock` is consistent and the project builds: `cargo build`.
- Specify feature flags explicitly when adding dependencies. LLM Agents sometimes add dependencies without the required feature flags (e.g., `serde` without `features = ["derive"]`).
- Workspace setups require `Cargo.toml` at the workspace root and member crates properly declared.

## Testing

- Unit tests in Rust live in the same file as the code under test in a `#[cfg(test)]` module. Integration tests live in `tests/`.
- Use `cargo test` to run all tests. LLM Agents sometimes generate test commands that do not include integration tests.
- Property-based testing with `proptest` is available and worth considering for algorithmic code.

---

## [Add new lessons here — format: `[date] [context] Lesson. → Action.`]

-
