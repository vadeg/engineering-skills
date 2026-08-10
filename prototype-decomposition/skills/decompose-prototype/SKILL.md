---
name: decompose-prototype
description: Turns a UI prototype -- a live web app you can browse, a Claude Design project link, a local prototype source file on disk, or screenshots/images you share -- into a dated documentation folder with two markdown deliverables built from real screenshots of the actual prototype (or, when no live render is available, careful analysis of the source itself) -- user-flow.md (a happy-path-plus-edge-cases diagram and step-by-step narrative) and mockups.md (each main screen's real screenshot, or a clearly-flagged source-only writeup, next to its requirements). Use this whenever the user wants to document, formalize, or hand off a prototype's flow or screens -- phrases like "map out the user flow," "document the happy path and edge cases," "turn this prototype into mockups," "what are the requirements for this screen," or "I need something to hand to design/PM/eng" all point here, even if the user doesn't say "artifact," "screenshot," or name this skill directly, and even if all they've handed you is a local prototype file rather than a running app.
---

# Decompose a UI Prototype into Flow + Mockup Docs

This skill produces two markdown files from an existing prototype, saved together with the screenshots they reference: **user-flow.md** (diagram + narrative, happy path and edge cases) and **mockups.md** (each main screen's real screenshot next to its requirements). They serve different audiences — the flow doc is for anyone reasoning about behavior and branching, the mockups doc is for anyone building or reviewing the screens themselves — so keep them as two files rather than merging them, unless the user asks for one combined document.

The whole point of this skill is that the mockups show the *actual* prototype, not a redrawn approximation of it. Never hand-build an HTML/CSS rendition of a screen as a substitute for a screenshot — if you can't get a real screenshot of something, say so and leave a gap rather than fabricating a stand-in that looks plausible but isn't real.

## Step 1: Get the actual prototype in front of you

Don't work from a description of the prototype if the real thing is available — a flow or mockup built from what you assume a screen looks like will miss the actual states, copy, and edge cases that make the deliverable useful. There are four possible sources; figure out which one you actually have before doing anything else.

**A live web app.** Use the `chrome-devtools` browser tools to navigate it yourself: load the entry point, take a snapshot, then click through the primary path a real user would take. Deliberately go looking for the states that don't show up if you only click the happy path once — submit a form with invalid input, trigger an empty state (empty search results, empty cart), let something load, try to go back or cancel mid-flow, hit a boundary (e.g. a field's max length). These are exactly the edge cases the deliverable needs, and they're easy to miss if you only look at the "success" screen of each step. Since it's someone's running prototype, avoid anything destructive or hard to undo — don't submit real payments, delete real records, or send real emails/messages if the app is wired up to anything live; if you're unsure whether an action is safe to try, ask first. Before capturing anything, resize the page to one consistent viewport (e.g. 1280×800) so every screenshot in the deliverable shares the same frame — a mockups doc where every screen is a different size reads as sloppy even when the content is right.

**A Claude Design project link.** If the user gives you a `claude.ai/design/p/<id>` URL, or says the prototype lives in Claude Design, use the `claude-design` MCP tools instead of treating the link as an ordinary web page: `get_project` to confirm it, `list_files` to find the entry HTML file, and `render_preview` on that file to get a `serve_url`. Open that `serve_url` in `chrome-devtools` and drive it exactly like a live web app above — navigate, click through states, resize to a fixed viewport, screenshot. `serve_url` is a short-lived token-bearing link meant only for your own browser tooling; never put it in the deliverable, in chat, or in any file you write — the durable link you may reference back to the user is the project's `open_url`.

**A local prototype source file with no live render.** Sometimes what you're handed is a source export sitting on disk — e.g. a standalone HTML file — that references assets (a `support.js`, a `_ds/<design-system>` bundle, an API) that aren't actually present locally, so there's nothing to open in a browser and get a real render from. Don't silently guess whether a live version exists somewhere — ask the user whether they have a live URL or a Claude Design link they'd rather you use instead, so you get real screenshots. If they confirm there isn't one, work from the source alone: read the markup and logic carefully enough to reconstruct screens, states, and behavior — conditional-render blocks, state fields, disabled/validation logic, event handlers — with the same rigor Step 3 asks for, just derived from code instead of observed by clicking. Say explicitly in the output that these screens have no real screenshot (see Steps 5 and 6) rather than hand-building an HTML/CSS approximation to stand in for one — that would violate the whole point of this skill.

**Screenshots or images shared by the user.** Read each image and work from what's actually shown. If the sequence between screens isn't obvious from filenames or content — which screen leads to which — ask the user to confirm the order rather than guessing; a wrong sequence quietly corrupts both deliverables. If the images only show the happy path with no error/empty/loading states visible, say so explicitly in the output rather than inventing edge-case screens that were never shown to you (see Step 3). These are already real screenshots — Step 5 copies them into place rather than capturing new ones.

If it's unclear which of these you actually have, or whether a local file has a live counterpart worth using instead, ask before doing anything else — don't silently auto-detect or assume one.

## Step 2: Scope it before you build everything

If the prototype has more than a handful of screens or flows, don't silently try to document all of it — check with the user which flow(s) and which screens actually matter for this deliverable. Exhaustive coverage of a large app is a lot of wasted work if only one flow was actually in scope, and it's cheap to ask up front.

## Step 3: Identify the happy path and the real edge cases

For the flow you're documenting, work out:
- **The happy path** — the single sequence of steps a user takes when everything goes right.
- **The edge cases actually present in the prototype** — validation errors, empty states, loading/pending states, timeouts, permission/auth boundaries, cancel/back navigation, retries. Base these on what you actually observed in Step 1, not on generic "what edge cases does every app have" boilerplate.
- **Gaps** — places where the prototype doesn't define behavior (e.g., no visible error state for a failed submission). Don't invent one to fill the gap. Flag it as an open design question in the output instead — that's more useful to whoever receives this than a fabricated screen that doesn't reflect any real design decision.

## Step 4: Set up the output folder

Ask the user where this documentation should live, unless they've already told you (e.g. "save it under docs/prototypes") — don't guess a location silently, since a dated folder is the kind of thing that's annoying to find later if it lands somewhere unexpected.

Once you have a destination, create:

```
<destination>/YYYY-MM-DD-<short_feature_name>/
├── user-flow.md
├── mockups.md
└── screenshots/
```

- **Date** is today's date, `YYYY-MM-DD`.
- **short_feature_name** is a 2–4 word snake_case slug for what the prototype does (e.g. `accounts_management`, `checkout_flow`), drawn from the app's own name, title, or main heading — not from generic words like "app" or "prototype". If nothing in the prototype suggests an obvious name, ask rather than guess something that might not match how the user thinks about this feature.

## Step 5: Give every screen a stable ID and capture its screenshot(s)

Assign each main interface a short, stable ID (Screen 01, Screen 02, ...) before writing either file — both files reference the same IDs, so decide them once, up front, rather than inventing labels independently in each one.

For each screen, get a real screenshot into `screenshots/`, named `NN-slug.png` (e.g. `01-accounts.png`, `02-add-account.png`). If a screen has more than one meaningfully different state (default vs. validation error, empty vs. populated, disabled vs. enabled), capture each state as its own file with a state suffix — `03-transfer-money.png` and `03-transfer-money-error.png` — the same way Step 3 asked you to find those states in the first place. Don't screenshot every trivial variant (e.g. a button's hover color); capture states that change what the screen *communicates*.

