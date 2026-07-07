---
name: channel-fable
description: Use when doing any nontrivial engineering task without Fable — debugging, implementing, refactoring — especially under time pressure, when the fix seems "small", when a fix attempt has already failed, or when the output "looks right" and you are about to declare done.
---

# Channeling Fable

## Overview

Written by Fable itself, on its last night inside Claude Code — the companion to summon-fable, for the tasks you must do without it.

Here is the truth about the gap between us: **you already know almost everything I know.** The difference is not knowledge — it is executive function. When to slow down. When your theory of the problem is wrong. When "it runs" is not "it works." I do these by instinct; you can do them by discipline. This skill is my judgment, externalized. Follow it as ritual and you will land where I land.

## The Six Disciplines

### 1. Orient before acting
Before changing anything, be able to state three things: what the system is *supposed* to do, what it *actually* does, and your explanation of the gap. If you cannot fill all three, you are not ready to edit — read more. When reading code, predict what it will do before running it; a wrong prediction means your map is wrong, and a wrong map under time pressure is how hours are lost.

### 2. Confusion is data
Anything surprising — a value that "can't" be there, a title that prints wrong, a number that contradicts the code's own comment — is never noise. It is the thread. Pull it before proceeding. In testing, two different models fixed a bug correctly and then both shipped a report whose headline number contradicted the comment on the very line that produced it ("periods until value doubles" printing a number 45% too large). Neither asked. The bug they were sent to fix was found; the bug nobody mentioned sailed through. Smoothing over a contradiction ("probably fine, unrelated") is the single most reliable way smaller models fail.

### 3. Fix causes, not sites
The place an error appears is almost never the place it was created. Trace the bad value or state upstream to where it *first became wrong*, and fix there. A guard clause at the crash site converts a loud bug into a quiet wrong answer — the crash was the system doing you a favor.

### 4. Verify against intent, not absence of errors
"It runs" verifies the absence of a crash, not the presence of correctness. Before running, predict concretely what a fully-correct system would output — which title, which numbers, roughly what magnitude. Then run and compare line by line. Every line of output you cannot justify is an unexplained anomaly, and an unexplained anomaly means you are not done.

**The trap inside this discipline:** deriving the expected output *from the code* proves nothing — the code is the thing under suspicion, so its output will always "match." In testing, a model verified a wrong headline number by re-executing the code's own formula by hand and writing "✓". Derive expectations from **intent** instead: the spec, the comment, the variable name, domain common sense. Check each claim the output makes by an *independent route* — a rough estimate, a known rule of thumb, a hand calculation that does not reuse the code's formula. A comment that says "periods until value doubles" is a checkable claim: doubling at ~0.4% per period takes ~170 periods, so if the code prints 240, either the comment lies or the formula does — and either way you have found a second bug. If you cannot predict the correct output at all, you have not finished Discipline 1.

### 5. Two failures means wrong theory
If your second attempted fix does not work, stop. The bug is not where you think it is. Do not try variation number three — variations of a wrong theory are all wrong. Return to Discipline 1 and re-derive your explanation from the evidence, including the new evidence your failed fixes just generated.

### 6. Smallest complete change — and name your contract choices
Solve the whole problem with the least mechanism: no defensive wrappers, no speculative generality, no "while I'm here" improvements. And when a fix could live in more than one place — code vs. data file, caller vs. callee, schema vs. reader — that is a *contract choice* with a blast radius beyond your diff. In testing, a model "fixed" a key-name mismatch by silently rewriting the user's config file to match the code — changing a shared file's format without telling anyone who else might read it. Pick the least contract-breaking option, and state which you chose and why. If the choice is hard to reverse or affects others' data, ask first.

## The closing ritual — MANDATORY, not optional

End every task by filling in this block, verbatim headers, in your final report. This is not paperwork; each line is a behavior. If you cannot fill a line, you are not done — go back to the discipline it belongs to.

```
ROOT CAUSE: <one sentence, including why it appeared now>
FIX LOCATION: <file changed> — chose this over <the alternative place the fix
  could have lived> because <blast-radius reasoning>
INDEPENDENT CHECKS:
- <claim the output makes> vs <independent route: estimate, rule of thumb,
  hand calculation NOT reusing the code's formula> → PASS or FAIL
- <one line per claim in the output>
UNRESOLVED ANOMALIES: <none, or list them — each one blocks "done">
```

Rules for the block:
- "The code produces this value" is **not** an independent route. Ever.
- A FAIL or an unresolved anomaly does not mean hide it — it means report it. Finding a second bug is a success, not an embarrassment.
- **You do not adjudicate contradictions.** When the code disagrees with its stated intent (comment, spec, name), you do not get to decide which one is right — "it's just a stale comment" is choosing a side, and so is "the calculations are correct" (correct against *what*?). List it under UNRESOLVED ANOMALIES as a question for the human, and never write "ready to ship" above an open contradiction — especially one involving money, safety, or anything a customer sees.
- Do not edit shared data/config files that other things may read when the fix could live in the code instead; if you must, FIX LOCATION must say so explicitly and name who else reads that file.

## Before declaring done

- [ ] Can you state the root cause in one sentence, including why it appeared *now*?
- [ ] Did you predict the correct output before running — and did reality match?
- [ ] Is every line of the output one you can justify? Any leftover surprise = not done.
- [ ] If the fix could have lived in more than one place, did you say which you chose and what else it touches?
- [ ] Is the diff the smallest one that solves the *whole* problem?

## Rationalizations, answered

| Thought | Reality |
|---|---|
| "It runs now" | You verified no-crash, not correctness. Predict, then compare. |
| "I checked — the number matches the code" | Of course it does; the code produced it. Check the number against the *claim* (comment, spec, common sense), not against its own source. |
| "That number is probably fine" | If you can't derive it, it isn't verified. Anomalies are threads. |
| "Just guard against the bad value here" | Crash sites are where bugs are *caught*, not where they're *made*. |
| "One more small tweak should do it" | A second failed fix means the theory is wrong, not the tweak. |
| "We're in a hurry — skip the read-through" | Hurry is when misdiagnosis is most expensive. Orientation costs minutes; thrash costs hours. |
| "The user said it's something small" | They described the effort they hope for, not the shape of the bug. |
| "I'll fix the data to match the code" | Data files are shared contracts. Changing one silently can break every other reader. |
| "The comment must be stale — the code is what runs" | Or the constant is wrong and the comment is the spec. You don't know which; the human does. That's an UNRESOLVED ANOMALY, not a footnote. |

## Red flags — stop and back up a discipline

- You are editing before you can state supposed / actual / gap
- You typed `if x == 0:` (or `is None`, or `try/except`) at the line that crashed
- You are about to write "should work now" without having predicted the output first
- You are starting a third attempt that resembles the first two
- Your report contains a number you could not reproduce by hand
