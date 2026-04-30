# Teranode as the productized version of Triangulate

> Strategic memo. Captures the connection between the Triangulate developer skill and the Teranode product moat. Authored 2026-04-29 alongside the skill itself.

## The principle (developer scale)

Triangulate, as a Claude Code skill, is **static**: it defines a fixed set of trigger points and a fixed set of outside sources. The discipline is in firing the right tool at the right moment. It does not learn — every developer who installs it gets the same skill.

This is good. The skill is reliable, predictable, copy-pasteable. Distribution = adoption.

## The principle (product scale)

Teranode AI productizes the same principle for regulated financial advisors, but with one critical difference: **it learns.**

Every Council run produces a structured decision record:
- Five independent role outputs (Risk, Builder, Strategist, Contrarian, Chairman)
- Per-role model attribution
- Confidence scores
- Preserved dissent
- Final synthesis

This record is **training data for a routing layer**. Specifically:

| Signal | What it teaches |
|---|---|
| Which models agreed on what | Redundancy. If two models converge 95% of the time, drop one — wasted spend. |
| Which model dissented and was right | Diversity value. The model that catches what others miss is the most valuable seat at the table. |
| Which role each model carries best | Specialization. GPT-X may be the strongest Builder. Claude-Y the strongest Risk. Gemini-Z the strongest Contrarian. |
| Confidence calibration | When this model says 85%, how often is it actually right? Adjust the displayed confidence accordingly. |
| Advisor feedback (when collected) | Did the advisor act on the recommendation? Was the dissent material? Ground truth for routing. |

## The static → dynamic transition

**Today (static):** [`council-site/lib/council/model-policy.json`](../../../../council-site/lib/council/model-policy.json) pins specific models to specific roles. The model-freshness GitHub Action files an issue when frontier models drift, but adoption is manual.

**Tomorrow (dynamic, learned):**

```
Every Council run logs:
  - inputs (anonymized question, scenario type)
  - per-role model assignment
  - per-role output quality signals (length, dissent presence, downstream advisor action)
  - synthesis quality signals
  → time-series store

Weekly batch job:
  - Compute per-(role, scenario_type, model) performance scores
  - Update routing policy
  - A/B test new policy against incumbent
  → updated model-policy.json (or runtime routing decision)

Monthly audit:
  - Drift in model availability, pricing, capability
  - Re-evaluate role assignments
  - Replay historical scenarios against new candidate models
```

## Why this is a moat

Competitors can copy:
- Multi-model architectures (5 models in parallel — done in a weekend)
- Role definitions (Risk, Builder, Strategist, Contrarian, Chairman)
- The decision-record artifact format

Competitors cannot copy:
- **Accumulated routing intelligence** from N thousand advisory questions
- **Calibration data** for each model's confidence per question type
- **Dissent-correctness mapping** (when did Contrarian-on-model-X catch real risks vs. cry wolf)
- **Advisor feedback loops** (which decisions led to client outcomes that confirmed the dissent)

This is the **data flywheel**: every API call makes the routing smarter, which improves output quality, which retains advisors, which feeds more data back.

## Pitch implication

Slide 3 of the deck currently positions Teranode as "five models is a decision." That's the static pitch.

The dynamic pitch upgrades to:

> **One model is a guess. Five is a decision. The right five — picked by data, getting better every run — is a moat.**

This is the move from "product" to "compounding asset."

## Implementation sketch

Where this would live in the existing codebase:

| Concern | Location |
|---|---|
| Per-run logging | New: `council-site/lib/council/run-log.ts` (writes to durable store; current `recordFailure` is a precedent) |
| Routing decisions | Extend: `council-site/lib/council/model-policy.json` → become a function (`pickModel(role, scenarioType): modelId`) |
| Performance batch job | New: scheduled function or external worker reading the run log, computing scores, writing updated policy |
| Confidence calibration | New: per-model calibration table consulted at synthesis time |
| Advisor feedback collection | New: post-decision survey route (`/api/feedback/decision/[recordId]`) |

None of this is hard. It's straightforward observability + scoring + policy update. **The hard part is having the volume of API calls to make the data load-bearing — which is why "ship and route traffic" is the priority over "build the perfect routing layer first."**

## What this means for the raise

This memo upgrades the moat narrative from "we have 5 prompts" (defensible only as long as competitors haven't shipped) to "we have a learning routing layer" (defensible as long as we have traffic). VC asks "what's defensible?" → answer is the data flywheel, not the prompts.

Add to the deck. Add to the data room. Add to the founder pitch.

## Cross-link

The static developer-side principle: see this skill's [SKILL.md](../SKILL.md). Same idea, two scales, one founder.
