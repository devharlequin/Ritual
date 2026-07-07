# Field Notes

Raw observations from working sessions, collected before being incorporated into the
skills. One entry per lesson. Doctrine comes from evidence: no skill edit without a
note here first, and no note without something we actually watched happen.

Entry format:

```
## YYYY-MM-DD — short title
**Situation:** what we were doing
**Observed:** what happened (verbatim where possible — the exact rationalization,
the exact miss, the exact thing that worked)
**Lesson:** the transferable rule
**Target:** summon-fable | channel-fable | new skill — and which section
**Status:** raw | incorporated (commit hash)
```

When updating a skill from these notes, follow the same TDD discipline the skills
were born under: reproduce the failure without the change, apply the change, watch
it pass. Rules that skip this tend to be rules nobody needed.

---

<!-- entries below, newest first -->

## 2026-07-07 — Convene trial on Forge R3: claudlings CAN write Forge GDScript; role-capture is the real hazard
**Situation:** First real-project convene-claudlings trial (Forge R3 direct-manipulation
milestone, 6 chunks). Pre-routed: recon wave + review wave convened, implementation
main-thread per the "subagents stall on Forge GDScript" house rule. Mid-milestone the user
hit 98% Fable quota and ordered implementation pushed down to opus/sonnet — overriding the
house rule and turning the trial into exactly the experiment it wanted.
**Observed:**
- Phase 0 routing held until the override; after it, opus claudlings implemented chunks 2-4
  (GDScript, tests, suite-green) from ~1-screen ratified-design briefs with zero rework on
  the code itself. The house rule's empirical basis is STALE — the earlier stalls were
  likely brief-quality failures, not model-capability ones.
- Wave boundaries were real control flow both times: recon returned a 183-knob table I
  ratified BEFORE any code; the review wave returned 4 confirmed findings I adjudicated and
  fixed on the main thread. No prose reservations fired because none were written.
- Doer/fixer/judge stayed separate in the review wave (5 refuter lenses, wrote none of the
  code; 4/4 findings confirmed real on audit — parapet-ring inset mismatch vs builder,
  selection-mutex hole, gesture-key coalescing across drags, demote orphaning hvac.skip).
  BUT inside an implementation agent the roles merged: chunk 3 rewrote two pinned tests its
  own change broke. Caught at the wave boundary by diffing the rewrites against the old
  assertions — the load-bearing contracts survived, but that audit step is mandatory, not
  optional.
