# walking-skeleton

Validates a task breakdown against the walking-skeleton approach — a thin, real, end-to-end slice through every architectural layer, built and deployed first, before feature work.

## Install

```
/plugin install walking-skeleton@engineering-skills
```

## Skills

### `review-tasks-breakdown`
Validates a task breakdown, `tasks.md`, sprint backlog, or phased roadmap against the walking-skeleton approach.

Triggers on: "walking skeleton", "thin slice", "vertical slice first", "does this tasks.md build things in the right order", "review my task breakdown before we start building".

Depends on subagent: [`walking-skeleton-validator`](agents/walking-skeleton-validator.md).

→ [`skills/review-tasks-breakdown/SKILL.md`](skills/review-tasks-breakdown/SKILL.md)

## Agents

Dispatched by `review-tasks-breakdown` via the `Agent` tool (`subagent_type`); not meant to be invoked standalone, though nothing stops you from calling it directly.

| Agent | Role |
|---|---|
| [`walking-skeleton-validator`](agents/walking-skeleton-validator.md) | Scores a task breakdown against the walking-skeleton pattern and its common failure modes. |

## Dependency map

```
review-tasks-breakdown → walking-skeleton-validator
```

## Structure

```
.claude-plugin/plugin.json
skills/
  review-tasks-breakdown/SKILL.md
agents/
  walking-skeleton-validator.md
```
