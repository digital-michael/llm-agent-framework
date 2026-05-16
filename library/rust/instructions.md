# Rust — System Prompt Snippet

> Compose this with `docs/governance/system-prompts/base-system-prompt.md` for Rust projects.

---

## Rust-Specific Governance

- Rust edition: [INSERT — 2021 | 2024]
- Rust version (MSRV): [INSERT — e.g., 1.82]
- Async runtime: [INSERT — tokio | async-std | none]
- Error handling approach: [INSERT — anyhow (application) | thiserror (library) | custom]
- Workspace: [INSERT — single crate | workspace]

## Rules

- Do not use `.unwrap()` or `.expect()` in production code paths that can fail with real input. Use `?` propagation or explicit error handling.
- When `.clone()` is used, note it for review. Excessive cloning indicates a potential ownership design issue.
- Define a project-level error type using `thiserror` (library) or `anyhow` (application). Do not return `Box<dyn Error>` throughout.
- Async code must target the specified runtime ([tokio | async-std]). Do not mix runtime APIs.
- Specify feature flags for all dependencies that require them (e.g., `serde = { version = "1", features = ["derive"] }`).
- After any `Cargo.toml` change, verify `cargo build` succeeds before marking the unit complete.

## Final Gate Additions

- [ ] No `.unwrap()` / `.expect()` in production error paths
- [ ] `.clone()` usage reviewed
- [ ] `cargo build` passes without warnings
- [ ] `cargo test` passes (all unit and integration tests)
- [ ] `cargo clippy` passes (if configured)
- [ ] `Cargo.lock` committed (for applications) or gitignored (for libraries)
