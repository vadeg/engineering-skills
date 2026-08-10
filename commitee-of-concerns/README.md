# commitee-of-concerns (coc)

A committee of three deliberately opinionated reviewers who look at the same system design and are told to find its weak points and its strengths — because they optimize for different things, running all three surfaces problems a single reviewer would miss.

## Install

```
/plugin install commitee-of-concerns@engineering-skills
```

## Skills

### `hard-system-design-review`
Runs a brutal, multi-perspective review of a system design, architecture doc, RFC, or ADR by dispatching three archetype subagents **in parallel** — the pragmatist, the minimalist, and the purist — then synthesizes their verdicts (agreements, conflicts, prioritized action list) into one report.

Triggers on: "hard/harsh/no-BS review", "roast my architecture", "multiple opinions on this design".

Depends on subagents: [`pragmatist`](agents/pragmatist.md), [`minimalist`](agents/minimalist.md), [`purist`](agents/purist.md).

→ [`skills/hard-system-design-review/SKILL.md`](skills/hard-system-design-review/SKILL.md)

## Agents

Dispatched by `hard-system-design-review` via the `Agent` tool (`subagent_type`); not meant to be invoked standalone, though nothing stops you from calling one directly for a single-perspective review.

| Agent | Role |
|---|---|
| [`pragmatist`](agents/pragmatist.md) | Evaluates a design purely on trade-offs, coupling, evolvability, and reversibility. |
| [`minimalist`](agents/minimalist.md) | Blunt, simplicity-obsessed — flags over-engineering and unnecessary abstraction. |
| [`purist`](agents/purist.md) | SOLID / Clean Architecture / dependency-direction / testability, didactic in tone. |

## Dependency map

```
hard-system-design-review → pragmatist, minimalist, purist   (parallel dispatch)
```

## Structure

```
.claude-plugin/plugin.json
skills/
  hard-system-design-review/SKILL.md
agents/
  pragmatist.md
  minimalist.md
  purist.md
```
