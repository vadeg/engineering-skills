# prototype-decomposition

Turns a UI prototype into a dated documentation folder built from real screenshots of the actual prototype, never redrawn approximations.

## Install

```
/plugin install prototype-decomposition@engineering-skills
```

## Skills

### `decompose-prototype`
Turns a UI prototype — a live web app, a Claude Design project link, a local source file with no live render, or shared screenshots — into a dated docs folder (`user-flow.md` + `mockups.md`) built from real screenshots of the actual prototype.

Triggers on: "map out the user flow", "document the happy path and edge cases", "turn this prototype into mockups", "what are the requirements for this screen".

No dependencies — self-contained.

→ [`skills/decompose-prototype/SKILL.md`](skills/decompose-prototype/SKILL.md)

## Structure

```
.claude-plugin/plugin.json
skills/
  decompose-prototype/SKILL.md
```
