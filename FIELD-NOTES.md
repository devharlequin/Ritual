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
