---
name: ritual
description: Use when unsure which ritual skill fits the current task — when the user says "/ritual" or "which ritual", asks "should I summon Fable for this", or substantial work is starting and no rite has been considered. Not for tasks whose routing is already obvious, and not a replacement for the rites themselves.
---

# The Ritual — front door to the rites

One question routes everything: **who is doing the work, and which direction is the talking?**

## The table

| Situation | Rite |
|---|---|
| The task deserves a stronger model than the one in the room | **summon-fable** — craft the one-shot first (Interview → Reconnaissance → Assembly) |
| The task is separable AND a cheaper capable tier exists below you | **convene-claudlings** — waves, canary, doer/fixer/judge. Convening is tier-relative: Fable convenes anyone, sonnet may convene haikus. Haiku has no one to convene — it channels and grinds. |
| Long-horizon API pipeline: mostly mechanical turns, judgment needed only at sparse forks | **consult-fable** — executor holds the wheel; Fable rides shotgun via the native advisor tool. (API pipelines only — the advisor tool is not available inside Claude Code sessions.) |
| Anyone, doing any nontrivial work — including high-VOLUME mechanical work (simple ≠ trivial; a 30-file batch still gets the closing ritual on a sample) | **channel-fable** — always in effect; it is the floor, not one of the options |
| Trivial task: minutes of work, a file or two, easily reversed | **No rite. Just do the work.** Ceremony that taxes small tasks is how big-task discipline dies. |

## Rules for the router

- **Answer from context before asking.** You know your own model tier. You can usually see whether the task is separable or entangled, big or small. Route silently when you can.
- **Ask at most ONE question, and it is always the same question**, because it is the only call that is genuinely the user's: *"Is this worth spending Fable on?"* Worth it: load-bearing code, wide-blast-radius design, work that must be right in one shot. Not worth it: routine implementation, mechanical batches, anything a claudling running channel-fable handles. Never invent a bespoke question — a router that improvises questions has become a grill.
- **Never escalate silently.** "This merits the expensive model" is spending the user's money; the budget question exists precisely so that decision is theirs. Routing to summon-fable without asking is only acceptable when the user already implied it ("get Fable on this").
- **Investigations channel first.** For debug/diagnose tasks, run channel-fable and gather the evidence cheaply before proposing a summoning; escalate when the investigation stalls (two failed theories — channel's own rule) or the fix turns out load-bearing. A summoning armed with evidence beats a summoning armed with symptoms.
- **Rites compose; they don't compete.** A big separable task in a small-model session = summon Fable, and the summoning prompt tells Fable to convene. Claudlings convened by anyone are told to channel. Route to the FIRST rite in the chain and name the whole chain.
- **If you ARE the expensive model, summoning is moot** — route to convene, or just work.

## Anti-rules

- Never run a grill here. If routing needs more than one question, the task is underspecified — that is a summon-fable Interview problem wearing a routing costume.
- Never route a trivial task to a rite "to be safe." The correct amount of ceremony for small work is zero.
- Never let this skill's existence delay obvious work. The front door is for people standing in the hallway, not people already in the right room.
