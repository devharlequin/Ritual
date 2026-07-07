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
