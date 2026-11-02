# Agent Blueprint

Three skills for people who run AI agents and need plain answers about them: what a job will cost, what the agent is allowed to do, and whether it's worth it.

## Skills

- **agent-cost-estimate.** Estimates what an agent task costs from measured runs instead of a guess. It starts from a benchmark of ten everyday jobs (code changes, data cleanup, SQL questions, long contracts, email triage and more), each run five times on Claude Code and on Codex, then scales by how much your agent has to read. It gives a typical and a worst-case cost and tells you which prices to check. Full results: https://agentblueprint.ai/agent-cost-benchmark
- **agent-one-page.** Writes a one-page summary of an agent for the people who have to approve it: what the agent may read and change, what actually enforces each rule, who owns it, what it costs and whether it's worth it. Anything nobody knows yet is marked Open, with who decides.
- **draw-my-agent.** Has the agent map its own setup: every tool and connection, sorted into what it can read and what it can change, with its riskiest powers flagged. It saves the map as one HTML file you open in a browser.

## Try it

- "Roughly what will it cost per month to have an agent triage 300 support emails a day?"
- "My manager wants one page on what our invoice agent is allowed to do and whether it's worth it."
- "Draw a picture of what you can access and change."

## What it runs and sends

Nothing leaves your machine because of this plugin. It has no MCP server, no hooks, no scripts and no network calls. The skills are instructions plus reference files (the benchmark results, a page template, an example page and an HTML template). The only file it writes is the one you ask for, such as `my-agent-blueprint.html` from draw-my-agent, saved in your working folder. That HTML file loads nothing from the internet.

## About

Made by Agent Blueprint, https://agentblueprint.ai. Licensed under Apache-2.0. Privacy: https://agentblueprint.ai/privacy. Questions: info@agentblueprint.ai.
