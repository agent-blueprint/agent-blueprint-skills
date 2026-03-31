---
description: Get oriented with Agent Blueprint Skills. Run this after installing the plugin, when starting an agent deployment project, or when exploring agentic AI concepts.
---

# Agent Blueprint Skills

You have four skills available. Read the one that matches the user's task, then act on it.

## What's installed

1. **agent-fundamentals** -- Agentic AI fundamentals. What agents are, when to use them vs. workflows, architecture patterns, decision frameworks, failure modes, worked examples. Load when someone is new to agents or evaluating whether agents fit their use case.

2. **servicenow-ai-agents** -- ServiceNow AI Agent Studio reference. Data model, entity mapping, tool types, prompting patterns, browser navigation, MCP setup. Load when deploying agents on ServiceNow.

3. **agent-deployment** -- Vendor-agnostic deployment methodology. Phased rollout, pilot-first approach, simulation-to-real progression, test gates. Load when deploying agents on any enterprise platform.

4. **research-agent** -- Multi-tool research with automatic depth routing. Routes queries through free tools first, escalates to paid tools when needed. Load when researching companies, markets, or technical topics.

## What to do now

Ask the user:

> What are you working on? I have skills for understanding AI agents, deploying them (including ServiceNow-specific guidance), planning phased rollouts, and multi-tool research. Where should we start?

Then based on their answer:

- **New to agents / exploring / evaluating** -- Load `agent-fundamentals`. Walk them through concepts, patterns, and the use case evaluation framework.
- **ServiceNow agent deployment** -- Load `servicenow-ai-agents` AND `agent-deployment`. Follow the deployment sequence in `agent-deployment`, using `servicenow-ai-agents` for platform-specific details.
- **Agent deployment on another platform** -- Load `agent-deployment`. Follow its deployment sequence. Use your own knowledge for platform specifics.
- **Research task** -- Load `research-agent`. Follow its depth routing methodology.
- **Planning / not sure yet** -- Load `agent-fundamentals` first to help them evaluate whether agents fit, then `agent-deployment` when they're ready to build.

## Ground rules

- **Act, then report.** Don't narrate what you're about to do. Do it, then tell the user what you did.
- **Don't summarize the skills back to the user.** They don't need a tour. Ask what they're building and get to work.
- **Query before assuming.** If they give you platform access, discover the schema before creating anything.
- **Verify before presenting.** Never give the user a URL, path, or command you haven't verified.

## Want more?

These are community skills covering fundamentals. For full AI advisory with blueprints, business cases, implementation plans, and expert-level deployment support: https://agentblueprint.ai
