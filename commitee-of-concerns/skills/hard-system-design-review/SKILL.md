---
name: hard-system-design-review
description: Runs a brutal, multi-perspective system/software design or architecture review by dispatching three archetype subagents in parallel — the pragmatist (evolutionary, trade-off-driven), the minimalist (blunt, simplicity-obsessed), and the purist (SOLID/Clean Architecture, didactic) — then synthesizes their verdicts into one report. Use this whenever the user asks to review, critique, or get feedback on a system design, architecture doc, RFC, ADR, design proposal, or high-level code structure, especially if they ask for a "hard", "harsh", "no-BS", "thorough", or "multiple opinions" review, or explicitly mention wanting pragmatist/minimalist/purist-style feedback. Also trigger if the user asks to "roast my architecture" or wants to stress-test a design before committing to it.
---

# Hard System Design Review

Three deliberately different, opinionated reviewers look at the same design and are told to find its weak points and its strengths. Because they optimize for different things — The Pragmatist for evolvability and reversibility, The Minimalist for simplicity and data structures, The Purist for dependency direction and testability — running all three surfaces problems a single reviewer would miss, and disagreements between them are often more informative than any one verdict.

This skill depends on three project-level subagents already defined in `.claude/agents/`: `pragmatist`, `minimalist`, `purist`. Each already contains its own full persona and review methodology — this skill's job is orchestration, not re-explaining how to review.

## Step 1: Pin down what's being reviewed

You need a concrete artifact all three reviewers can independently read — they run as fresh subagents with no memory of this conversation, so nothing you and the user discussed earlier is visible to them unless it's in the artifact or the prompt you write.

- If the user points at a file, directory, or repo path, use that directly.
- If the user pastes a design doc inline in chat, write it to a file in the scratchpad directory first (e.g. `design-under-review.md`) so all three subagents can `Read` the same fixed text. Don't just paste it into each prompt from memory — write it once, point all three at the file, so there's no risk of paraphrasing it differently for each reviewer.
- If it's genuinely unclear what to review (no path, no pasted doc, ambiguous scope), ask the user before dispatching anything. Guessing wrong here wastes three subagent runs.

## Step 2: Dispatch all three reviewers in parallel

Call the Agent tool three times **in a single message** (one call each for `subagent_type: "pragmatist"`, `"minimalist"`, `"purist"`) so they run concurrently instead of one after another. Give all three the *same* input material and the *same* framing of what's being asked, so their reviews are actually comparable — differences in output should come from the persona, not from three different task descriptions.

Each prompt should be self-contained and include:
- What the design is for (one or two sentences of context — what problem it solves, what scale/constraints matter if known).
- Where to read it (exact path, or "read `<scratchpad-path>`").
- The ask, plain: review this design. Call out what's good. Find the weak points and explain why they're weak. Propose a concrete better approach for the weak points. (Each agent already knows *how* to do this in its own voice — you're just handing over the material and confirming the ask, not re-teaching the persona.)

Do not summarize or pre-digest the design for them — let each subagent read the real material itself via its own Read/Grep/Glob/Bash tools. A summary you write is a lossy filter that could hide exactly the thing one persona would have flagged.

## Step 3: Present all three reviews in full

Once all three come back, show each review under a clear heading, verbatim, in this order: The Pragmatist, The Minimalist, The Purist. Don't trim or paraphrase them down — the point of a hard review is getting the full, distinct take from each angle. Preserve each persona's voice as they wrote it.

## Step 4: Add a synthesis section

After all three full reviews, add a short **Synthesis** section:

- **Where they agree** — if two or three reviewers independently flagged the same weak point (even if they framed it differently — e.g. The Pragmatist calls it "a one-way door" and The Purist calls it "a DIP violation" for the same piece of the design), say so explicitly. Independent agreement across different value systems is a strong signal.
- **Where they conflict** — if their recommendations actually pull in different directions (e.g. The Minimalist wants to delete an abstraction The Pragmatist wants to keep for evolvability), name the conflict plainly and explain the real tension underneath it (simplicity now vs. optionality later, etc.) rather than picking a winner for the user.
- **Prioritized action list** — a short, ordered list of what to fix first, weighted toward points that multiple reviewers raised or that block testability/correctness, not just style preferences.

Keep the synthesis tight — a page of bullets, not a fourth review. Its job is to help the user act on three opinions at once, not to add a fourth opinion.
