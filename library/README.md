# Library — Technology-Specific Governance Overlays

> Language and technology-specific lessons, pitfalls, and system prompt additions.
> Each subdirectory covers one technology.

---

## Structure

Each technology directory contains:

| File | Contents |
|---|---|
| `README.md` | Overview and notes for this technology |
| `governance-overlay.md` | Technology-specific lessons learned and pitfalls |
| `instructions.md` | Agent instructions to compose with the base system prompt |

---

## Technologies

| Directory | Technology |
|---|---|
| `java/` | Java (JVM, Maven/Gradle, Spring ecosystem) |
| `go/` | Go (modules, standard library, idiomatic patterns) |
| `javascript/` | JavaScript (browser and general JS, ESM/CJS) |
| `node/` | Node.js (runtime-specific, server-side JS, npm ecosystem) |
| `bash/` | Bash scripting (portability, safety, POSIX) |
| `python/` | Python (packaging, typing, async, common frameworks) |
| `rust/` | Rust (ownership, Cargo, error handling, async) |

---

## How to Use

1. Identify the technologies in your project
2. Compose the relevant `instructions.md` files with the base system prompt
3. Review `governance-overlay.md` before starting any assignment in that technology
4. Add new lessons to `governance-overlay.md` at each retrospective

---

## Adding a New Technology

1. Create a new subdirectory: `docs/library/<technology>/`
2. Copy the structure from an existing entry
3. Populate with technology-specific knowledge from your team's experience
