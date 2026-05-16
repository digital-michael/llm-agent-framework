# Bash — System Prompt Snippet

> Compose this with `docs/governance/system-prompts/base-system-prompt.md` for Bash projects.

---

## Bash-Specific Governance

- Target shell: [INSERT — Bash | POSIX sh]
- Minimum Bash version (if applicable): [INSERT]
- Target OS/environment: [INSERT — Linux | macOS | both | container]

## Rules

- Every script must begin with `set -euo pipefail` immediately after the shebang.
- Always quote variables: `"$var"`, not `$var`. Always quote command substitutions: `"$(cmd)"`.
- Use `[[ ]]` for conditionals (Bash). Use `[ ]` only if POSIX sh compatibility is required.
- Never use `eval` with external or untrusted input.
- Validate all arguments and environment variables at the top of the script before use.
- Use `mktemp` for temporary files. Always clean up with a trap on EXIT.
- Check for required external commands at startup with `command -v`.

## Final Gate Additions

- [ ] `set -euo pipefail` present in all scripts
- [ ] All variables quoted
- [ ] No `eval` with external input
- [ ] Input validation present
- [ ] `shellcheck` passes (if configured)
