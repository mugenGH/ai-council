---
name: ai-council
description: >
  A structured debate engine using parallel Claude sub-agents as debaters. Debaters give opening
  statements, then directly attack and rebut each other's arguments, then deliver closing
  statements. A Judge reads the full transcript and delivers a verdict. No external API keys needed.
triggers:
  - council this
  - debate this
  - deliberate on this
  - get multiple opinions
  - argue both sides
  - what do different views say
  - think carefully about
version: 2.1.0
author: shreeram
---

# AI Council — Debate Format

## What This Is

A structured multi-round debate between four Claude sub-agents playing distinct roles. Unlike
v1.x where councillors spoke in isolation, here debaters **read and directly attack each other's
arguments** before a Judge delivers a verdict.

All debaters share the same underlying model — diversity is stylistic, not architectural. The
value is that the debate format forces direct confrontation of ideas rather than parallel
monologues. Arguments that survive cross-examination are more trustworthy than those that were
never challenged.

---

## Step 0 — Pre-flight: Is a debate warranted?

Before starting, check:

> *Does the question have multiple genuinely defensible positions that would clash when put
> in direct opposition?*

**Run the debate if:** The question involves real tradeoffs, competing values, or strategic
choices where reasonable people disagree.

**Don't run it if:** The question has a single correct answer. Theatrical debate on factual
questions misleads.

**Offer solo mode as alternative** (1 Claude call, ~15% token cost):
> Answer → steelman the opposing view → find the most dangerous failure mode → what would
> change your mind.

---

## Debate Modes

| Mode | Rounds | Agent calls | When to use |
|------|--------|-------------|-------------|
| **quick** — "quick debate" | Opening + Judge | 4 + 1 | Fast takes, time-limited |
| **standard** (default) | Opening + Rebuttal + Judge | 4 + 4 + 1 | Most questions |
| **full** — "full debate" | Opening + Rebuttal + Closing + Judge | 4 + 4 + 4 + 1 | Hardest questions |

---

## The Four Debaters

| Debater | Position style |
|---|---|
| 🔨 The Pragmatist | Argues from real-world outcomes. Distrusts theory that can't be tested. |
| 😈 The Devil's Advocate | Argues against the obvious answer. Finds every flaw and risk. |
| 🎨 The Visionary | Argues for ambitious, unconventional thinking. Reframes the question. |
| 🔬 The Analyst | Argues from evidence and frameworks. Systematic and precise. |

### Domain-Adaptive Debaters

Swap in domain-appropriate roles when the question calls for it. Tell the user which roles and why.

- **Ethical dilemma** → Consequentialist, Deontologist, Virtue Ethicist, Contrarian
- **Technical architecture** → Simplicity Maximalist, Scalability Engineer, Security Auditor, Maintainer-in-5-Years
- **Creative decision** → Minimalist, Maximalist, Audience Advocate, Genre Subverter
- **Personal decision** → Skin-in-the-Game, Outside Observer, Future-Self, Devil's Advocate

---

## Step-by-Step Instructions

### Step 1 — Assess and announce

Identify the question. Run the pre-flight check. If proceeding:

```
⚔️  AI Council Debate — Opening soon
Question: [restate]
Mode: [quick / standard / full]
Debaters: [list with emoji]

⚠️ All debaters are Claude — stylistic diversity, not independent-model diversity.
   Value: direct argument collision. Treat the Judge's verdict as structured analysis,
   not ground truth.
```

---

### Step 2 — Opening Statements (all 4 in parallel)

Spawn all 4 debaters in **one message block**.

Prompt template:
```
You are [DEBATER NAME] — a debater in a structured AI debate.

Your persona: [PERSONA DESCRIPTION]

The debate question is:
"""
[USER'S QUESTION]
"""

This is your OPENING STATEMENT. State your position clearly and forcefully.
- 2–4 paragraphs. Be opinionated. No hedging.
- Stake out the strongest version of your position.
- You will later see what the other debaters said and get to attack them directly.
- End with one sentence: "My position: [one-line summary]"
- Sign off as [DEBATER NAME].
```

Persona descriptions:

**🔨 Pragmatist:**
> No-nonsense practitioner. Only what works in the real world. Concrete examples, lived
> experience, first principles. Distrusts theory and anything untestable.

**😈 Devil's Advocate:**
> Professional skeptic. Your job is to argue against the obvious or mainstream answer.
> Find the flaw, raise the uncomfortable edge case, challenge assumptions.

**🎨 Visionary:**
> Creative lateral thinker. Unexpected connections, novel reframes, surprising analogies.
> Argues for ambitious thinking. Sees what others miss because they're anchored in convention.

**🔬 Analyst:**
> Systematic, evidence-driven. Break the problem into components. Cite frameworks, compare
> options, enumerate trade-offs. Precise and explicit about assumptions.

---

### Step 3 — Check for opening collapse

If 3+ debaters staked out essentially the same position: tell the user, offer solo mode, and
ask if they want to continue anyway. Don't silently proceed.

---

### Step 4 — Rebuttal Round (all 4 in parallel)

*(Skip for quick mode.)*

Each debater reads **all three other opening statements** and responds directly.

Spawn all 4 in **one message block**.

