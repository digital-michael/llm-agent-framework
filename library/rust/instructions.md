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
- **Operator-facing text is a keyed catalogue (D-041).** Text a person reads (a screen, a message, a dialog, a command title, a setting's description) is defined once as a keyed message with named placeholders, not written inline: it can then be renamed and translated. Whole messages, no joined fragments; plural and select forms inside the message, by the locale's rules; product and company names are placeholders fed from one brand table; numbers and dates are formatted by the locale; one place chooses the language, with English as the fallback. Text a model reads, log events, ids and text that callers or tests match are not catalogue text. A test lists the migrated files and fails if a hand-typed sentence returns, and a pseudo-locale test shows what bypasses the catalogue. Mechanism here: `fluent` or `rust-i18n`, with message IDs and named arguments.

## Final Gate Additions

- [ ] No `.unwrap()` / `.expect()` in production error paths
- [ ] `.clone()` usage reviewed
- [ ] `cargo build` passes without warnings
- [ ] `cargo test` passes (all unit and integration tests)
- [ ] `cargo clippy` passes (if configured)
- [ ] `Cargo.lock` committed (for applications) or gitignored (for libraries)
- [ ] Operator-facing text is in the catalogue; no hand-typed sentence in a migrated file (D-041)
