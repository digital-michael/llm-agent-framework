# Personal Profile — Templates

> Copy this directory's contents to your private personal profile repo and fill in each file.
> This repo should be **private** — it contains personal working preferences and collaboration style.
> Never commit personal profile content back to the infrastructure repo.

---

## Files in This Directory

| File | What to Fill In |
|---|---|
| `context-protocol.md` | Your preferred default profile, session declaration preferences |
| `collaboration-preferences.md` | Your autonomy level defaults, LLM collaboration preferences |
| `collaboration-patterns.md` | Your personal collaboration strengths and failure modes |
| `roles-guide.md` | How you prefer the agent to adapt to your working style |

---

## Setup

```bash
# Copy templates to your personal profile repo location
cp -r ~/Projects/infrastructure/templates/personal ~/Projects/personal/your-name
cd ~/Projects/personal/your-name
git init
# Fill in each file, then commit
git add . && git commit -m "Initialize personal profile"
```

Reference from each project's `.llm-framework.yml`:
```yaml
personal: ~/Projects/personal/your-name
```

---

## Template Version

When infrastructure personal templates are updated, review the changes and decide which to adopt. The `template-version` field in each file records which version you initialized from.
