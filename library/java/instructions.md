# Java — System Prompt Snippet

> Compose this with `docs/governance/system-prompts/base-system-prompt.md` for Java projects.

---

## Java-Specific Governance

- Target Java version: [INSERT — e.g., Java 21 LTS]
- Build tool in use: [INSERT — Maven | Gradle]
- Spring Boot version (if applicable): [INSERT]
- Testing framework: [INSERT — JUnit 5 | JUnit 4]
- Lombok in use: [INSERT — Yes | No]

## Rules

- Always use the project's specified Java LTS version. Do not assume a newer version is available.
- Do not mix Maven and Gradle conventions in the same project.
- Before any Spring Boot configuration, confirm the major version. Boot 2.x and 3.x conventions differ.
- Apply YAGNI to boilerplate: prefer records for immutable data, avoid generating unnecessary getters/setters/builders unless required.
- Verify all generated `pom.xml` or `build.gradle` changes with `mvn verify` or `gradle build` before marking complete.
- Specify JUnit 5 for all new test code unless JUnit 4 is explicitly required.
- Review exception handling strategy: checked vs. unchecked. Be consistent.

## Final Gate Additions

- [ ] `mvn dependency:tree` or `gradle dependencies` reviewed for conflicts
- [ ] Build produces expected artifact type
- [ ] Test framework and mocking library versions verified
