# Go — Governance Overlay

> Go-specific lessons learned and pitfalls.
> Update at every retrospective involving Go work.

---

## Error Handling

- LLM Agents sometimes generate `if err != nil { return err }` without wrapping context. Use `fmt.Errorf("context: %w", err)` to preserve error chains.
- Never silently discard errors with `_`. If an error is intentionally ignored, comment why.
- Sentinel errors and error types should be defined at the package level, not inline.

## Concurrency

- Review all goroutine usage for data races. Run `go test -race ./...` before the final gate.
- Channel direction should be explicit in function signatures: `chan<- T` (send-only), `<-chan T` (receive-only).
- Context propagation (`context.Context`) should be the first parameter of any function that may block or be cancelled. Verify this in all generated code.

## Module Management

- After any dependency addition or change, verify `go.mod` and `go.sum` are consistent: `go mod tidy`.
- Pin major versions explicitly. `go get package@latest` may introduce breaking changes.
- Avoid indirect dependency version overrides unless you understand the reason.

## Idiomatic Go

- Prefer small, focused interfaces. An interface with one method is idiomatic; an interface with ten methods is usually a design problem.
- Use table-driven tests for all logic with multiple input cases.
- Package names should be lowercase, single words. LLM Agents sometimes generate multi-word package names.
- Avoid unnecessary pointer receivers. Use pointer receivers only when mutation or large structs justify it.

## Build and Testing

- Run `go vet ./...` before the final gate — catches common correctness issues.
- Run `golangci-lint` or equivalent if the project has a linter configured.
- Integration tests should use `TestMain` for setup/teardown when applicable.

---

## Resource Lifecycle Contract (RLC)

See `governance/rlc.md` for the full definition. Go-specific phase mappings:

| Phase | Go idioms |
|---|---|
| **Allocation** | `NewXxx()` constructor, composite literal, `sync.Pool.Get()` |
| **Configuration** | functional options (`WithXxx()`), config struct passed to constructor |
| **Activation** | `Start()`, `Open()`, `Dial()`, `ListenAndServe()`, or implicit on first call |
| **Primary Use** | interface method calls; verify access scope (exported vs unexported) |
| **Deactivation** | `Stop()`, `Close()`, `Shutdown()`, `cancel()` |
| **Deallocation** | GC handles memory; explicit `defer resource.Close()` for I/O and network resources |

**Common Go failures:**
- Missing `defer resource.Close()` immediately after a successful open — pair them at the same site
- Configuration applied after activation (e.g., setting options on an already-started server)
- Goroutines allocated without a defined deactivation path (no context cancellation, no `WaitGroup`)
- Shared resources passed to goroutines without defined ownership — concurrent access without a designated owner
- Agent check: verify `defer` is present and correctly scoped for every I/O resource opened

---

## [Add new lessons here — format: `[date] [context] Lesson. → Action.`]

-