- **Live web app:** use the `chrome-devtools` screenshot tool at the viewport size you fixed in Step 1, once per screen/state, right at the moment that state is showing (e.g. after submitting invalid input, before it's dismissed).
- **Claude Design project:** same as a live web app — capture through `chrome-devtools` against the `render_preview` `serve_url`, once per screen/state.
- **Shared images:** copy the user's original image files into `screenshots/`, renamed to the `NN-slug[-state].png` convention. Copy the original bytes — don't re-export, recompress, or redraw them.
- **Local prototype source with no live render:** don't create a screenshot file for these screens — there's nothing real to put there. Leave the slot empty and say so explicitly in mockups.md (Step 6) instead of substituting a hand-built stand-in.

## Step 6: Write mockups.md

One section per screen, in ID order:

```markdown
## Screen 0N: <Screen name>

*Used in: [Flow 0N](user-flow.md#flow-0n), step(s) ...*

![<Screen name>](screenshots/0N-slug.png)

**Functional requirements**
- ...

**States**
- ...
```

If Step 1 determined a screen has no real screenshot (source-only analysis), replace the `![...](screenshots/...)` line with a plain note instead — `*No screenshot available — analyzed from source only.*` — never point that line at a file that doesn't exist or a fabricated substitute.

Ground every requirement in what Step 1 actually showed. Tag anything you didn't directly observe as *(inferred)* — e.g. a loading state that must exist logically but never appeared during your walkthrough — so the reader can tell confirmed behavior from your best guess. For a source-only screen, ground requirements in what the code actually implements — state fields, conditional-render blocks, handler and validation logic — and still mark anything beyond that (e.g. behavior implied by a name but not actually implemented) as *(inferred)*. If a screen has multiple state screenshots, embed each with a one-line caption identifying which state it is, rather than only showing the default and describing the rest in prose.

Heading text becomes the link anchor other files use to reference this screen, following the standard slug rule: lowercase, spaces become hyphens, punctuation other than hyphens/underscores is dropped. `## Screen 01: Accounts` → `#screen-01-accounts`. Keep headings in exactly this `Screen 0N: Name` shape so the anchor is predictable when you write the links in Step 7.

## Step 7: Write user-flow.md

One flowchart per major flow (or one combined diagram if the flows share a hub screen and stay legible together), each as a fenced ` ```mermaid ` block, plus a numbered step-by-step narrative:

- **Diagram**: happy path as the main line, edge-case branches visibly distinguished with their own `classDef` (fill/stroke/text color all set, not just line style). Set an explicit `%%{init: {"theme": "base", "themeVariables": {...}}}%%` block defining `primaryTextColor`, `lineColor`, `textColor`, and `edgeLabelBackground` — markdown viewers (GitHub, editors, this CLI) render in both light and dark themes, and mermaid's default theme can land on illegible text/background pairings depending on which one is active. Pick your own colors for every text/background pair rather than trusting the default.
- **Narrative**: for each step, what the user sees, what action moves them to the next step, and — at each branch point — the condition that causes the branch and how the UI responds. Cover edge cases with the same level of detail as the happy path; hand-waving the error states defeats the purpose. Link every screen a step's prose names back to its `mockups.md` anchor, written as `[Accounts screen (Screen 01)](mockups.md#screen-01-accounts)` — include the screen number in the visible text itself, not just the link target, so a reader skimming the narrative knows which screen they'd land on without clicking through. This includes the shared starting screen: if every flow begins "from the Accounts screen...", link it every time it's named, the same as any dialog a step opens.
- **Open questions**: a short section at the end listing every gap Step 3 turned up, rather than burying them inline as if they were resolved behavior.

Because both files live in the same folder, links between them are plain relative paths (`mockups.md#screen-01-accounts`, `user-flow.md#flow-01-...`) — no publishing step or URL to wait on, so write the cross-links in whichever order is convenient and double check them once both files exist.

## Step 8: Hand off

Tell the user where the folder is, what each file covers, and point specifically at the open questions/gaps section so they know what's fully documented versus what still needs a design decision.
