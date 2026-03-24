# dev-skills

A curated set of language-agnostic Claude Code skills for software development projects. Each skill detects the project's language and stack automatically and applies idiomatic practices.

## Skills

| Skill | Description |
|---|---|
| `dev-brainstorm` | Validate ideas and design solutions before writing any code |
| `dev-plan` | Write a detailed implementation plan saved to `docs/plans/` |
| `dev-plan-issue` | Read a GitHub issue and produce an implementation plan from it |
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

**From a GitHub issue:**
```
/dev-plan-issue 42 → /dev-do → /dev-review → /dev-commit
```

**Quick implementation (small change):**
```
/dev-tdd → /dev-commit
```

## Installation

### Via Claude Code marketplace

Add this to your `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "phbpx": {
      "source": {
        "source": "github",
        "repo": "phbpx/dev-skills"
      }
    }
  },
  "enabledPlugins": {
    "dev-skills@phbpx": true
  }
}
```

### Manual install (Claude Code)

```bash
git clone https://github.com/phbpx/dev-skills
cd dev-skills
for skill in dev-*/; do
  cp -r "$skill" ~/.claude/skills/
done
```

### Claude.ai

1. Download or clone this repository
2. Zip the skill folder you want (e.g. `dev-commit/`)
3. Open Claude.ai → Settings → Capabilities → Skills → Upload skill

## Philosophy

Lightweight discipline without enterprise ceremony. Each skill enforces a good practice (TDD, root-cause debugging, atomic commits) but skips multi-tenant gates, mandatory telemetry, and 11-step pipelines.

Each skill works standalone — no dependencies on other skills.
