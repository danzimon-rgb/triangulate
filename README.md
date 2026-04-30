# Triangulate

> Verify a claim by checking 2+ independent sources, before committing to your first answer.

**A Claude Code skill that adds the discipline of cross-source review at specific decision points.**

LLMs anchor on first instinct. When the model produces output AND evaluates it, evaluation runs on the same weights that produced it — same blind spots, same biases. Self-review under those conditions is rubber-stamp review.

`triangulate` is the **meta-decision skill** that fires existing review tools (`second-opinion`, `adversarial-reviewer`, `dispatching-parallel-agents`, `Agent` subagents) at the right moments — and covers surfaces those skills don't, like compliance copy, image prompts, architectural alternatives, and async state verification.

## Install

**One command:**

```bash
git clone https://github.com/danzimon-rgb/triangulate ~/.claude/skills/triangulate
```

Restart Claude Code. The skill auto-loads on session start. The description triggers Claude to invoke it automatically when relevant; you can also invoke it explicitly with the `Skill` tool.

**Project-scoped install (this repo only, not user-level):**

```bash
git clone https://github.com/danzimon-rgb/triangulate .claude/skills/triangulate
```

## What's in the box

- **Six trigger points** where the agent must fork to an outside source automatically: compliance/legal copy, architectural alternatives, image/video prompts, debug loops >2 cycles, cross-cutting decisions, **async state verification (propagation patience)**.
- **A taxonomy of "outside sources"** — different model > fresh subagent > persona shift, in order of independence.
- **A maturity-test gating question** to ask before shipping: *"would another model produce a meaningfully different answer here?"*
- **Concrete invocation patterns** — which tool to fire for which trigger.
- **A propagation-windows reference table** — Vercel, GitHub API, DNS, Supabase, Stripe, etc. — so you wait the right amount of time before claiming verification.
- **Anti-patterns** — when NOT to fork (routine work, after the user said ship, etc.)

## Origin

Authored by Dan Zimon (founder, [Teranode AI](https://teranode.ai)) on 2026-04-29, after watching a parallel Codex session out-architect Claude on a streaming-progress refactor. The lesson:

> **The cost of the 30-second pause to fork the question is much lower than the cost of shipping the inferior pattern.**

The skill grew on its first day in public: Trigger #6 (async state verification, "go slow to go fast") was added the same night after Claude polled a Vercel deploy too quickly and produced wrong conclusions on still-propagating data — Dan named the discipline, we built the artifact, the world got the lesson. That's the pattern this whole skill is about.

Same principle Teranode ships at the product level — five reasoning models forking advisory questions in parallel — now applied at the developer level. **Two scales, one principle.**

## How it complements existing skills

| Existing skill | What it does | What triangulate adds |
|---|---|---|
| `second-opinion` (trailofbits) | Shells out to Codex CLI + Gemini CLI | The trigger logic — *when* to invoke it, including non-code surfaces |
| `adversarial-reviewer` (engineering-team) | 3 hostile review personas | The non-code applications and the cross-model layer |
| `dispatching-parallel-agents` (superpowers) | Fans out independent debug tasks | The decision rule for *whether* to dispatch in the first place |
| `brainstorming` (superpowers) | Explores design space before deciding | The fork-on-finalists step after design space is mapped |

`triangulate` doesn't replace any of these — it tells the agent **when** to reach for them.

## The Teranode connection

This skill is the static, developer-facing version of a principle that Teranode AI productizes for regulated financial advisors at scale:

- **Static (this skill):** fixed trigger points, fixed sources, fixed decision rule. Reliable, copy-pasteable.
- **Dynamic (Teranode):** learning routing layer that gets smarter with every Council run — which model excels at which question, which dissent was material, which calibration was off. **Every API call is training data.**

See [`skills/triangulate/references/teranode-routing-layer.md`](skills/triangulate/references/teranode-routing-layer.md) for the strategic breakdown of how the principle scales.

If you're an advisor or RIA who wants the productized version: [teranode.ai](https://teranode.ai).

## License

MIT. Free to use, fork, modify, redistribute. Attribution appreciated, not required.

## Contributing

Open an issue or PR. Small, focused changes welcome. Especially:

- New trigger points the original draft missed
- Better invocation patterns for specific tools
- Anti-patterns observed in the wild
- Translations of the principle for non-Claude environments

## Author

[Dan Zimon](https://teranode.ai/trust) — Series 7, Series 66, insurance licensed; fourteen years across institutional finance. Building Teranode AI, decision infrastructure for regulated financial advisors.

OpenClaw ecosystem.
