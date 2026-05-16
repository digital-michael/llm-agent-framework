# Java — Governance Overlay

> Java-specific lessons learned and pitfalls.
> Update at every retrospective involving Java work.

---

## Dependency Management

- Always specify the Java LTS version target. LLM Agents default to what they were trained on, which may not match your project.
- Maven and Gradle are not interchangeable. Confirm which build tool is in use before any dependency changes.
- When using Spring Boot, verify the Spring Boot major version before generating any configuration — Boot 2.x and 3.x have significant differences (Jakarta EE namespace, dependency names).
- Check for dependency version conflicts using `mvn dependency:tree` or `gradle dependencies` before marking a unit complete.

## Code Generation Patterns

- LLM Agents generate verbose Java by default. Apply the YAGNI check: getter/setter methods, builders, and DTOs are often over-generated.
- Prefer records (Java 16+) for immutable data carriers over manually generated POJOs.
- Specify whether Lombok is in use before any model generation. Mixing Lombok and manual boilerplate creates inconsistency.
- LLM Agents sometimes mix checked and unchecked exception handling inconsistently. Review exception strategy at the final gate.

## Testing

- Specify JUnit 4 vs JUnit 5 explicitly — the agent will default to one and may be wrong.
- Mockito and JUnit 5 require specific dependency combinations; verify the test configuration compiles before marking tests done.
- Spring Boot test slice annotations (`@WebMvcTest`, `@DataJpaTest`) are useful but require understanding of what they do and do not load. Verify test scope.

## Build and Artifacts

- Always verify the build produces the expected artifact type (JAR, WAR, executable JAR) before the final gate.
- Executable JAR (fat JAR / Spring Boot JAR) packaging is not always the default. Confirm.
- Multi-module Maven/Gradle projects require explicit module awareness in every generated `pom.xml` or `build.gradle`. Verify parent/child relationships.

---

## [Add new lessons here — format: `[date] [context] Lesson. → Action.`]

-
