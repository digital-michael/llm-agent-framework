---
template-version: 1.0.0
override: replacement
---

# Best Practices

> Standing practices for every project and every language, distilled from lessons learned.
> Each is a rule, not a suggestion. They are loaded through the base system prompt (Process and Workflow).
> Project and domain profiles may add detail (see their own `best-practices.md`) but may not weaken a rule here.

---

## BP-1 — Tool drift: re-read tool definitions after a long gap

**Rule.** Tool definitions change between sessions. After a long gap, never write with a remembered parameter name.

**When it applies.**

- A session resumes after a few days or more, or after the context was summarized or compacted.
- The host reports updated tool definitions.
- A tool that was working starts returning results that differ from what was sent.

**How to apply.**

1. Reload the schema of every tool that **writes** (creates, updates, deletes, sends, publishes) before the first call. Reading tools can be checked on first use.
2. After the first write of each kind, **read it back** and confirm every field landed (a get, or a verbose list). Do this before batching.
3. Treat silence as no evidence. A tool that drops an unrecognised parameter without an error looks identical to one that accepted it.
4. If a tool silently drops unknown parameters, record a defect asking for a warning or an error. Do not rely on remembering the difference.

**Why.** A bulk create used a parameter name that the tool had since renamed. The unknown name was dropped silently, every item was created without its parent, and the loss was found only later from a verbose listing. A single read-back after the first create would have caught it.

---

## BP-2 — Read before fixing a failing build: intentional failures

**Rule.** A failing build or test is not proof of a defect. Some builds fail on purpose (for example a fail-closed build that must refuse to compile without an explicit variant selected). Establish that the failure is unintended before changing anything.

**How to apply (the agent).**

1. **Read** the build file header (Makefile, build script, CI definition) and **list the folder** before touching code.
2. Search for the marker `INTENTIONAL-FAILURE` (below) in the files you are about to change, and in the build file.
3. **Reproduce with the repository's own gate** (its documented build and test commands), not a bare language command. A bare `build ./...` can fail where the gate passes, and the reverse.
4. If the documentation says the failure is deliberate, stop. Do not add a fallback, default or stub to make it build. If the documentation and the behaviour disagree, ask.
5. **Report only what the check supports.** Say exactly which command ran and what it printed. "It has not built since X" requires having built with the real gate. A suite that skipped part of itself (a live tier that needs a key, reported as "pending") has not covered that part: run the repository's own gate, which supplies what the skipped tests need, and say what was skipped.

**How to apply (the author).** Mark every intentional failure so the next reader, human or agent, cannot mistake it for a bug. Use the greppable token `INTENTIONAL-FAILURE` at three levels:

| Where | What to write |
|---|---|
| The build file header | Which targets fail on purpose, under what condition, and which command is the real gate. |
| Every file that causes or depends on the failure | A comment at the point a reader would be tempted to add the missing piece. |
| A gate or test | A check that the failure still happens, so removing it is also caught. |

