---
template-version: 1.0.0
override: replacement
---

# Engineering Principles

> Adapted from: `em-lai-project/docs/library/company/Engineering Best Practices.md`
> Core software engineering principles for LLM-assisted development.

---

## Purpose

These principles define the baseline quality standards the agent must apply to all code it generates, modifies, or reviews. They are not aspirational — they are the minimum bar for production-bound work.

---

## Core Principles

### SOLID

Apply SOLID principles to all object-oriented and component-based designs:

| Principle | Rule |
|---|---|
| **Single Responsibility** | Each class/module/function has one reason to change. If you can describe a class's purpose using "and", it has too many responsibilities. |
| **Open/Closed** | Design for extension without modification. New behavior should be added by adding new code (implementations, extensions, configurations), not by editing existing code. |
| **Liskov Substitution** | Subtypes must be substitutable for their base types without breaking correctness. Override behaviors that preserve contracts; don't override to specialize exceptions. |
| **Interface Segregation** | Clients should not depend on interfaces they don't use. Prefer narrow, role-specific interfaces over broad, multi-purpose ones. |
| **Dependency Inversion** | High-level modules must not depend on low-level modules; both depend on abstractions. Inject dependencies; don't hard-code them. |

**Symptom to watch for:** A class that imports many other classes or has many distinct responsibilities is a Single Responsibility violation. When the agent detects this pattern in existing code, note it — and if the work scope includes refactoring, fix it.

---

### DRY (Don't Repeat Yourself)

Every piece of knowledge must have a single, authoritative representation in the system.

- Extract logic shared across two or more places
- Extract constants and configuration values; don't hard-code them inline
- Document *why* a piece of logic exists (its intent), not *what* it does (which the code already shows)

**Caveat for LLM-generated code:** Avoid premature abstraction. Two call sites that look similar are not always the same thing. Apply DRY when the duplication is *semantic* (same concept), not just syntactic (same tokens).

---

### GRASP

GRASP (General Responsibility Assignment Software Patterns) guides responsibility allocation across components:

| Pattern | Application |
|---|---|
| **Information Expert** | Assign responsibility to the class that has the information required to fulfill it |
| **Creator** | Assign creation responsibility to the class that aggregates or closely uses the created type |
| **Controller** | Route incoming requests through a dedicated controller; don't process them in unrelated classes |
| **Low Coupling** | Minimize dependencies between components; maximize the ability to change one without changing another |
| **High Cohesion** | Keep related responsibilities together; separate unrelated ones |
| **Polymorphism** | Use polymorphism to handle type-based variation rather than if/switch chains |
| **Pure Fabrication** | If no natural class owns a responsibility, create a dedicated service/utility class |
| **Indirection** | Insert intermediary objects to decouple otherwise-direct dependencies |
| **Protected Variations** | Identify volatile or unstable interfaces and wrap them to protect the rest of the system from change |

---

## Architectural Standards

### Separation of Concerns

Every architectural unit — service, module, file — should have a distinct, named purpose. Mixing concerns produces:
- Code that is hard to test because it does too much
- Components that change for multiple reasons, multiplying regression risk
- Systems that are hard to understand because you can't know what any one part is responsible for

**In LLM-assisted development:** Watch for scope creep. The agent is efficient at generating code but may mix concerns when producing scaffolding or "obvious" setup code. Review generated code for SoC violations as part of the quality gate.

### Loose Coupling

Components should communicate through interfaces, not implementations:
- Define contracts at module boundaries; code to the contract
- Inject dependencies rather than instantiating them inside the dependent
- Use abstractions at integration points (queues, databases, HTTP) to enable replacement and testing

### High Cohesion

A cohesive component contains everything needed to fulfill its responsibility — and nothing else:
- Group by behavior, not by type (prefer a "UserOnboarding" module over a "StringUtils" grab-bag)
- If a module constantly imports from many other modules, it may lack cohesion
- Cohesion and coupling are complementary — increase both together

---

## LLM Agent Application Notes

- The agent should check generated code against SOLID before presenting it, not after
- When the agent detects a SOLID or SoC violation in *existing* code that is in scope for modification, it should note it even if fixing it is out of scope
- Refactoring suggestions outside the current task scope should be noted, not executed
- Quality standards apply equally to test code as to production code
