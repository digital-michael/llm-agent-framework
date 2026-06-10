# Bash — Governance Overlay

> Bash-specific standards, lessons learned, and pitfalls.
> Update at every retrospective involving Bash work.
>
> References: [Google Shell Style Guide](https://google.github.io/styleguide/shellguide.html) · [ShellCheck](https://www.shellcheck.net/) · [Bash Manual](https://www.gnu.org/software/bash/manual/)

---

## Script Structure

- Start with a shebang: `#!/usr/bin/env bash`
- Set strict mode immediately after: `set -euo pipefail`
- Define constants near the top using `readonly`:
  ```bash
  readonly CONFIG_FILE="${CONFIG_FILE:-config.json}"
  ```
- Use the `main()` function pattern:
  ```bash
  main() {
    validate_args "$@"
    do_the_work
  }
  main "$@"
  ```
  This makes scripts testable and keeps global side effects explicit.
- Group related functions; place helper functions before the functions that call them.
- Run [ShellCheck](https://www.shellcheck.net/) on every script: `shellcheck scripts/*.sh`

---

## Safety and Error Handling

- `set -euo pipefail` is mandatory. Without it, scripts continue silently after errors.
  - `set -e`: exit on error
  - `set -u`: treat unset variables as errors
  - `set -o pipefail`: fail if any command in a pipeline fails
- Trap on EXIT for cleanup: `trap 'cleanup' EXIT`. Always clean up temp files and resources.
- Log errors to stderr: `echo "ERROR: message" >&2`
- Return meaningful exit codes: 0 for success, 1 for general errors, 2 for usage errors.
- Use `|| true` sparingly and only when failure is genuinely acceptable.

---

## Quoting

- Always quote variables: `"$var"` not `$var`. Unquoted variables fail with spaces and special characters.
- Always quote command substitutions: `"$(command)"`.
- Array elements must be quoted when expanded: `"${array[@]}"`.
- Use `${var:-default}` for default values.
- Prefer lowercase for local variables, UPPERCASE for exported or environment variables.
- **LLM agents frequently generate unquoted variables. Review every variable reference.**

---

## Arrays

- Declare arrays explicitly: `files=("a.txt" "b.txt")`
- Always expand with `"${array[@]}"` (with quotes) to handle filenames containing spaces.
- Never iterate with `${array[*]}` — it collapses to a single string.

---

## Functions

- Declare with `function_name() { }` syntax (no `function` keyword; improves POSIX compatibility).
- Use `local` for function-scoped variables to avoid polluting the global namespace.
- Return values via exit codes or stdout capture: `result=$(my_function)`.
- Keep functions focused: one responsibility per function.

---

## Portability

- Target Bash 4.4+ (standard on modern Linux). Use `#!/bin/sh` explicitly for POSIX-only scripts.
- Bash-specific features (`[[`, arrays, `$(...)`) are not available in strict POSIX sh.
- Use `[[ ]]` for conditionals in Bash (safer and more predictable than `[ ]`).
- Prefer `$()` over backticks for command substitution.
- Check for required external tools: `command -v jq >/dev/null 2>&1 || { echo "jq required"; exit 1; }`

---

## Security

- **Never use `eval` with external input.** Command injection via `eval` is a critical vulnerability.
- **Never interpolate shell variables directly into jq filter strings.** See Lessons below.
- Validate all external input (arguments, env vars, file content) before using it in commands.
- Use `mktemp` for temporary files, not predictable paths like `/tmp/myfile`.
- Avoid running scripts as root unless explicitly required; document why root is needed.
- Sanitize file paths: resolve with `realpath` or `readlink -f`.

---

## Style and Readability

- Include a usage/help function for any script intended for reuse.
- Comment non-obvious logic. Scripts without comments are hard to maintain.

---

## Lessons Learned

### jq Dot-Notation Fails on Hyphenated Keys (Injection Risk)

**Observed:** jq `1.6` / `1.7`

When iterating over values from a JSON file and building a jq filter by interpolating a shell variable directly into a double-quoted string:

```bash
# BROKEN and unsafe
value=$(jq -r ".services.${svc}.image" "$CONFIG_FILE")
```

If `$svc` contains a hyphen (e.g., `knowledge-index`), jq interprets the hyphen as a subtraction operator and returns `null` silently. Additionally, this pattern allows shell variable expansion inside the filter string, which enables jq expression injection if `$svc` is externally controlled.

**Fix:** Use `--arg` to pass the variable as a typed jq string, and use the index operator `[]` in a single-quoted filter:

```bash
# Correct and injection-safe
value=$(jq -r --arg s "$svc" '.services[$s].image' "$CONFIG_FILE")
```

**Rule:** Never interpolate shell variables into jq filter strings. Always use `--arg name "$value"` (strings) or `--argjson name "$value"` (numbers/booleans/objects) and reference them as `$name` inside a single-quoted filter. This is both correct and injection-safe.

---

## Resource Lifecycle Contract (RLC)

See `governance/rlc.md` for the full definition. Bash-specific phase mappings:

| Phase | Bash idioms |
|---|---|
| **Allocation** | `mktemp`, opening file descriptors (`exec N>file`), acquiring a lock file |
| **Configuration** | `readonly` constants, argument parsing, environment variable validation |
| **Activation** | starting a background process (`&`), opening a connection, acquiring a lock |
| **Primary Use** | reading/writing file descriptors, calling external tools, piping data |
| **Deactivation** | signaling background processes (`kill $PID`), releasing locks |
| **Deallocation** | `trap 'cleanup' EXIT` — remove temp files, close file descriptors (`exec N>&-`), release locks |

**Common Bash failures:**
- Temporary files or lock files created without a `trap ... EXIT` cleanup — leaked on error exit
- Background processes (`&`) started without storing their PID — cannot be waited for or terminated
- File descriptors opened with `exec N>` never closed — descriptor leak
- Agent check: verify every `mktemp`, background process, and lock acquisition has a corresponding `trap 'cleanup' EXIT` handler

---

## [Add new lessons here — format: `[date] [context] Lesson. → Action.`]

-
