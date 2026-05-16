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

## Final Gate Additions

- [ ] `go vet ./...` passes
- [ ] `go test -race ./...` passes
- [ ] `go mod tidy` run; `go.mod` and `go.sum` in sync
- [ ] Linter (if configured) passes
