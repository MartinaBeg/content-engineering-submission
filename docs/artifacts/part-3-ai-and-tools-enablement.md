# Part 3 — AI & Tools Enablement

**Zuora AI agents for quote-to-cash · Content engineering submission · Martina Beg · May 2026**

---

## The generative framework

A reusable system for producing on-brand Zuora copy at scale — one agent, two skills, and a shared voice-rules reference. The framework is composed of four real Markdown files in this repo (linked below), each loadable into Claude Code, Claude.ai, or any LLM platform as deployable agent code.

How a content request flows through it:

> *Request → Agent → `generate-zuora-copy` → `zuora-qa-check` → Approved output*

If QA fails, the agent revises via the generator with explicit avoidance instructions, then re-runs the check. After two consecutive failed attempts on the same variant, the agent escalates to a human reviewer.

---

## The agent

**File:** [`agent/content-production-agent.md`](./agent/content-production-agent.md)

The content production agent is the orchestrator. It receives a content request (e.g., *"produce three hero variants for the new landing page, audience CFO, pillar P1"*), parses it into structured inputs, invokes the generator skill, runs QA on each variant, and returns approved copy.

Guardrails baked into the agent's system prompt:
- It does not invent product capabilities. Requests outside the five pillars get clarified, not faked.
- It does not change the underlying messaging claim. Channel rendering can shape *how* a message is delivered; it cannot reinvent *what* the message is.
- It does not ship copy that fails QA.

---

## The skills

### `generate-zuora-copy`

**File:** [`agent/skills/generate-zuora-copy.md`](./agent/skills/generate-zuora-copy.md)

The generator. Takes a pillar (P1–P5 from Part 1), a channel (`web_hero` / `email_subject` / `social_linkedin` / etc.), an audience (`cfo` / `revenue_manager` / etc.), and a length, and produces channel-shaped copy. The actual prompt template lives inside the skill file — copy-paste-able into any LLM, with the pillar definitions and voice rules already inlined.

### `zuora-qa-check`

**File:** [`agent/skills/zuora-qa-check.md`](./agent/skills/zuora-qa-check.md)

The validator. Takes a piece of generated copy and the channel it's targeted at. Runs the 10-point checklist (next section). Returns per-check status (`pass` / `warn` / `fail`) and an overall verdict.

Both skills reference a shared voice file: [`agent/reference/voice-rules.md`](./agent/reference/voice-rules.md). One source of truth for banned phrases, signature phrases, and proof patterns — updating it propagates to both skills.

---

## The QA checklist

The `zuora-qa-check` skill runs these ten checks on every piece of generated copy:

1. **Banned phrases** — fails if any of: *revolutionize, next-gen, cutting-edge, game-changing, "AI-powered" wrapper, "easy to use" bare claim, "get started free," "try it now."*
2. **Exclamation marks** — fails if any appear outside customer quotes.
3. **Claim has proof** — warns if a quantitative claim has no number, named customer, or cert attached.
4. **Channel word count** — fails if outside the expected length for the channel (hero ≤ 12 words, email subject ≤ 50 chars, social 60–100 words).
5. **Audience addressing** — warns if "your business" appears instead of a specific persona.
6. **Finance jargon undefined** — warns if ASC 606, GAAP, or SSP are explained (assume CFO fluency).
7. **Signature phrase usage** — warns if no Zuora signature phrase appears where natural.
8. **CTA discipline** — fails if a CTA reads as "Get started," "Try now," "Sign up."
9. **Verbs-first in headlines** — warns if headlines stack adjectives instead of leading with a verb.
10. **Voice register match** — warns if the copy doesn't match the expected register for its channel (case study vs. press release vs. FAQ).

Behavior: one `fail` → revise and re-check. Two `warns` → return with flags. Three or more `warns` → return as fail. Two consecutive fails on the same variant → escalate to human.

---

## How this scales

Four ways the same framework produces content at scale, without adding people or process:

- **Variants for A/B testing.** Run the generator N times with the same inputs. The example below produces three hero variants in one invocation.
- **Channel adaptations.** Run the generator with different channel inputs against the same pillar. One claim becomes a web hero, an email subject, a LinkedIn post, and a sales one-liner — from one source.
- **Summaries.** Run with shorter length specs. A landing page section becomes a newsletter blurb, a social post, an internal Slack summary.
- **New surfaces.** Add a new channel value to the generator's allowed list (e.g., `podcast_intro`, `webinar_promo`). The agent and the QA skill don't change.

---

## Example: the framework in action

**Request:** *Generate three hero variants for the new Zuora AI agents landing page. Audience: CFO. Pillar: P1 (Finance-grade, by design).*

**Agent parses the request and invokes `generate-zuora-copy`:**

```yaml
pillar: P1
channel: web_hero
audience: cfo
count: 3
```

**Generator output:**

1. **Finance-grade AI agents for quote-to-cash.**
   *AI built to ISO/IEC 42001. Built for the audit. Built for finance.*
   Editorial note: leads with the certification, anchors on three "built for" beats.

2. **AI that closes the books without breaking the audit.**
   *Zuora AI brings finance-grade intelligence inside billing, collections, and revenue recognition.*
   Editorial note: leads with the operator pain (the close).

3. **The first AI revolutionizing finance workflows.**
   *AI-powered automation that makes audits a breeze.*
   Editorial note: leads with category claim.

**Agent invokes `zuora-qa-check` on each variant:**

- **Variant 1** — 10/10 pass.
- **Variant 2** — 9/10. Warn on check 7 (signature phrase usage). Could add *"audit-ready by default"* or *"before anything touches the ledger."*
- **Variant 3** — **Fail.** Check 1 (banned phrases): *"revolutionizing," "AI-powered."* Check 8 (CTA discipline): *"a breeze"* reads as a bare ease claim.

**Agent revises Variant 3 by re-invoking `generate-zuora-copy` with explicit instruction to avoid the flagged phrases. Re-runs QA:**

3. **Finance-grade AI for the people who close the books.**
   *Reviewable before posting. Audit-defensible after. Built into the workflows your team already runs.*
   Editorial note: persona-led, uses two signature phrases.

   QA: 10/10 pass.

**Final output to the team:** three approved hero variants ready for A/B testing on the landing page.

---

The four files in [`./agent/`](./agent/) are the real, inspectable artifacts. The narrative above explains them; the files themselves are the deployment.
