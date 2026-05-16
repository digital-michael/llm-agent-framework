# Team Profile — Templates

> Copy this directory's contents to your team profile repo and fill in each file.
> The team profile repo is shared by all team members and all team projects.
> Visibility (public or private) is determined by your organization's needs.
> Never commit team profile content back to the infrastructure repo.

---

## Files in This Directory

| File | What to Fill In |
|---|---|
| `collaboration-patterns.md` | Your team's validated collaboration dynamics |
| `governance-overlay.md` | Team-specific governance additions and rule overrides |

---

## Setup

```bash
# Copy templates to your team profile repo location
cp -r ~/Projects/infrastructure/templates/team ~/Projects/domains/my-team
cd ~/Projects/domains/my-team
git init
# Fill in each file, then commit
git add . && git commit -m "Initialize team profile"
```

Reference from each project's `.llm-framework.yml`:
```yaml
team: ~/Projects/domains/my-team
```

---

## Override Mechanics

Files in the team repo with a matching path override the infrastructure version.
The `override` field in each file's front matter controls how:

- `override: replacement` — the entire infrastructure file is replaced
- `override: extend` — only named/numbered items in this file are replaced; the rest of the infrastructure version remains in effect

---

## Team vs. Personal

The team profile is for shared, agreed-upon overrides. Personal preferences go in each individual's personal profile repo. Personal always takes priority over team when both are present.
