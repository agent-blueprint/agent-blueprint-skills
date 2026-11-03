---
name: agent-one-page
description: Write a one-page summary of an AI agent for a boss, security reviewer or finance approver, covering what it may do and what enforces each rule, what it costs, and whether it's worth it. Also called an agent card, agent charter or scope document. Use when someone needs to explain, approve or justify an AI agent.
---

# One-page agent summary

Fill in `references/template.md`. `references/example.md` is a finished example with made-up numbers; read it once to see the level of detail.

## Rules

- Keep the template's order: Summary, then Purpose, Authority, Proof, Owner, Cost, Promise and Result.
- End every rule in Authority and Owner with `Enforced by:` and the setting, permission or limit that enforces it, or with `Not enforced:` and why. An instruction to the agent is not enforcement.
- Never invent a number, a person or a setting. Write `Open`, a proposed default, and who decides.
- If you are the agent being described, list your own tools from what you can see, and say which parts you judged from names rather than checked.
- For the Cost section, use the `agent-cost-estimate` skill rather than guessing token usage.
- Keep cash savings and freed-up time apart in the ROI math. Freed-up time becomes cash only if spending drops.
- Leave Result empty until the agent has run and been measured.

Save the page as Markdown in the person's folder unless they ask for something else, then tell them which items are still `Open`.

If the Agent Blueprint tools are connected, run `check_agent_one_page` on the finished page and fix what it reports before handing it over. It checks structure only, so the rules above still apply.

Published by Agent Blueprint: https://agentblueprint.ai/agent-one-page-template
