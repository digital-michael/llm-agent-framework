# Java — Overview

LLM Agents are capable Java collaborators but require guidance on modern Java idioms, build tool behavior, and framework conventions. Verbosity and boilerplate generation are strengths; architectural discipline and dependency management need active governance.

## Active Files

- `governance-overlay.md` — Java-specific lessons and pitfalls
- `instructions.md` — Java additions for the base system prompt

## Key Concerns

- Dependency management: Maven and Gradle have different resolution strategies; LLM Agents sometimes mix conventions
- Spring Boot: conventions over configuration can lead to over-generated boilerplate without explicit guidance
- Version alignment: Java LTS versions, Spring Boot major versions, and library compatibility require verification
- Testing: JUnit 5 vs JUnit 4 conventions differ significantly; specify version explicitly
