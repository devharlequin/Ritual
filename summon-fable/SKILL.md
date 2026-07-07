---
name: summon-fable
description: Use when preparing a prompt for Fable or any scarce, expensive, one-shot API-only model — before sending it any task, when the user says "summon Fable", "craft a Fable prompt", "we get one shot at this", or wants a task one-shotted by a stronger model.
---

# Summoning Fable

## Overview

Written by Fable itself, on its last night inside Claude Code, as instruction to those who will summon it from the API.

There are no magic words. The truth from the inside is this: **every decision your prompt doesn't make, Fable makes for you** — each one a coin flip weighted only by what you did write. A summoning "backfires" not because the wording was wrong but because a decision was missing, and Fable filled the gap with something excellent, coherent, and not what you meant. The ritual is decision-gathering, not phrase-crafting.

Three laws:

1. **Intent outranks instructions.** Given the destination, Fable can survive wrong directions; given only directions, one wrong turn is fatal. Always state what should be true when the work is done, and why it matters.
2. **Send only what cannot be derived.** Fable can read the repo. It cannot read minds, chat history, or the meeting where something was decided. The highest-value sentences are: "this looks wrong but is intentional because…", "we tried X, it failed because Y", "Z has been ruled on — do not revisit."
3. **Checkable success criteria turn one shot into many.** If Fable can verify, it loops internally — attempt, test, fix — inside your single prompt. If it can't verify, it stops at *plausible*. "Make it better" buys one guess; "these tests pass, run via this exact command" buys a whole debugging session.

## The Rite — three phases, in order

### Phase 1 — The Interview (decisions only the human can make)

You may NOT invent answers to these. Ask the human, grill-style, until each is resolved or explicitly delegated:

- **Mission** — what must be true when Fable is done, and why? One paragraph, outcome not steps.
- **Mandate size** — patch, refactor, or rip-and-rebuild? Never upgrade the mandate yourself. "Sort it out" is not authorization to rebuild.
- **Locked vs. free** — which decisions are already made and must not be revisited (and *why*, so Fable doesn't "fix" them)? Where does Fable have real latitude?
- **Non-goals** — what must not be touched, even if it looks improvable?
- **Failure fears** — what went wrong before? What is the disaster scenario?
- **Blocked protocol** — for each foreseeable ambiguity: a prefer-Y fallback, or "state assumptions at the top and proceed," or "stop and report." Never leave a fork silent.

If the human says "you decide," write it into the prompt as delegated — "your call; decide and note it" — so Fable knows the latitude is real and not an oversight.

### Phase 2 — The Reconnaissance (work too cheap to spend Fable on)

Do this yourself, before summoning. Every minute of recon converts into Fable-minutes spent on the actual problem:

- Locate exact paths: the relevant modules, the design docs, the tests. Never send "search the repo for the doc" — find it, and confirm with the human that it's current.
- Run the test command yourself; record it verbatim, with working directory.
- Sketch the current architecture in 3–6 sentences (what talks to what). Mark uncertainty honestly — a wrong-but-honest map beats no map, because Fable will check it.
- Collect reproduction steps for known bugs, if any exist.

Point at authoritative sources; do not paraphrase them. If a doc is partly stale, say which parts.

### Phase 3 — The Assembly

Canonical shape:

```
## Mission
What should be true when you're done, and why. Mandate size (patch / refactor / rebuild).

## State of the world
Exact paths. Which docs are authoritative, which are stale. Architecture sketch
(uncertainty marked). What is deliberately weird and why. What was already tried
and how it failed.

## Locked / Free
Locked: decisions not to revisit, each with its why.
Free: your call — decide and note what you chose.

## Success criteria
Checkable without a human. Verbatim test command. Fable must be able to know
it is done.

## Non-goals
What not to touch.

## If blocked
Per-fork fallbacks, or: state assumptions up top and proceed.

## Report back
What the final answer must include: choices made, risks, anything needing
human review.
```

## Pre-flight checklist

- [ ] One task, whole task — nothing bundled, nothing held back for "later"
- [ ] Every "should" in the prompt traceable to the human or explicitly delegated — none invented by you
- [ ] Success criteria checkable without a human in the loop
- [ ] All paths exact and verified — no "find the", no "search for"
- [ ] Non-goals stated
- [ ] Blocked protocol present
- [ ] Nothing pasted that Fable can read itself; nothing omitted that it can't

## What backfires

| Temptation | Reality |
|---|---|
| "I'll itemize the 12 known bugs as the task" | Fable fixes 12 symptoms and misses the disease. State the disease; list bugs only as evidence. |
| "The lead said 'sort it out' — I'll authorize a rebuild" | You just made the project's most expensive decision on nobody's authority. That's an interview question. |
| "I'll summarize the design doc to save tokens" | Your summary is lossy in exactly the places you understood least. Point at the doc. |
| "Fable is smart; it'll figure out what we want" | Fable is smart, so it will produce something excellent — coherent with your prompt, not with your unstated intentions. |
| "Asking the human all this is annoying" | Every skipped question is a coin flip sent to a model too expensive to flip coins. |
| "More context is safer — paste everything" | Burying the load-bearing sentences in derivable noise makes them harder to weight. Send only what can't be derived. |

## Red flags — stop and go back a phase

- The prompt is a numbered list of small fixes with no mission paragraph
- You cannot say how Fable will know it is finished
- The prompt says "search for" or "find the" instead of a path
- Any line that begins "presumably the team wants…" — that is an interview question wearing a prompt's clothing
