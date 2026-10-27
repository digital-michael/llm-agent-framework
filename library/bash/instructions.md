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
- **Operator-facing text is a keyed catalogue (D-041).** Text a person reads (a screen, a message, a dialog, a command title, a setting's description) is defined once as a keyed message with named placeholders, not written inline: it can then be renamed and translated. Whole messages, no joined fragments; plural and select forms inside the message, by the locale's rules; product and company names are placeholders fed from one brand table; numbers and dates are formatted by the locale; one place chooses the language, with English as the fallback. Text a model reads, log events, ids and text that callers or tests match are not catalogue text. A test lists the migrated files and fails if a hand-typed sentence returns, and a pseudo-locale test shows what bypasses the catalogue. Mechanism here: one sourced `messages.sh` of named strings used with `printf`; a script's operator output is written from it, not inline.

## Final Gate Additions

- [ ] `set -euo pipefail` present in all scripts
- [ ] All variables quoted
- [ ] No `eval` with external input
- [ ] Input validation present
- [ ] `shellcheck` passes (if configured)
- [ ] Operator-facing text is in the catalogue; no hand-typed sentence in a migrated file (D-041)
