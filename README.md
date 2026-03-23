# dev-skills

A curated set of Claude Code skills for personal software development projects.

Designed for:
- **Go** — backend services and CLI tools
- **Python** — data analysis and scripting
- **TypeScript / Next.js** — frontend applications

## Skills

| Skill | Description |
|---|---|
| `dev-brainstorm` | Validate ideas and design solutions before writing any code |
| `dev-plan` | Write a detailed implementation plan saved to `docs/plans/` |
| `dev-do` | Execute an approved plan task by task with test validation |
| `dev-tdd` | Implement features using RED-GREEN-REFACTOR discipline |
| `dev-debug` | Investigate bugs by finding root causes first |
| `dev-review` | Run a 4-perspective code review in parallel |
| `dev-commit` | Organize changes into clean, atomic Conventional Commits |
| `dev-explore` | Understand an unfamiliar codebase through parallel exploration |

## Typical workflows

**New feature:**
```
/dev-brainstorm → /dev-plan → /dev-do → /dev-review → /dev-commit
```

**Bug fix:**
```
/dev-debug → /dev-commit
```

**Starting in unfamiliar code:**
```
/dev-explore → /dev-plan → /dev-do
```

**Quick implementation (small change):**
```
/dev-tdd → /dev-commit
```

## Installation

### Claude Code

```bash
# Clone and symlink each skill into your Claude skills directory
git clone https://github.com/phbpx/dev-skills
cd dev-skills

for skill in dev-*/; do
  cp -r "$skill" ~/.claude/skills/
done
```

Or manually copy the folders you want into `~/.claude/skills/`.

### Claude.ai

1. Zip the skill folder you want (e.g. `dev-commit/`)
2. Open Claude.ai → Settings → Capabilities → Skills → Upload skill

## Philosophy

These skills are intentionally lightweight. They enforce discipline (TDD, root-cause debugging, atomic commits) without enterprise ceremony (multi-tenant gates, mandatory OpenTelemetry, 11-step pipelines).

Each skill works standalone — no dependencies on other skills.
