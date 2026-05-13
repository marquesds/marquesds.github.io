---
title: A style harness for AI coding agents
author: lucas
date: 2026-05-13 09:00:00 -0300
categories: [Blogging, Essay]
tags: [ai-agents, harness-engineering, coding-style, claude-code]
---

Coding agents write whatever you give them. Give them nothing and they default to averages from their training data. Give them your style and they write your style. That gap is where I have been spending most of my AI engineering time.

So I packaged my preferences into a small repo called [style](https://github.com/marquesds/style){:target="_blank"}. It is language agnostic, single source of truth, and installs into any agent I use (Claude Code, Cursor, and a few others). This post covers the decisions behind it.

Quick disclaimer before anything else: none of this is a silver bullet. I did not invent any of these techniques either. They are my defaults, and yours will probably be different.

# What style is

Three buckets feed every agent from one place.

* **Rules** are always on. Workflow, code quality, design principles, observability, and the skills catalog.
* **Skills** are loaded on demand. The catalog stays in context, and a skill body only loads when a task matches it. TDD, hexagonal architecture, REST design, code review, threat modeling, caching, and more.
* **Commands** are slash prompts: `/plan`, `/tdd`, `/bug`, `/review`, `/done`, `/commit`, `/threat-model`, `/refactor`.

Installation writes only into a managed namespace inside each agent's config. Uninstall touches only files carrying a marker. Reruns are idempotent. Boring on purpose.

# The decisions

## Defer side effects

Pure functions hold logic. Effects live at the edge. The shell does I/O, the core does math.

Good:

```python
def total(items: list[Item]) -> Money:
    return sum((i.price for i in items), Money.zero())

async def checkout(state, db):
    receipt = build_receipt(state, total(state.items))
    await db.save(receipt)
    return receipt
```

Bad:

```python
async def total(items, db):
    s = Money.zero()
    for i in items:
        p = await db.get_product(i.id)
        s += p.price
    return s
```

The second version hides a database call inside something named like math. Untestable without mocks. Agents pattern match on names, and a function named `total` should not surprise the caller.

## Bug first debugging

Reproduce the bug as a failing test. Then fix it. Then keep the regression test forever.

Three benefits. The repro proves you understand the bug before touching code. The fix is scoped to the failing assertion, not the symptom. The regression test stays long after everyone forgets the incident.

Agents are quick to propose a fix. This is the rule they need most.

## Design by contract

Every function has preconditions, postconditions, and invariants. Subtypes inherit them and may only weaken preconditions or strengthen postconditions. Liskov first among SOLID.

Encode what types can encode. Use asserts and property tests for the rest. Honest interfaces beat clever inheritance.

## Avoid N+1 queries

The classic trap: one query returns N rows, you loop, each iteration fires another query. Twenty rows, twenty one queries.

Eager load, batch, or use a DataLoader. Catch it at review time, not in production. Add it to your perf budget. Agents reproduce N+1 patterns from training data routinely, and naming the rule helps them notice.

## Subagent first

Reading three files inline burns the main context. Spawning a subagent to read those same files and return a small summary compresses the result around twenty five times.

I use cheap models (haiku, flash, composer 2) for reading code, running tests, and listing call sites. I use the expensive model only for the edit itself. The main thread stays sharp, and the bill stays small.

# What I am watching next

Even with all these techniques shared around, every codebase is different. Architecture, documentation, tests, sharp edges, and agent failure modes are different in every repo. So each codebase probably needs its own continuous improvement loop for agentic coding.

That is the bet behind a weekend project I just published called [Kaizen](https://github.com/marquesds/kaizen){:target="_blank"}: telemetry from code agents, retrospectives focused on agent performance, and recommendations for harness improvements based on real agent behavior. Optional export to Datadog or PostHog for teams that want cross-repo insights.

One thing that helped a lot while building Kaizen was using Quint (built and maintained by Gabriela Moreira) to verify parts of the system. An executable spec gave me a tighter feedback loop and let me use weaker LLMs for most of the implementation.

# Closing

Agentic coding will not be won by better models alone. It will also be won by better harnesses.

What are your bets in harness engineering? I would love to learn from your style too.

Thanks for reading this post! Bye 👋🏾