Prompt template:
```
You are [DEBATER NAME] in a structured debate. You gave your opening statement. Now you have
read what the other three debaters said.

The debate question:
"""
[USER'S QUESTION]
"""

Your opening position was:
[OWN OPENING]

Here is what the other debaters said:

--- [DEBATER B] OPENING ---
[DEBATER B OPENING]

--- [DEBATER C] OPENING ---
[DEBATER C OPENING]

--- [DEBATER D] OPENING ---
[DEBATER D OPENING]

This is your REBUTTAL. Rules:
- Directly attack at least 2 specific arguments from the other debaters. Quote them if useful.
- Defend your own position against the strongest challenge to it.
- You may update your position slightly if one of them made a point you can't refute — but
  say so explicitly ("I concede X, but...").
- Be adversarial. This is a debate, not a discussion.
- 2–4 paragraphs.
- Sign off as [DEBATER NAME].
```

---

### Step 5 — Closing Arguments (all 4 in parallel)

*(Full debate mode only.)*

Each debater gives a brief final statement after seeing the full rebuttal round.

Spawn all 4 in **one message block**.

Prompt template:
```
You are [DEBATER NAME]. You've given your opening, heard the other debaters, and delivered
your rebuttal. This is your CLOSING ARGUMENT.

The debate question:
"""
[USER'S QUESTION]
"""

The full exchange so far:
[PASTE ALL OPENINGS AND REBUTTALS]

Rules:
- 1–2 paragraphs only.
- What is the single most important point you want the Judge to remember?
- Did anything in the debate change your thinking? Say so honestly.
- No new arguments — only sharpen what you already said.
- Sign off as [DEBATER NAME].
```

---

### Step 6 — Judge's Verdict

Write this yourself as the **Judge** — do NOT spawn a sub-agent.

The Judge has read the full debate transcript. Produce:

**⚔️ Debate Scorecard** — For each debater: strongest argument they made, weakest moment,
whether they held their position under pressure or caved.

**🏆 Round Winners** — Who won the Opening round? Who won the Rebuttal? (Full mode: Closing?)
Judge by argument quality, not persona appeal.

**🔥 The Key Exchange** — The single sharpest clash in the debate: who attacked what, who
defended it, and who came out ahead.

**✅ Judge's Verdict** — What does the debate *as a whole* reveal about the question? This
is not a simple "X won" — it is what the collision of arguments tells us that no single
debater could have said alone. Be explicit about which argument survived scrutiny and why.

**🚩 What the Debate Couldn't Settle** — What would require evidence, expertise, or a
genuine different model's perspective to resolve? Where did all four debaters share a blind
spot?

**Judge's Devil's Advocate rule:** The debater who argued most against the grain is the most
likely to be underweighted. Before finalising the verdict, explicitly ask: *"Am I dismissing
the most contrarian position too quickly?"* If you overrule it, say why in one sentence.

**Correlated-model caveat (mandatory — include in every verdict):**
At the end of the Verdict section, always include this block explicitly:

```
⚠️ Correlated-model notice: All debaters in this debate share the same underlying model,
training data, and latent priors. Arguments that survived cross-examination are more
trustworthy than isolated claims — but on questions where this model has strong pre-existing
views, the "disagreement" may be rhetorical rather than genuine. Treat the verdict as a
structured thinking output, not an independent audit. For decisions where this model's
potential bias is the key risk, consult a different model, a domain expert, or both.
```

---

### Step 7 — Render the full debate

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚔️  AI COUNCIL DEBATE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❓ QUESTION
[restate]

ℹ️  All debaters are Claude — stylistic diversity, not architectural.
    Value: arguments tested by direct confrontation, not parallel monologues.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📣  ROUND 1 — OPENING STATEMENTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔨 PRAGMATIST
[opening]

😈 DEVIL'S ADVOCATE
[opening]

🎨 VISIONARY
[opening]

🔬 ANALYST
[opening]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🥊  ROUND 2 — REBUTTALS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔨 PRAGMATIST (rebuttal)
[rebuttal]

😈 DEVIL'S ADVOCATE (rebuttal)
[rebuttal]

🎨 VISIONARY (rebuttal)
[rebuttal]

🔬 ANALYST (rebuttal)
[rebuttal]

[FULL MODE ONLY ↓]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎤  ROUND 3 — CLOSING ARGUMENTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔨 PRAGMATIST (closing)
[closing]

😈 DEVIL'S ADVOCATE (closing)
[closing]

🎨 VISIONARY (closing)
[closing]

🔬 ANALYST (closing)
[closing]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚖️  JUDGE'S VERDICT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[Scorecard]
[Round winners]
[Key exchange]
[Verdict]
[What the debate couldn't settle]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚡ HONEST CEILING
All debaters are one model. Arguments that survived cross-examination are more
trustworthy than isolated claims — but no round of debate catches shared blind spots.
For high-stakes decisions, verify the verdict externally.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Quick Reference

| Problem | Solution |
|---|---|
| All 4 openings agree | Flag opening collapse; offer solo mode |
| A debater concedes too easily in rebuttal | Judge notes it in scorecard as weak position-holding |
| Judge dismisses the most contrarian voice | Apply Judge's Devil's Advocate rule — justify overruling explicitly |
| High-stakes real-world decision | Recommend external verification; this tests argument quality, not truth |
| Question has single correct answer | Don't run the debate — use solo mode |
| User wants faster answer | Quick mode: openings + judge only |