Comment form (use the language's comment syntax):

```text
INTENTIONAL-FAILURE: <what fails> when <condition>.
Do not add a default or fallback to make it build. Verify with: <the repository's gate command>.
```

**Why.** A fail-closed example was seen failing under a bare build command. It was "fixed" by adding a default file, which broke the repository's real gate and reached the public main branch. It was found sixteen days later. The Makefile header already said the failure was deliberate; it was not read.

---

## Promotion

These two were promoted to governance at the practitioner's direction (2026-11-23), ahead of the usual three-recurrence threshold, because both caused damage that reached shared or public state. Source entries: the retrospective of 2026-11-04 to 2026-11-22 in the originating project's lessons-learned file.

---

## BP-3 — Where a program keeps its files

**Rule.** A program writes only inside folders it names once, resolved in one place. Every file is one of three kinds, lives under one vendor folder, and is found by function, never by a product or brand name.

**The three kinds.** Decide by the test below, not by habit.

| Kind | Test | Examples |
|---|---|---|
| **config** | A person chose it and can edit it | model aliases, agent definitions, the machine's routes, non-secret machine settings |
| **data** | The user would miss it if it were deleted and the program restarted | databases, conversation history, secrets, credential profiles, **audit logs** |
| **state** | The program can recreate it | logs, job folders, caches, the list of open workspaces |

**The shape.** `<base>/<vendor>/<component>/...`, where the vendor is the **company** (it outlives every product name) and a component is named for what it does (`ledger`, `memory`, `secrets`, `logs`). A store used by several programs sits at the top of the vendor folder. A server's private files go under a group and the full program name (`mcp/local/...`), never an abbreviation. A product that may be split out later gets its own component folder and resolves it through the same helper, so leaving means changing one constant (or one root override), not code scattered through the programs.

**Platforms.** Choose one mapping per project and document it. Linux and macOS: the XDG folders (`~/.config`, `~/.local/share`, `~/.local/state`), each honouring its XDG variable. Windows: `%APPDATA%` for config, `%LOCALAPPDATA%` for data and state. Override order, first match wins: the specific file or folder variable, then a variable for the kind, then the XDG variable, then the default. Say plainly which platforms are **tested** and which are only written.

**Logs never go to `/tmp`** (it is cleared at reboot and readable by every local user). They go to the state folder, with a retention policy (rotation and clean-up) owned by whoever starts the service.

**How to apply.**

1. One resolver per language, with **one shared test table** that every language's tests use; no path built by hand anywhere else.
2. A check fails the build on a hand-typed home path or XDG folder outside the resolver.
3. Environment variable prefixes are the platform's interface, not its brand: keep them stable across renames.

**Why.** In one project the same folder rule was written seven times with small differences; the ledger database sat in a folder named for one of three programs that shared it; five services wrote logs to `/tmp`; and the audit logs were split across data and state.

---

## BP-4 — Configuration: layers, precedence, and what a repository may hold

**Rule.** Every setting has a **kind** and exactly one home. A repository holds defaults and templates only; nothing that belongs to one machine, one person or one secret is committed, and a check fails if it is.

| Kind | Home |
|---|---|
| product default | the repository |
| machine (paths, ports, memory limits, which models are installed) | the config folder |
| user (aliases, preferences) | the config folder or the host application's user settings |
| secret | the secrets folder (BP-5) |
| entitlement | the membership or licence data |
| state | the state folder |

**Precedence, weakest to strongest:** product default, machine, user, one request. The same order locally and on a remote host.

**How to apply.**

1. **A file a tool writes must not be tracked.** Do not hide it with a per-clone git flag (it protects one clone, not a fresh checkout). Put the file outside the repository and include it from there (a link plus a relative include), and keep an *empty default* in the repository if the consumer fails when the file is missing.
2. **Templates, not copies.** A service definition in the repository carries placeholders (`@HOME@`); the installer fills them in on the machine.
3. **Non-secret machine settings live in one environment file in the config folder, read by the programs themselves.** Order: real environment variable, then the file, then the default. A shell profile is not a dependency: a program started by a service manager must not need one.
4. Commands in shared configuration are bare names found on the service's `PATH`, not absolute paths into one person's home.

**Why.** A machine's model routes were committed and pushed because the file looked like pending work; the gateway configuration named one person's home folder; and three settings that every service needed lived only in a shell profile.

---

## BP-5 — Secrets

**Rule.** A secret is never in an environment variable that every process inherits, a shell profile, a repository, a log, a command line, or a chat. It lives in its own file, mode 0600, in a secrets folder, mode 0700.

**How to apply.**

1. One file per secret. The service manager's definition may hold what that service needs, and is itself 0600.
2. **Hand a secret over through the clipboard with an immediate clear**, or read it from the file inside the program. A person or an agent never prints it.
3. **A second copy is a leak.** Backups of a service definition that holds a key keep the key: delete them once the change is verified.
4. Host-managed secret stores (an editor's keychain-backed storage) belong to the **extension id**: a rename of the id loses them. This is one more reason identifiers carry no brand (BP-7).
5. A tool that must act as a person is gated by a token only that person reads; an intermediary may add the token on the person's behalf but never shows it to a model.

**Why.** The gateway key was exported from a shell profile (inherited by every program the user ran), copied into a backup of the service file, and lost when an extension id changed.

---

## BP-6 — Processes: services, restarts and logs

**Rule.** Treat a service manager's loaded definition as a **copy**. Changing the file changes nothing until the service is unloaded and loaded again.

**How to apply.**

1. To change a service's arguments or environment: edit the definition, **unload, wait until it is gone, load**. A "kick" or "restart" of the loaded job keeps the arguments it was loaded with. After any restart, verify the *running* process (its start time and its arguments), not the file.
2. **Wait on a condition with an upper bound** (the port answers, the health endpoint returns 200), never a fixed sleep.
3. Logs go to the state folder (BP-3). Decide rotation and retention when the service is added, not afterwards.
4. Service names are generated per platform from one internal id (launchd label, systemd unit, Windows service); path rules do not depend on them.
5. A status script compares what is **installed** with what is **built from source**, including shared dependencies: when a shared library changes, every program built from it is stale.
6. Start, stop and install scripts are safe to run twice.

**Why.** A gateway "restarted" after its definition was edited kept serving the old configuration, and the status script said the machine's routes were missing; the cause was visible only in the running process's arguments.

---

## BP-7 — Rename-proof identifiers

**Rule.** Identifiers carry no brand name. Package and extension ids, setting prefixes, command ids, class names, icon files, folder names and environment prefixes use functional names (`datum-chat`, `datum.*`, `DatumError`). The brand exists in **one constant** and in generated text.

**How to apply.**

1. **One constant, one command.** Changing the constant and running the generator updates every display string (README, guides, text tables, command titles).
2. **A rename tool**, dry run by default, that replaces the three cases of a name, moves files and folders that contain it, protects named tokens (a repository or branch name, a third-party product that shares the word) and skips dated history and lockfiles.
3. **A retired-names check** that fails when a retired name appears in a current file. A hyphenated token is a different name; one line can be marked as a deliberate mention.
4. **Retired-name tests are written by hand.** A blanket text replacement run over the tests that list retired names rewrites them into nonsense (`(Synod|Synod)`) that passes.
5. Write down what **no search finds**: repository and project names, stored secrets (BP-5), titles of earlier tickets, trademark clearance, the meaning of the name in each shipped language, and publishing.

**Why.** One product was renamed three times in a day. The first rename touched 2,166 mentions in 168 files because identifiers carried the name; the third touched display text only.

---

## BP-8 — Migrations of a user's files

**Rule.** Moving a person's files is done by a script that can be run twice, shows what it would do first, and cannot lose a file.

**How to apply.**

1. **Dry run by default.** `--apply` (or the named verb) changes things; the dry run prints every move.
2. **Idempotent.** A second run says "nothing to do" and changes nothing.
3. **Stop the writers first**, then move, then start them, then verify with a script. A database in write-ahead mode is moved with its own backup interface and an integrity check, never with `cp` while it is open.
4. **Move, do not copy; keep the old folder** (renamed with a date) until the person deletes it. Verify a checksum or an integrity check **before** removing the source.
5. **Refuse on conflict and change nothing** when two places hold different data.
6. **No long-lived fallback that reads the old path.** The migration runs once; after it the old paths are not read.
7. **Back up irreplaceable data first** and say where the backup is.
8. **Test on scratch copies on every supported operating system** (a container for Linux), including the second run, a conflict, and a hand-edited file. Name any platform that is only written, not tested.
9. Leave the person's own files (a shell profile) changed only with a backup and a report.

**Why.** A machine's routes were moved out of a repository with a script that was run twice, refused to overwrite a route that differed, kept the old service definition beside the new one, and restarted the service by unloading and loading it (BP-6).
