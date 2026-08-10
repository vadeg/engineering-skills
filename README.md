# Engineering Skills

A personal [Claude Code](https://claude.com/claude-code) plugin marketplace of engineering skills — prototype documentation and hard multi-perspective design/plan reviews.

## Installing this marketplace

From a local checkout:

```
/plugin marketplace add /path/to/engineering-skills
```

Once this repo is pushed to GitHub, team members can instead run:

```
/plugin marketplace add <owner>/engineering-skills
```

## Plugins

| Plugin | Description |
|---|---|
| [`prototype-decomposition`](prototype-decomposition/README.md) | `decompose-prototype` skill — turns a UI prototype into a dated docs folder built from real screenshots. |
| [`walking-skeleton`](walking-skeleton/README.md) | `review-tasks-breakdown` skill, plus the `walking-skeleton-validator` subagent. |
| [`commitee-of-concerns`](commitee-of-concerns/README.md) (coc) | `hard-system-design-review` skill, dispatching the pragmatist/minimalist/purist subagents in parallel. |

Install a plugin with:

```
/plugin install prototype-decomposition@engineering-skills
/plugin install walking-skeleton@engineering-skills
/plugin install commitee-of-concerns@engineering-skills
```

## Repo layout

```
.claude-plugin/marketplace.json   # marketplace manifest (this file lists the plugins below)
prototype-decomposition/           # plugin — see its own README for details
  .claude-plugin/plugin.json
  skills/
walking-skeleton/                  # plugin — see its own README for details
  .claude-plugin/plugin.json
  skills/
  agents/
commitee-of-concerns/              # plugin — see its own README for details
  .claude-plugin/plugin.json
  skills/
  agents/
```

## Adding a new plugin

1. Create a new top-level directory with its own `.claude-plugin/plugin.json`, `skills/`, and (if needed) `agents/`.
2. Add an entry to `.claude-plugin/marketplace.json`'s `plugins` array pointing at it (`"source": "./<dir>"`).
3. Give it its own `README.md` following the pattern in `commitee-of-concerns/README.md`.