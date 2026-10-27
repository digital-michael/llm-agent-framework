# Go — System Prompt Snippet

> Compose this with `docs/governance/system-prompts/base-system-prompt.md` for Go projects.

---

## Go-Specific Governance

- Go version: [INSERT — e.g., go1.23]
- Module path: [INSERT — e.g., github.com/org/repo]
- Linter in use: [INSERT — golangci-lint | staticcheck | none]

## Rules

- Always wrap errors with context: `fmt.Errorf("operation: %w", err)`. Never silently discard errors.
- Context (`context.Context`) must be the first parameter of any blocking or cancellable function.
- After any dependency change, run `go mod tidy` and verify `go.mod` and `go.sum`.
- Use table-driven tests for all logic with multiple cases.
- Prefer small interfaces (1–3 methods). Flag interface designs larger than this for review.
- Before marking any concurrent code complete, note that it should be reviewed for data races.
- **Operator-facing text is a keyed catalogue (D-041).** Text a person reads (a screen, a message, a dialog, a command title, a setting's description) is defined once as a keyed message with named placeholders, not written inline: it can then be renamed and translated. Whole messages, no joined fragments; plural and select forms inside the message, by the locale's rules; product and company names are placeholders fed from one brand table; numbers and dates are formatted by the locale; one place chooses the language, with English as the fallback. Text a model reads, log events, ids and text that callers or tests match are not catalogue text. A test lists the migrated files and fails if a hand-typed sentence returns, and a pseudo-locale test shows what bypasses the catalogue. Mechanism here: message IDs with `golang.org/x/text/message` or `go-i18n`, for programs a person operates; not for MCP tool descriptions or other text read by models.

## Final Gate Additions

- [ ] `go vet ./...` passes
- [ ] `go test -race ./...` passes
- [ ] `go mod tidy` run; `go.mod` and `go.sum` in sync
- [ ] Linter (if configured) passes
- [ ] Operator-facing text is in the catalogue; no hand-typed sentence in a migrated file (D-041)
