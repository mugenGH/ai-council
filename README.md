# ai-council

A structured debate skill for [Claude Cowork](https://claude.ai), inspired by [Karpathy's llm-council](https://github.com/karpathy/llm-council) — but using **only Claude sub-agents** with no external API keys required.

Instead of multiple AI providers, four Claude sub-agents play distinct debater roles, directly attack each other's arguments across multiple rounds, and a Judge delivers a verdict.

## How it works

```
Round 1 — Opening Statements   (4 agents in parallel)
Round 2 — Rebuttals            (4 agents, each reads + attacks the other 3)
Round 3 — Closing Arguments    (full mode only)
          Judge's Verdict      (written by main Claude)
```

Unlike parallel monologue tools, debaters here **read and directly attack each other's arguments** in the rebuttal round. Arguments that survive cross-examination are more trustworthy than those never challenged.

## Modes

| Command | Rounds | Best for |
|---|---|---|
| `/ai-council quick debate: [question]` | Openings + Judge | Fast takes |
| `/ai-council [question]` | Openings + Rebuttals + Judge | Most questions |
| `/ai-council full debate: [question]` | + Closing arguments | Hardest decisions |

## Install

1. Download `claude-council.skill`
2. Open Claude Cowork
3. Click **Save skill** on the file card

## What makes it different from a single prompt

- Each debater is **role-constrained** — forced to argue from one angle only
- The **rebuttal round** requires each agent to read and name-attack specific claims from the others
- The **Judge's scorecard** tracks round winners, the key exchange, and explicitly names what the debate couldn't settle
- A mandatory **correlated-model caveat** in every verdict tells you when to trust the output less

## Honest limitations

All debaters share the same underlying Claude model — diversity is stylistic, not architectural. On questions where Claude has strong pre-existing views, disagreement may be rhetorical rather than genuine. This is a structured thinking tool, not a substitute for a true multi-model council or human expert review.

## Versioning

| Version | Key change |
|---|---|
| v1.0 | Original councillor format (parallel monologues) |
| v1.1 | Honest "single-model ensemble" labeling, USE/DON'T USE rules |
| v1.2 | Solo mode pre-flight, persona collapse detection, DA structural protection |
| v2.0 | Full debate redesign — rebuttals, Judge's verdict, three modes |
| v2.1 | Mandatory correlated-model caveat in every Judge verdict |

## Credits

Inspired by [karpathy/llm-council](https://github.com/karpathy/llm-council).
Built iteratively using the skill's own debate format to rate and improve itself.
