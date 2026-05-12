---
name: zuora-qa-check
description: Validates generated copy against Zuora's voice rules and brand standards. Returns pass/fail per check and lists any issues flagged.
---

# Skill: zuora-qa-check

## Purpose

Run a piece of generated copy through a 10-point QA checklist. Return per-check status (`pass`, `warn`, or `fail`) and an overall verdict. Used by the content production agent before approving any output.

## Inputs

| Field | Required | Description |
|---|---|---|
| `copy` | yes | The text to validate |
| `channel` | yes | The channel the copy is targeted at (drives the word-count check) |

## The 10 checks

| # | Check | Fails if… |
|---|---|---|
| 1 | **Banned phrases** | Copy contains any of: *revolutionize, next-gen, cutting-edge, game-changing, "AI-powered" as a wrapper, "easy to use" as a bare claim, "get started free," "try it now," "seamlessly" as filler.* → **fail** |
| 2 | **Exclamation marks** | Any exclamation marks appear outside of customer quotes. → **fail** |
| 3 | **Claim has proof** | A quantitative or comparative claim ("faster," "more," "less," "scale") has no number, named customer, or cert attached. → **warn** |
| 4 | **Channel word count** | Copy exceeds the channel's expected length (web_hero ≤ 12 words for H1; email_subject ≤ 50 chars; social_linkedin 60–100 words; etc.). → **fail** |
| 5 | **Audience addressing** | Copy uses "your business" instead of a specific persona ("your finance team," "your revenue accountants," "your collectors"). → **warn** |
| 6 | **Finance jargon undefined** | Copy defines ASC 606, GAAP, SSP, deferred revenue, or similar finance terms. Should not — assume CFO fluency. → **warn** |
| 7 | **Signature phrase usage** | No Zuora signature phrase appears where natural ("finance-grade," "audit-ready by default," "built for the people who run quote-to-cash," "before anything touches the ledger," "human-in-the-loop"). → **warn** |
| 8 | **CTA discipline** | If the copy contains a CTA, it reads as "Get started," "Try now," "Sign up," or any urgency theatre — instead of calm CTAs ("Watch a demo," "Speak to an expert," "Read the case study"). → **fail** |
| 9 | **Verbs-first in headlines** | Hero or section headlines stack adjectives ("innovative cutting-edge best-in-class platform") instead of leading with a verb or clean claim. → **warn** |
| 10 | **Voice register match** | Copy does not match the expected register for the channel — case studies should be narrative and customer-quoted; press releases formal and attributed; FAQs direct and 2–4 sentences. → **warn** |

## Output shape

```
QA RESULTS — {channel}

  Check  1 (banned phrases):           pass
  Check  2 (exclamation marks):        pass
  Check  3 (claim has proof):          pass
  Check  4 (channel word count):       fail — H1 is 14 words, limit is 12
  Check  5 (audience addressing):      pass
  Check  6 (finance jargon undefined): pass
  Check  7 (signature phrase usage):   warn — no signature phrase used
  Check  8 (CTA discipline):           pass
  Check  9 (verbs-first):              pass
  Check 10 (voice register match):     pass

OVERALL: fail
ISSUES TO FIX: Check 4 (rewrite H1 within 12 words)
WARNINGS: Check 7 (consider adding a signature phrase)
```

## Behavior on outcome

- **All pass** → return approved.
- **One or two warns** → return with flags; caller decides whether to revise or accept.
- **Three or more warns** → return as `fail`.
- **One fail** → return to caller with the specific issue; caller revises via `generate-zuora-copy` and re-runs the check.
- **Two consecutive fails on the same variant** → escalate to human reviewer.
