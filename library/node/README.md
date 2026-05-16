# Node.js — Overview

Node.js governance overlaps significantly with JavaScript but adds runtime-specific concerns: process management, environment configuration, server framework conventions, and deployment artifact behavior. LLM Agents are generally capable Node.js collaborators; the main governance needs are environment isolation, configuration management, and server lifecycle handling.

## Active Files

- `governance-overlay.md` — Node.js-specific lessons and pitfalls
- `instructions.md` — Node.js additions for the base system prompt

## Key Concerns

- Environment configuration: secrets and environment variables must never appear in code; `.env` and config management need explicit governance
- Server lifecycle: startup, shutdown, and error handling must be explicit
- Node.js version: LTS vs current; specify which is required
- Framework: Express, Fastify, Hono, and others have different conventions; specify before any server code is generated
