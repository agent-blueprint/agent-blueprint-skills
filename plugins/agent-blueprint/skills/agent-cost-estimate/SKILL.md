---
name: agent-cost-estimate
description: Estimate the tokens and dollar cost of an AI agent task or recurring agent job, using measured runs of ten everyday jobs on Claude Code and Codex. Use when someone asks what an agent, agent task or automation will cost to run, how many tokens a task uses, or needs the cost section of an agent business case.
---

# Estimate what an agent task costs

Use measured numbers instead of guessing. `references/benchmark-2026-11-02.md` has typical and worst-case tokens and cost for ten everyday jobs, five runs each on two agents, all of which passed their checks. `references/benchmark-2026-11-02-summary.json` has the same numbers as data.

## Steps

1. Ask, or work out, what the job is, how much the agent has to read for it (files, documents, rows, emails), how often it runs, and which agent and model will run it.
2. Pick the closest job in the benchmark and compare input sizes. Each job lists how big its input was, and the test inputs are small (the 200 emails average about 20 words). Real emails, threads and documents are usually longer, so ask or estimate the real size and scale by it. Size matters more than difficulty: in these runs cost followed how much the agent read.
3. Start from that job's typical input and output tokens. Keep the floor in mind: even tiny jobs used about 29k input tokens on Claude Code and 60k on Codex, because the agent re-reads its own instructions and tools at every step.
4. Turn tokens into dollars with the model's current prices. Most input is cached (about 81% on Claude Code and 88% on Codex in these runs), and cached input is billed far lower. If you don't know current prices, don't invent them: give the token estimate and say which prices to look up.
5. Give a typical figure and a worst case. Most jobs' worst run was within 1.3x of typical, and one was 2.3x, so use at least 1.5x for a budget.
6. For a recurring job, multiply by runs per month, and say that people's time to review the work is usually a bigger cost than tokens.

## What to tell the person

- The estimate, its range, and the benchmark job you based it on.
- That the benchmark used small made-up jobs on 2 November 2026, with the agents and models named in the reference, so their own real runs should replace it as soon as they have them.
- If they're writing up the agent for approval, the `agent-one-page` skill has a cost section these numbers fit into.

Source and method: https://agentblueprint.ai/agent-cost-benchmark
