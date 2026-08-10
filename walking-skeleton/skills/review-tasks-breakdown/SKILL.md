---
name: review-tasks-breakdown
description: Validates a task breakdown, implementation plan, tasks.md, sprint backlog, or phased roadmap against the walking-skeleton approach — a thin, real, end-to-end slice through every architectural layer built and deployed first, before any layer-by-layer or feature-by-feature work. Use this whenever the user wants their plan checked for this, explicitly mentioning "walking skeleton", "thin slice", "vertical slice first", "avoid big-bang integration", or asking things like "does this tasks.md build things in the right order", "will we find out too late that the pieces don't talk to each other", "is phase 1 too big/too small", or "review my task breakdown before we start building" — even if they never say "walking skeleton" by name. Also trigger proactively right after a tasks.md or phased plan has just been generated (e.g. by speckit-tasks) if the user asks for a sanity check on sequencing or risk before implementation starts.
---

# Review Tasks Breakdown

Checks a task breakdown against one question: **does the plan prove the architecture actually holds together — real layers, real data, deployed — before it spends effort on features?** Most broken plans don't fail because any individual task is wrong; they fail because integration risk gets pushed to the end (build every layer fully, then wire them together) or because the "first slice" is secretly a full feature rather than the thinnest possible connectivity proof.

This skill depends on the `walking-skeleton-validator` subagent already defined in `.claude/agents/`. That agent holds the full methodology — what a walking skeleton is, the five common failure modes, and how to score a plan against them. This skill's job is just getting it the right material and presenting what comes back.

## Step 1: Pin down what's being reviewed

The validator runs as a fresh subagent with no memory of this conversation — it only sees what you hand it, so get this right before dispatching.

- If the user points at a file or directory (a `tasks.md`, a plan doc, a project board export), use that path directly.
- If the user pastes a task list or plan inline in chat, write it to a file in the scratchpad directory first (e.g. `task-breakdown-under-review.md`), so the subagent reads fixed text via its own tools rather than a paraphrase of what was pasted.
- Look for sibling context the validator will want: a `spec.md`, `plan.md`, design doc, or README near the tasks file that describes the system's actual architecture (is there a UI? a datastore? third-party integrations? a deploy target?). You don't need to read these yourself — just note their paths so you can point the subagent at them. The validator needs to know what the real layers are before it can judge whether the first phase touches all of them.
- If it's genuinely unclear what document to review (no path, nothing pasted, ambiguous scope), ask the user rather than guessing — a review of the wrong artifact wastes the run.

## Step 2: Dispatch the validator

Call the Agent tool with `subagent_type: "walking-skeleton-validator"`. Give it a self-contained prompt with:

- What the system is, in one or two sentences, if you know it (helps the validator sanity-check what "every layer" means here).
- The exact path(s) to the task breakdown, and the exact path(s) to any sibling spec/plan/design doc for architecture context.
- The ask, plainly: review this breakdown for whether it follows the walking-skeleton approach, using the criteria and failure modes it already knows.

Don't pre-summarize the plan's structure yourself — let the subagent read the real file and form its own judgment about phase boundaries and task ordering. A summary you write could smooth over exactly the sequencing problem the validator would have caught.

## Step 3: Present the review

Show the validator's report in full — architecture layers identified, candidate skeleton phase, findings, verdict, and reorder suggestions — under a `## Walking Skeleton Review` heading. Don't trim or soften it.

This skill only critiques and suggests; it never rewrites the task file itself (the validator has no write access, by design — reviews should stay opinions the user acts on deliberately, not silent edits). If the user wants the plan actually restructured after seeing the review, do that as a normal follow-up edit in this conversation, informed by the validator's specific suggestions.
