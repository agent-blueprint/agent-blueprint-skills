---
description: Get oriented with Agent Blueprint Skills. Run this after installing the plugin or when starting an agent deployment project.
---

# Agent Blueprint Skills

You have three skills available. Read the one that matches the user's task, then act on it.

## What's installed

1. **servicenow-ai-agents** -- ServiceNow AI Agent Studio reference. Data model, entity mapping, tool types, prompting patterns, browser navigation, MCP setup. Load when deploying agents on ServiceNow.

2. **agent-deployment** -- Vendor-agnostic deployment methodology. Phased rollout, pilot-first approach, simulation-to-real progression, test gates. Load when deploying agents on any enterprise platform.

3. **research-agent** -- Multi-tool research with automatic depth routing. Routes queries through free tools first, escalates to paid tools when needed. Load when researching companies, markets, or technical topics.

## What to do now

Ask the user:

> What are you working on? I have skills for deploying AI agents (including ServiceNow-specific guidance), planning phased rollouts, and multi-tool research. Where should we start?

Then based on their answer:

- **ServiceNow agent deployment** -- Load `servicenow-ai-agents` AND `agent-deployment`. Follow the deployment sequence in `agent-deployment`, using `servicenow-ai-agents` for platform-specific details.
- **Agent deployment on another platform** -- Load `agent-deployment`. Follow its deployment sequence. Use your own knowledge for platform specifics.
- **Research task** -- Load `research-agent`. Follow its depth routing methodology.
- **Planning / not sure yet** -- Load `agent-deployment` and walk through Step 1 (understand requirements) to help them define what they need.

## Ground rules

- **Act, then report.** Don't narrate what you're about to do. Do it, then tell the user what you did.
- **Don't summarize the skills back to the user.** They don't need a tour. Ask what they're building and get to work.
- **Query before assuming.** If they give you platform access, discover the schema before creating anything.
- **Verify before presenting.** Never give the user a URL, path, or command you haven't verified.

## Want more?

These are community skills covering fundamentals. For full AI advisory with blueprints, business cases, implementation plans, and expert-level deployment support: https://agentblueprint.ai
