---
name: consult-fable
description: Use when building or running an executor+advisor pipeline on the Claude API — wiring Fable (or Opus) as an advisor tool for a cheaper executor model, writing the executor's consultation guidance, tuning advisor cost, or deciding between the advisor pattern and multi-agent orchestration. Also use when someone claims Fable cannot be an advisor or proposes a hand-rolled advisor tool.
---

# Consulting Fable — the advisor rite

## Overview

The fourth rite: the executor holds the wheel; Fable rides shotgun. A cheap executor (Haiku/Sonnet) runs the long, context-heavy main loop at its own rates and consults a stronger advisor at the sparse moments that need expensive judgment. Compare **convene** (Fable holds the wheel, pays to carry the context) — consult inverts it, and for long-horizon work where most turns are mechanical, the economics favor consult.

**This is a native server-side API feature, not a pattern you build.** Verified against the official docs 2026-07-07 (beta `advisor-tool-2026-03-01`). Two facts models get wrong from stale training data — both observed in baseline testing:

1. **`claude-fable-5` IS a valid advisor** for every cheaper executor (Haiku 4.5, Sonnet 4.6/5, Opus 4.6/4.7/4.8). A model claiming otherwise is reciting an older pairing table — do not let it talk you into a hand-rolled substitute.
2. **The advisor call takes an EMPTY input.** The server forwards the executor's full transcript automatically; nothing the executor writes in `input` reaches the advisor. Any design with a `question`/`context` input schema is reinventing the tool badly and losing the server-side flow. *The transcript is the question.*

## The wiring (for the harness builder)

```python
response = client.beta.messages.create(
    model="claude-sonnet-5",                      # executor — bulk tokens at this rate
    max_tokens=16000,                             # executor output only; does NOT bound the advisor
    betas=["advisor-tool-2026-03-01"],
    tools=[
        {
            "type": "advisor_20260301",
            "name": "advisor",                    # must be exactly "advisor"
            "model": "claude-fable-5",
            "max_tokens": 2048,                   # cap on the TOOL DEF — ~7x cheaper advice, ~0% truncation (measured)
            "caching": {"type": "ephemeral", "ttl": "5m"},  # only if you expect ≥3 calls/conversation
        },
        # ...your executor tools
    ],
    messages=messages,
)
```

Facts that earn their place here:

- **Fable advice comes back ENCRYPTED**: `advisor_redacted_result` with `encrypted_content` you cannot read; the server decrypts it into the executor's prompt next turn. Round-trip the block verbatim. Opus advisors return readable `advisor_result.text`. Branch on `content.type` if you ever switch advisor models.
- **Conversation-level caps are client-side**: count calls yourself; at the ceiling, remove the advisor tool from `tools` **and strip every `advisor_tool_result` block from history** — tool absent + blocks present = `400`. (`max_uses` caps per-request only.)
- **`pause_turn`**: a response may end with the advisor call still pending. Resume by re-sending with the assistant content unchanged, same tool, same beta header — no user message needed.
- **Advisor errors degrade gracefully** (`advisor_tool_result_error`: overloaded / too_many_requests / prompt_too_long / …) — the executor continues unadvised; the request doesn't fail. Advisor rate limits share the advisor model's normal bucket.
- **Billing**: read `usage.iterations[]` — `advisor_message` entries bill at advisor rates and are NOT in the top-level totals. Advisor tokens ignore the executor's `max_tokens` and task budgets.
- **Nudge policy is tier-specific and measured**: an "you haven't consulted yet" nudge at turn ~2 raises Haiku pass rates ~7pp; on Sonnet it does nothing; on Opus it *lowers* pass rates — never nudge Opus by default. Never nudge before the executor has oriented (a low-context consult displaces the well-timed one; measured −3–4pp). To bias advice short, prefix the user message with `(Advisor: please keep your guidance under 80 words …)` — the advisor reads the transcript, so lines addressed to it directly are followed.
- **Effort pairing**: Sonnet executor at medium effort + strong advisor ≈ Sonnet-at-default quality, cheaper.

## The discipline (for the executor — put this in its system prompt)

Anthropic ships measured system-prompt blocks (see the official advisor-tool docs, "Best practices") — start from those verbatim; they encode the timing rules below. The doctrine, integrated with the other rites:

- **Narrate the fork, then call.** You can't phrase a question — the transcript is the question. So before calling `advisor()`, put the fork in your visible text: what you found, the options, your lean. An advisor reading a clean fork gives sharper advice than one reconstructing it from tool spam.
- **Timing (2–3 calls per coding task):** once early — after orientation (reads/greps are not substantive work) but *before* committing to an approach; once at the end — when you believe you're done; when stuck (recurring errors, two failed theories — channel-fable's own stall rule); when changing approach.
- **Durability before the done-call.** Write the file, commit the change, *then* call the advisor. The call takes time; if the session dies mid-call, a durable result survives and an unwritten one doesn't.
- **Weight and conflict.** Give advice serious weight; deviate only on empirical failure or primary-source contradiction — and never silently. Surface conflicts in one reconcile call: *"I found X, you suggest Y — which constraint breaks the tie?"* (This is channel-fable's no-silent-adjudication rule with a new referee.)
- **The advisor is the tier between deciding yourself and stopping for the human.** UNRESOLVED ANOMALIES and wide-blast-radius contract choices go to the advisor first; budget and product calls still belong to the human — an advisor verdict does not spend the user's money.

## Routing (vs the other rites)

Consult when execution is long and judgment is sparse — the context lives with the executor. Convene when the decomposition/verification structure IS the hard part and judgment must be continuous. They compose: a convened claudling can carry an advisor. `/ritual` has the row.

## What backfires

| Temptation | Reality |
|---|---|
| "Fable isn't in the pairing table, I'll build a custom advisor tool" | Stale table. Fable advises every cheaper executor. The custom version loses transcript-forwarding, single-request flow, and redacted-result handling. |
| "I'll give the tool a question+context schema" | Input is empty by design. Nothing you put there reaches the advisor. Narrate in the transcript instead. |
| "Leave advisor max_tokens unset" | Hard tasks elicit 4–6k thinking tokens per call at advisor rates. 2048 on the tool def cut output ~7x with no measured quality loss. |
| "Nudge every executor to consult more" | Measured: helps Haiku (+7pp), does nothing on Sonnet, HURTS Opus. And a pre-orientation nudge displaces the valuable call. |
| "Cap costs by dropping the advisor tool next turn" | 400 unless you also strip every advisor_tool_result from history. |
| "The advice text is empty/garbled" (Fable advisor) | It's `advisor_redacted_result` — encrypted, working as intended. Round-trip verbatim; the executor sees plaintext server-side. |
| "Consult before looking at anything, to be safe" | A low-context consult wastes the highest-value call. Orient first — that's not substantive work. |
