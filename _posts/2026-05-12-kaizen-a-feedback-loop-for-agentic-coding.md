---
title: Kaizen, a feedback loop for agentic coding
author: lucas
date: 2026-05-12 09:00:00 -0300
categories: [Blogging, Essay]
tags: [ai-agents, harness-engineering, agentic-coding, observability, kaizen]
---

A coding agent that wastes tokens looks fine from the outside. Tests pass. The PR opens. You read the diff and it works. What you do not see is the thirty tool calls it took to get there, the file it read four times because it forgot, the skill that fired but contributed nothing, and the rule it quietly ignored. We instrument almost everything in modern software except the thing that now writes a meaningful fraction of our code.

That asymmetry is the problem I wanted to put a name on. So I built a small tool for it, called [Kaizen](https://github.com/marquesds/kaizen){:target="_blank"}.

# The bet

Better models help. Better techniques help. But every codebase carries its own history. Architecture, tests, documentation, sharp edges, and the specific places where agents fall over are different in every repo. Generic harness advice goes a long way, and then it stops. From that point on, each codebase probably needs its own continuous improvement loop. The Japanese word for that idea is kaizen (改善): small, steady improvements driven by what you actually observe, not by what you wish were true.

# What it does

Three stages, in order. **Observe**: tail agent transcripts from Cursor, Claude Code, Codex, Goose, OpenClaw, OpenCode, and Copilot into a workspace-local SQLite store. **Summarize**: project raw events into tool-span hierarchies, file facts, and commit-pinned repo snapshots. **Propose and measure**: a deterministic engine ranks improvement bets backed by real evidence, and a git-bound A/B mode tells you whether the change worked. Everything lives in a single Rust binary. No hosted service to opt into. No login.

# What Kaizen is not

It is not a dashboard. There is a TUI for browsing sessions locally, but the headline output is not a chart, it is a ranked list of bets with evidence and apply steps. It is not a real-time monitor either; the loop is weekly, on purpose, because most useful patterns only show up across several sessions. And it is not an LLM-as-judge tool by default. There is an optional eval feature, but the retro engine itself runs on plain heuristics that anyone can read, audit, and disagree with. The shape of the tool is closer to a personal coach than a status page.

# Why local-first

Agent telemetry is sensitive. A single session can leak source code, secrets, environment variables, absolute paths, and the email address attached to every commit. Sending that to a SaaS by default would be a privacy disaster, and any tool that wants to be useful on real repositories has to take that seriously.

Kaizen keeps everything on disk. SQLite by default, with WAL for the hot append log and a Parquet cold tier after seven days. Sync to PostHog, Datadog, or OTLP is opt-in, and fully redacted client-side before anything leaves your machine. The redaction pass itself is covered by a formal spec, so the guarantee is checked rather than asserted.

```bash
kaizen init        # idempotent: drops hooks into ~/.claude and ~/.cursor
kaizen summary     # cost/count roll-up by agent, model, repo
```

The day-one experience is meant to be boring. You install, you keep working, and the tool quietly fills its store while you forget about it.

# Why deterministic retros

The obvious move is LLM-as-judge. Feed a week of transcripts into a model and ask which agent did best. I tried it. It is slow, expensive, non-reproducible across runs, and biased toward whichever style the judge happens to prefer. Worse, it produces bets that read like horoscopes. "Improve your context window usage" is not actionable.

Kaizen runs a pure heuristic engine instead. It reads the real session events and the real repo facts, and emits ranked bets like *"split `auth.py` (412 lines, edited in 8 sessions, average tool-span fan-out 11)"*. Every bet carries a confidence level and an estimated savings, and the ranking is `confidence × expected_tokens_saved / (effort_minutes + 1)`. The same Rust function powers the CLI and the agent integration, so a human and an agent looking at the same week of data get the exact same list.

```bash
kaizen retro       # ranked improvement bets, with evidence and apply steps
```

# Why git-bound A/B experiments

A bet means nothing until you measure whether it worked. Kaizen ships an experiment mode tied directly to git history. You pin the control group to a commit, apply your change, and the next batch of sessions becomes the treatment. Median deltas come back with bootstrap confidence intervals (10,000 resamples, 95% by default), which is overkill for a side project and exactly right for an argument with yourself a week later.

```bash
kaizen exp new --bind git --hypothesis "split auth.py reduces tokens/session by 20%"
# ...work for a week...
kaizen exp report
```

The point is not statistical rigor for its own sake. The point is that "delete this unused skill", "add this rule", and "split this file" stop being vibes. You ship the change, you wait, and the tool tells you whether it earned its keep. If it did not, you revert and move on with one less hypothesis polluting your future.

# Why MCP

Kaizen exposes its read paths as a stdio MCP server. The agent itself can call `kaizen retro` from inside its own session and reason about its own performance. This sounds gimmicky until you watch it happen: the model proposes a fix, queries Kaizen, sees that a similar change last week regressed by 8% on token usage, and walks the recommendation back. A small, self-aware loop. Not magic, just a feedback path the agent did not have before.

# Why Quint

The piece I am most happy with is the spec. Kaizen has a handful of properties that absolutely must hold. Redaction is complete before any sync. Ingest is idempotent. The session lifecycle never loses an event. The retro pipeline is pure. These are easy to claim and hard to verify with unit tests alone.

So a chunk of the design lives as [Quint](https://github.com/informalsystems/quint){:target="_blank"} models, written in the language built and maintained by Gabriela Moreira and her team at Informal Systems. Quint catches the kind of bug that unit tests do not, and an executable spec turned out to be the tighter feedback loop I needed. With the spec in front of me, I could lean on weaker LLMs (composer 2, haiku) for most of the implementation and trust them, because the spec held the contract. Strongly recommend trying it on your next side project.

# Closing

Agentic coding will not be won by better models alone. It will also be won by better harnesses, better feedback loops, and a willingness to treat agent behavior as something you can measure and improve. Kaizen is my bet on what that loop looks like for a single codebase. Yours will look different.

What are you betting on?

Thanks for reading this post! Bye 👋🏾
