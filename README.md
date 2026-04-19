# Agent Blueprint Skills

Open-source AI agent skills for enterprise deployment. Built for coding agents (Claude Code, Codex, Cursor, Copilot, Windsurf) using the [Agent Skills](https://agentskills.io) open standard.

**7 skills. Zero vendor lock-in. Works with any coding agent.**

### Skills

| Skill | What it does |
|-------|-------------|
| [agent-fundamentals](plugins/agent-blueprint-skills/skills/agent-fundamentals/SKILL.md) | Agentic AI fundamentals: what agents are, when to use them vs. workflows, architecture patterns, decision frameworks, failure modes, worked examples |
| [servicenow-ai-agents](plugins/agent-blueprint-skills/skills/servicenow-ai-agents/SKILL.md) | ServiceNow AI Agent Studio reference: data model, entity mapping, tool types, prompting patterns, browser navigation, MCP setup |
| [agent-deployment](plugins/agent-blueprint-skills/skills/agent-deployment/SKILL.md) | Vendor-agnostic deployment methodology: phased rollout, pilot-first, test gates, access tier adaptation |
| [research-agent](plugins/agent-blueprint-skills/skills/research-agent/SKILL.md) | Multi-tool research with automatic depth routing: free tools first, paid escalation, caching, structured output |

### Blueprint Patterns

Pre-designed agent team architectures for common enterprise use cases. Each pattern describes the full agent team, roles, orchestration, and integration points. Use as a starting point, or generate a full customized blueprint via the [Agent Blueprint MCP server](https://github.com/agent-blueprint/mcp-server).

| Pattern | What it does |
|---------|-------------|
| [it-service-desk-triage](plugins/agent-blueprint-skills/skills/it-service-desk-triage/SKILL.md) | 5-agent IT help desk: triage, classification, knowledge search, auto-resolution, escalation |
| [procurement-rfx-processing](plugins/agent-blueprint-skills/skills/procurement-rfx-processing/SKILL.md) | 6-agent RFP/RFQ/RFI lifecycle: intake, requirements, vendor matching, compliance, evaluation, communication |
| [customer-onboarding](plugins/agent-blueprint-skills/skills/customer-onboarding/SKILL.md) | 5-agent onboarding pipeline: intake, verification, provisioning, communication, handoff |

## Install

### Claude Code

Add the marketplace, then install the plugin:

```
/plugin marketplace add agent-blueprint/agent-blueprint-skills
/plugin install agent-blueprint-skills@agent-blueprint
```

Skills appear in your session automatically. Claude loads them when your task matches the skill description.

### OpenAI Codex CLI

Copy skills into your project:

```bash
git clone https://github.com/agent-blueprint/agent-blueprint-skills.git /tmp/ab-skills
cp -r /tmp/ab-skills/plugins/agent-blueprint-skills/skills/* .agents/skills/
```

### Cursor / Copilot / Windsurf / Any Agent

Clone this repo and point your agent to the SKILL.md files:

```bash
git clone https://github.com/agent-blueprint/agent-blueprint-skills.git .agent-skills
```

The SKILL.md format is an open standard. Any agent that reads markdown can use these skills.
Skills are in `plugins/agent-blueprint-skills/skills/`.

### Manual (single skill)

Copy just the skill you need:

```bash
# ServiceNow AI Agents
curl -o SKILL.md https://raw.githubusercontent.com/agent-blueprint/agent-blueprint-skills/main/plugins/agent-blueprint-skills/skills/servicenow-ai-agents/SKILL.md

# Deployment Methodology
curl -o SKILL.md https://raw.githubusercontent.com/agent-blueprint/agent-blueprint-skills/main/plugins/agent-blueprint-skills/skills/agent-deployment/SKILL.md

# Research Agent
curl -o SKILL.md https://raw.githubusercontent.com/agent-blueprint/agent-blueprint-skills/main/plugins/agent-blueprint-skills/skills/research-agent/SKILL.md
```

## What's in each skill

### agent-fundamentals

A practical guide for understanding AI agents in enterprise environments:

- **Agent vs. workflow vs. chatbot**: When do you actually need an agent? Decision framework with clear criteria
- **Anatomy of an agent**: Instructions, tools, memory, guardrails, orchestration
- **Architecture patterns**: Single agent, orchestrator + workers, pipeline, human-in-the-loop — with selection criteria
- **Common failure modes**: Over-agenting, tool sprawl, vague instructions, missing guardrails, skipping validation
- **Use case evaluation framework**: 9-question walkthrough to determine if a process needs an agent
- **Worked examples**: Three detailed walkthroughs (simple ticket triage, workflow-not-agent invoice processing, complex multi-agent procurement)

### servicenow-ai-agents

Everything a coding agent needs to deploy AI agents on ServiceNow's AI Agent Studio:

- **Data model**: All `sn_aia_*` tables, fields, and relationships
- **Entity mapping**: How blueprint roles (Manager, Worker) map to ServiceNow entities (Workflow, Agent)
- **Tool types**: Record Operation, Script, Flow, Search Retrieval, MCP Tool, etc.
- **Prompting patterns**: Markdown format, parameter naming, gate definitions
- **Browser navigation**: Verified URL patterns and Playwright selectors for AI Agent Studio
- **MCP setup**: servicenow-mcp-server and nowsdk-ext-mcp configuration

### agent-deployment

A structured methodology for deploying AI agents on any enterprise platform:

- **Phased rollout**: Pilot first, validate, then expand
- **Simulation-to-real progression**: Prove orchestration works, then connect real data
- **Test gates**: Mandatory validation checkpoints before expanding scope
- **Access tier adaptation**: Full access, read-only, or no-access workflows
- **Research-backed**: Cites Microsoft, McKinsey, Forrester on why simulation-only pilots fail

### research-agent

A reusable research methodology that routes queries through the right tools:

- **Depth routing**: WebSearch (free) to Brave to Perplexity to Gemini (expensive)
- **Escalation rules**: Start cheap, escalate when results are thin
- **Supplemental sources**: Financial filings, careers pages, reviews, competitive intel
- **Caching**: Avoid redundant queries with freshness-aware cache
- **Tool-agnostic**: Works with whatever MCP servers you have configured

### Blueprint Patterns

#### it-service-desk-triage

A 5-agent architecture for automating IT service desk operations:

- **Triage Agent**: Extracts structured data, sets priority, routes tickets
- **Classification Agent**: Deep categorization, duplicate detection, assignment group recommendation
- **Knowledge Search Agent**: Semantic search across knowledge base, relevance ranking
- **Resolution Agent**: Auto-resolves known patterns (password resets, access requests, known errors)
- **Escalation Agent**: Packages context for human analysts, monitors SLA compliance
- **Orchestration**: Routing pattern with Knowledge Search as utility agent

#### procurement-rfx-processing

A 6-agent architecture for end-to-end RFx lifecycle automation:

- **Intake Agent**: Extracts requirements from source documents, creates structured RFx record
- **Requirements Agent**: Validates requirements, identifies gaps and conflicts, builds scoring rubric
- **Vendor Matching Agent**: Identifies qualified vendors from database, past performance, compliance status
- **Compliance Agent**: Validates regulatory and policy compliance at every stage
- **Evaluation Agent**: Scores vendor responses, produces comparative analysis and award recommendation
- **Communication Agent**: Manages all stakeholder and vendor communications
- **Orchestration**: Orchestrator + Workers with parallel vendor matching and compliance

#### customer-onboarding

A 5-agent architecture for customer onboarding pipeline automation:

- **Intake Agent**: Receives applications from any channel, validates completeness, sets risk tier
- **Verification Agent**: Identity, compliance, KYC/AML, document validation
- **Provisioning Agent**: Account creation, billing, access, integrations (parallel tasks)
- **Communication Agent**: Status updates, document requests, activation packages
- **Handoff Agent**: Transitions to ongoing service with structured CSM package
- **Orchestration**: Pipeline with parallel branches, async waits for customer actions

## Cross-platform compatibility

These skills follow the [Agent Skills](https://agentskills.io) open standard, supported by:

| Platform | Skill location | How it works |
|----------|---------------|-------------|
| Claude Code | `.claude/skills/` or plugin | Loaded via plugin system or project skills |
| OpenAI Codex | `.agents/skills/` | Same SKILL.md format, automatic discovery |
| Cursor | Project context | Read SKILL.md as context |
| GitHub Copilot | `.github/skills/` | Agent Skills standard |
| Any agent | Direct file read | SKILL.md is just markdown |

## Want more?

These community skills cover the fundamentals. [Agent Blueprint](https://agentblueprint.ai) provides:

- **Full AI advisory pipeline**: Business profile analysis, AI readiness assessment, use case generation, agent blueprints, business cases, and implementation plans
- **Expert-level deployment skills**: Battle-tested patterns, version-specific configuration, security hardening, and proprietary gotchas from real enterprise deployments
- **Living blueprints**: Ongoing advisory with bidirectional state sync, performance monitoring, and strategic recommendations
- **MCP server + CLI**: `agentblueprint` npm package for programmatic blueprint access

## Contributing

Contributions welcome. Found a missing URL pattern, a better prompting technique, or a useful deployment tip? Open a PR.

Please keep contributions:
- **Vendor-agnostic** in deployment and research skills
- **Verified** against real instances (for ServiceNow patterns)
- **Concise** -- these skills are loaded into LLM context windows, every token counts

## License

Apache 2.0. See [LICENSE](LICENSE).

Built by [Agent Blueprint](https://agentblueprint.ai) (NowGentic LLC).
