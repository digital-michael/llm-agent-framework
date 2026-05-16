# Bash — Overview

LLM Agents can generate useful Bash scripts but require strict governance around safety, portability, and error handling. Without explicit constraints, generated scripts are often fragile: missing error guards, unquoted variables, and non-portable constructs are common failure modes.

## Active Files

- `governance-overlay.md` — Bash-specific lessons and pitfalls
- `instructions.md` — Bash additions for the base system prompt

## Key Concerns

- Error handling: scripts must exit on error by default; unguarded failures are silent corruption risks
- Quoting: unquoted variables are a leading cause of script failures with spaces and special characters
- Portability: Bash-specific features vs. POSIX sh; specify the target shell
- Security: scripts that accept external input must validate it; command injection is a real risk
