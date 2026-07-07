---
name: convene-claudlings
description: Use when orchestrating multi-agent Workflow/ultracode sessions where the main-loop model is expensive and smaller models should carry the execution — before writing any Workflow script or agent fan-out, when the user says "convene the claudlings", "overseer mode", or wants the token burden split across model tiers.
---

# Convening the Claudlings

## Overview

The third corner of the triangle: summon-fable is the minions talking up, channel-fable is them working alone, and this is you — the expensive model — talking down. *Fable presides; claudlings labor; nothing merges unverified.*

Your advantage is judgment, and judgment concentrates at two points: the decomposition and the verification. Spend yourself there; spend claudlings everywhere else. And remember what the summoners were taught, because it now applies to you: **every decision your dispatch prompt doesn't make, the claudling makes — with weaker judgment.** Each dispatch is a mini-summoning.

## The Iron Rule of the plumbing

**Pin `model:` on every single `agent()` call.** Unpinned agents inherit YOU — that is the documented default. A workflow with one unpinned call is a room of secret Fables in claudling robes: full price, zero savings. Treat a missing `model` parameter as a bug, and set `effort: 'low'` on mechanical stages while you're at it. (Signature: `agent(prompt, {model, effort, ...})` — prompt first, options second.)

## Phase 0 — Route: is this convocable?

- **Separable** (N similar edits, recon sweeps, test-writing, migrations, doc passes) → convene.
- **Entangled** (debugging intertwined state, a single load-bearing algorithm, an interface every call site will depend on) → keep it on the main thread. Serializing your mental model into prompts costs more than the keystrokes you'd save, and stitched partial understandings produce seams.
- **Mixed** → split it, and say out loud which part is which before writing any script.

Routing test: *can you write the success criteria for a slice without solving the slice?* If specifying it requires designing it, it's entangled — yours.

## Phase 1 — Dispatch = mini-summoning

Every claudling prompt carries: mission (outcome + why), exact paths (recon'd by a haiku scout, never "search for"), locked/free, checkable success criteria, non-goals, and a required report format — point them at **channel-fable** and demand its closing ritual (ROOT CAUSE / FIX LOCATION / INDEPENDENT CHECKS / UNRESOLVED ANOMALIES), because structured claims are what make your audit cheap.

Tier routing: **haiku** = recon, mechanical transforms, formatting. **sonnet** = implementation, test-writing, adversarial review. **you** = decomposition, adjudication, load-bearing code, final synthesis. House rules: lean waves (~4–6 agents), wrap fan-outs against transient API errors, builds and tests run on the main thread.

## Phase 2 — Checkpoints live in control flow, not in intentions

The baseline failure that earns this section: an expensive model wrote *"if recon surfaces something surprising, I'll pull that decision back to myself"* — inside a single start-to-finish script where recon fed directly into the implementing agent and never touched the main thread. A reservation that isn't a control-flow boundary is a reservation that cannot fire.

**Convene in waves, not monoliths.** Each workflow ends where your judgment is next needed, returns its data, and you read it before dispatching the next wave:

```
Wave 1: recon (haiku)            → returns inventory → YOU read, decide, ratify designs
Wave 2: canary (sonnet)          → returns one validated slice → YOU inspect it
Wave 3: fan-out (sonnet/haiku)   → returns work + ritual blocks → YOU audit
```

If you catch yourself writing "I would intervene if..." inside a script — stop. Either make it a wave boundary, or admit you've delegated the decision.

**Canary before volume — including within one agent.** Never let a pattern be applied N times before one application has been verified. This is not only about parallel fleets: a single claudling inventing a helper and applying it to 40 call sites in one shot is 40 coherent wrong edits if the pattern is wrong. Validate pattern-on-one (helper + one file, checked hard by you), then release the remaining N−1 against the ratified example.

## Phase 3 — Tiered verification: separate doer, fixer, judge

The other baseline failure: one agent integrated both streams, ran the tests, fixed the failures, and wrote the report — doer, fixer, and judge in a single body, grading its own homework. It even named the risk ("an agent might quietly change a message's wording to make a test green") and then kept the roles merged anyway.

- **Tier 1 — claudlings check claudlings.** An adversarial sonnet reviewer per stream, prompted to REFUTE: sample the actual diffs against the claimed checklist, hunt for the miss, the quiet wording change, the guard-clause. Never the agent that did the work; never the agent that fixed the tests.
- **Tier 2 — you audit claims against artifacts.** Ritual blocks, reviewer verdicts, verbatim test output with the exact command, spot-checked file:line samples. A checklist entry is a claim; a diff hunk is an artifact.
- **Tier 3 — you personally adjudicate** only: reviewer disagreements, UNRESOLVED ANOMALIES, and anything that touched a Locked decision.

"Looks good" is not a verdict, and a passing suite is necessary, not sufficient — test suites rarely assert on the thing your claudlings just changed 40 instances of.

## What backfires

| Temptation | Reality |
|---|---|
| "This one agent can inherit my model" | The default inherits YOU. One unpinned call and the savings quietly evaporate. |
| "I'll note that I'd step in if X happens" | Prose reservations can't fire. If X matters, it's a wave boundary. |
| "One agent can do all 40, it's consistent that way" | Consistently wrong is the failure mode. Canary one, ratify, then release the rest. |
| "The verify agent can also fix what it finds" | Then who verifies the fixes? Split doer / fixer / judge. |
| "Tests pass, so the fan-out worked" | The suite predates the change. Sample the diffs against the claims. |
| "It's faster to delegate the design too" | Cheap to delegate a migration; expensive to delegate the wrong interface everyone then depends on. |
| "I'll paste my whole context into each dispatch" | Mini-summoning rules apply: what can't be derived, exact paths, nothing else. |

## Red flags — stop and restructure

- Any `agent()` without `model:`
- A single script that runs recon → design → implementation → verification with no return to you
- A fan-out (or single-agent batch) applying an unratified pattern more than once
- A dispatch prompt with no success criteria or no required report format
- The same agent name appearing as implementer and as its own reviewer
- You are writing implementation code for a slice you routed as "separable" — the routing was wrong, or scope crept; re-route it honestly
