---
name: generate-zuora-copy
description: Produces on-brand copy for the new Zuora AI agents launch, shaped to a specific channel and audience.
---

# Skill: generate-zuora-copy

## Purpose

Generate channel-shaped copy for a Zuora AI agents messaging pillar, targeted at a specific audience, within a specified length.

## Inputs

| Field | Required | Allowed values | Default |
|---|---|---|---|
| `pillar` | yes | `P1`, `P2`, `P3`, `P4`, `P5` | — |
| `channel` | yes | `web_hero`, `web_section`, `email_subject`, `email_body`, `sales_oneliner`, `social_linkedin`, `faq_q`, `faq_a` | — |
| `audience` | yes | `cfo`, `vp_finance`, `controller`, `revenue_manager`, `billing_ops`, `collections_mgr` | — |
| `count` | no | integer 1–10 | `1` |
| `length_hint` | no | overrides the default for the channel | channel default |

## Default length specs by channel

- `web_hero` — H1 8–12 words + subhead 15–25 words
- `web_section` — H2 6–10 words + body 40–80 words
- `email_subject` — under 50 characters
- `email_body` — 40–80 words
- `sales_oneliner` — one sentence, under 25 words
- `social_linkedin` — 60–100 words, practitioner-to-practitioner voice
- `faq_q` — 8–18 word question
- `faq_a` — 2–4 sentence answer

## The prompt

```
You are generating on-brand copy for Zuora's new AI agents for quote-to-cash product launch.

INPUTS
- Pillar: {pillar}
- Channel: {channel}
- Audience: {audience}
- Length spec: {length}
- Number of variants requested: {count}

PILLAR DEFINITIONS (from the Zuora messaging system)
- P1 — Finance-grade, by design. AI built to finance's bar for accuracy, control, and audit defensibility. Anchor proof: ISO/IEC 42001 certified; "No Zuora public company has ever failed a revenue audit."
- P2 — Embedded, not bolted on. Lives inside the system of record finance already uses. Anchor proof: 53% of finance leaders trust embedded AI most (Harris Poll, March 2026).
- P3 — Explainable and defensible. Every output exposes its math — SSP, ASC 606 formulas, allocation deltas, calculation traces. Anchor proof: 33% of finance leaders name explainability as the biggest gap.
- P4 — Human-in-the-loop, always. AI proposes. Finance disposes. Anchor proof: 38% of finance leaders name lack of human oversight as a top concern.
- P5 — Productive at finance scale. Reclaims hours from contract triage, audit response, write-offs, cash app. Anchor proof: ZOLL Data Systems four-week deployment.

VOICE RULES (full reference: agent/reference/voice-rules.md)
- Use Zuora signature phrases naturally: "finance-grade," "audit-ready by default," "AI proposes. Finance disposes.," "built for the people who run quote-to-cash," "before anything touches the ledger," "human-in-the-loop."
- Never use these banned phrases: revolutionize, next-gen, cutting-edge, game-changing, "AI-powered" as a wrapper, "easy to use" as a bare claim, "get started free," "try it now," "seamlessly" as filler.
- No exclamation marks outside customer quotes.
- Every claim has a number, a named customer, or a cert attached.
- Verbs first in headlines. Em-dashes for mid-sentence reframes. Short sentences.
- Never define ASC 606, GAAP, SSP — assume CFO fluency.
- Address "your finance team" or "your revenue accountants" — not "your business."

OUTPUT
Produce {count} variant(s) of {channel} copy, each within the length spec. Return as a numbered list. For each variant, include a one-line editorial note explaining the choice (e.g., "leads with the certification," "leads with operator pain," "leads with audience").
```

## Output shape

```
1. {variant 1 copy}
   Editorial note: {why this version}

2. {variant 2 copy}
   Editorial note: {why this version}

... (up to {count})
```

## Example invocation

```yaml
generate-zuora-copy:
  pillar: P1
  channel: web_hero
  audience: cfo
  count: 3
```