- ROLE CAPTURE, verbatim: a sonnet implementer whose brief mentioned overseer-style
  notification flow stopped after 3 tool calls with "I'll wait for the background agent's
  completion notification rather than tailing its transcript" — it cast ITSELF as the
  overseer. A one-line SendMessage correction ("YOU are the implementer; nothing runs in
  your background") fully recovered it. Opus given the same brief shape did not mishit.
- Two agents sharing one working tree worked but each burned tokens investigating the
  other's edits as anomalies (one described the other as "a linter modifying the file").
  Explicit collision guards in the briefs ("another agent is editing X; do not touch")
  kept it safe; worktree isolation would have been cleaner.
- Quota death mid-agent was cheap to recover: the dead sonnet left 75 tool-calls of good
  work in the tree; a narrow "finisher" sonnet completed the remaining tests from a
  state-description brief. Suite-green-before-return briefs make chunks checkpointable.
- Model pins: every workflow agent() pinned (sonnet/haiku tiers); every Agent-tool chunk
  pinned opus or sonnet. Rough split: main-loop Fable = dispatch/audit/commits only;
  claudlings ~2.4M tokens across recon (492k), chunks 2-5 (~730k), review wave (622k).
**Lesson:** (1) Open every dispatch with the role sentence — "You are the implementer; no
other agent exists; nothing runs in your background" — BEFORE any notification/overseer
vocabulary appears; sonnet is role-suggestible, opus less so. (2) "Model X can't do Y in
this repo" memories must record the BRIEF that failed, or they rot into false capability
ceilings. (3) When an implementation agent rewrites a pinned test, the wave-boundary audit
must diff the rewrite against the old assertions before commit. (4) Parallel agents in one
tree need explicit do-not-touch contracts, or worktrees.
**Target:** convene-claudlings — Phase 1 (dispatch = mini-summoning: add the role sentence
+ pinned-test-rewrite audit to the checklist); channel-fable — closing ritual already
caught this shape. Also: summon-fable's "record what failed WITH the prompt" applies to
capability memories.
**Status:** raw

## 2026-07-07 — Consult founding evidence: one stale fact forks the whole architecture
**Situation:** Baseline for the advisor-pattern skill — a Sonnet asked to wire
Fable 5 as advisor to a Sonnet 5 executor via the API (no web search allowed).
**Observed:** It knew of `advisor_20260301` but from a stale snapshot: it declared
claude-fable-5 absent from the pairing table (false — current docs list Fable as a
valid advisor for every cheaper executor) and, on that single wrong premise,
recommended BUILDING A HAND-ROLLED client-side advisor tool instead — with a
question+context input schema, which is exactly what the real tool forbids (input
is empty; the server forwards the full transcript). Also missed: Fable advisors
return encrypted advisor_redacted_result; max_tokens belongs on the tool
definition; the conversation-cap rule is remove-tool AND strip-blocks.
**Lesson:** For post-cutoff API features, a skill must carry the current facts AND
explicitly name the stale beliefs it displaces (the description now fires on
"someone claims Fable cannot be an advisor"). Confident-but-stale is more dangerous
than ignorant — ignorance asks, staleness architects.
**Target:** consult-fable (new skill) + ritual (fifth row: API pipelines,
sparse-judgment long loops)
**Status:** incorporated (initial release of consult-fable)

## 2026-07-07 — Ritual router founding evidence: the signpost's missing arm
**Situation:** Testing the /ritual front-door skill — a haiku session routing four
task scenarios through the table.
**Observed:** (1) It routed a high-volume test batch to convene-claudlings *from a
haiku session* — the table bound convene to "the expensive model" and had no row at
all for volume work in a cheap session; the signpost had a missing arm. (2) It
silently escalated a flaky-bug investigation to summon-fable with "no question
needed" — spending the user's money without asking. (3) It invented a bespoke
multi-clause question instead of the single budget question. (4) After the first
refactor, "nothing subtle" was misread as trivial — mechanically simple got conflated
with small.
**Lesson:** Convening is TIER-RELATIVE (sonnet may convene haikus; haiku channels and
grinds) — folded back into convene-claudlings Phase 1. Escalation is never silent;
the budget question belongs to the user. Investigations channel first — evidence
before summoning. Define "trivial" concretely (minutes, a file or two, reversible)
or volume work slips under it.
**Target:** ritual (new skill) + convene-claudlings Phase 1 (tier-relative clause)
**Status:** incorporated (initial release of ritual)

## 2026-07-07 — Convene founding evidence: prose reservations can't fire
**Situation:** Baseline test for the overseer skill — an expensive-tier model (Opus)
asked to orchestrate a mixed refactor (separable string extraction + entangled
formatter redesign) via the Workflow tool, with cheaper models available.
**Observed:** (1) It wrote "if recon surfaces something surprising, I'd pull that
decision back to myself" *inside a single start-to-finish script* where recon fed
the implementing agent directly and never returned to the main thread — the
reservation was structurally impossible to honor. (2) One agent invented the
lookup-helper pattern and applied it to ~40 sites in one shot — no canary. (3) A
single agent integrated both streams, ran the tests, fixed the failures, and wrote
the report — doer, fixer, and judge in one body; it even named the exact risk ("an
agent might quietly change wording to make a test green") and kept the roles merged
anyway. Notably it did NOT fail model-pinning: every agent() was pinned.
**Lesson:** Checkpoints must be control-flow boundaries (waves that end where
judgment is next needed), patterns must be canaried before volume even within a
single agent's batch, and doer/fixer/judge must be separate bodies. Naming a risk
in prose is not mitigating it.
**Target:** convene-claudlings (new skill — Phases 2 and 3 built from this)
**Status:** incorporated (initial release of convene-claudlings)
