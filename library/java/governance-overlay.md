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

## Resource Lifecycle Contract (RLC)

See `governance/rlc.md` for the full definition. Java-specific phase mappings:

| Phase | Java idioms |
|---|---|
| **Allocation** | constructor, factory method, Spring `@Bean` / DI container |
| **Configuration** | constructor args, builder pattern, `@Value` / `application.properties` |
| **Activation** | `@PostConstruct`, `start()`, `init()`, `open()`, application context refresh |
| **Primary Use** | method calls against interface; verify `@Scope` (singleton vs prototype) |
| **Deactivation** | `@PreDestroy`, `stop()`, `close()`, `shutdown()` |
| **Deallocation** | GC + `AutoCloseable`; Spring container manages lifecycle for beans |

**Common Java failures:**
- `@PostConstruct`/`@PreDestroy` hooks missing on Spring beans that manage I/O resources
- `AutoCloseable` resources not used in try-with-resources — deallocation not guaranteed on exception
- Configuration injected after activation (`@Value` fields used before context is fully initialized)
- Prototype-scoped beans that acquire resources but have no defined deallocation — Spring does not destroy them
- Agent check: verify every `Closeable`/`AutoCloseable` resource is in a try-with-resources block or has an explicit `@PreDestroy`

---

## [Add new lessons here — format: `[date] [context] Lesson. → Action.`]

-
