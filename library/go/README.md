# Go — Overview

LLM Agents are generally strong Go collaborators. Go's explicit error handling and module system align well with structured governance. Key areas requiring active oversight: idiomatic error handling, goroutine safety, and module version management.

## Active Files

- `governance-overlay.md` — Go-specific lessons and pitfalls
- `instructions.md` — Go additions for the base system prompt

## Key Concerns

- Error handling: Go errors are values; LLM Agents sometimes generate overly terse error handling or swallow errors silently
- Goroutine safety: concurrent code requires explicit review; LLM Agents can produce data races
- Module management: `go.mod` and `go.sum` must stay in sync; verify after any dependency changes
- Interface design: Go interfaces are implicit; LLM Agents sometimes over-engineer interface hierarchies against Go idiom
