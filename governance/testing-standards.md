---
template-version: 1.0.0
override: replacement
---

# Testing Standards

> Adapted from: `em-lai-project/docs/library/team/Testing Best Practices.md`
> General-purpose testing standards for LLM-assisted software development.

---

## Scope

These standards cover unit testing, integration testing, and coverage requirements across development, staging, and production-like environments. The goal is fast feedback locally, realistic validation in staging, and safe verification in production.

---

## Test Strategy by Environment

| Environment | Purpose | Recommended Test Types | Key Rules |
|---|---|---|---|
| **Local / Development** | Fast developer feedback and debugging | Unit tests, small integration tests, linting | Run quickly; isolate dependencies; use mocks/fakes; avoid shared infrastructure |
| **Staging** | Validate real integrations before release | Integration tests, smoke tests, API tests, deployment checks | Use production-like config, real service wiring, disposable data |
| **Production** | Verify health; detect regressions safely | Smoke checks, synthetic probes, post-deploy validation | Keep tests read-only, low-risk, and narrowly scoped |

---

## Unit Testing Standards

- Write one behavior per test; keep tests independent
- Prefer deterministic tests: no network calls, no real clock dependence, no external service calls
- Use arrange-act-assert structure
- Mock boundaries (HTTP, queues, databases, filesystem) only at the edge — not inside core logic
- Keep test files cohesive: one module or one behavior family per test file
- Name tests descriptively: a failing test name should tell you what broke

---

## Integration Testing Standards

- Test real interactions between services, databases, queues, and auth boundaries
- Run integration tests in disposable environments, not against shared developer resources
- Use stable fixtures, seeded data, and explicit cleanup
- Fail fast on contract drift, schema changes, or incompatible configuration
- Integration tests are the primary safety net for LLM-generated changes — do not defer them

**On LLM-assisted refactoring:** Do not begin any refactoring assignment until integration test golden-path coverage meets 95%+. If it doesn't, creating those tests is the first assignment.

---

## Coverage Standards

Track both **code coverage** (lines executed) and **test coverage** (behaviors verified) — neither alone is sufficient.

| Area | Minimum Expectation | Notes |
|---|---|---|
| Core business logic | High — including branches | Must cover nominal, boundary, and failure paths |
| Integration boundaries | Moderate to high | Focus on real contract behavior |
| UI / orchestration glue | Moderate | Favor smoke tests and behavior checks |
| Generated / thin wrappers | Lower acceptable | Exempt only with explicit justification |

**Coverage targets for LLM-assisted development:**
- Integration tests (golden path / must-always-work): **95%+**
- Unit tests: **80%+**

These are not vanity metrics — they are the minimum thresholds at which LLM-generated changes can be made safely.

---

## Priority Order

1. **Integration tests** — first priority for all new and existing codebases
   - Create before refactoring begins; these are the safety net
   - Use as use cases, then as integration tests, then as automated regression tests

2. **Unit tests** — second priority
   - Focus on logic boundaries, edge cases, error paths
   - Cover mutation and conditional logic with branch coverage

3. **Extended testing** (instruct the agent):
   - Log output validation
   - Smoke tests for artifact launch
   - Automated integration replays via REST, gRPC, IPC, or equivalent

---

## Local Workflow

- Run unit tests first, then targeted integration tests
- Use fast markers or test selection to keep feedback loops short
- Run coverage locally on changed modules, not the entire repository
- Make failures actionable: clear assertions, descriptive test names

---

## Staging Workflow

- Deploy the exact build artifact that will be promoted to production
- Run smoke tests immediately after deployment
- Run integration and API tests against live dependencies
- Validate configuration, secrets wiring, permissions, readiness

---

## Production Workflow

- Limit to safe checks: health endpoints, synthetic transactions, canary validations
- Avoid destructive tests or tests that mutate real data
- Prefer observability signals (error rate, latency, readiness) over direct probing
- Treat production validation as a guardrail, not a substitute for staging

---

## Testing and the LLM Agent

- Instruct the agent to propose additional test scenarios beyond what was specified
- The agent should not mark any unit of work complete without corresponding tests
- Manual testing by the technologist and regular design reviews — especially early — catch structural issues before they become expensive
- Unhandled test failures must not be committed; do not modify tests to force passage
- If a test cannot be made to pass, document the blocker and escalate
