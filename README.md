# Agent Blueprint Skills

Open-source AI agent skills for enterprise deployment. Built for coding agents (Claude Code, Codex, Cursor, Copilot, Windsurf) using the [Agent Skills](https://agentskills.io) open standard.

**Three skills. Zero vendor lock-in. Works with any coding agent.**

| Skill | What it does |
|-------|-------------|
| [servicenow-ai-agents](skills/servicenow-ai-agents/SKILL.md) | ServiceNow AI Agent Studio reference: data model, entity mapping, tool types, prompting patterns, browser navigation, MCP setup |
| [agent-deployment](skills/agent-deployment/SKILL.md) | Vendor-agnostic deployment methodology: phased rollout, pilot-first, test gates, access tier adaptation |
| [research-agent](skills/research-agent/SKILL.md) | Multi-tool research with automatic depth routing: free tools first, paid escalation, caching, structured output |

## Install

### Claude Code (plugin)

```bash
/plugin add agent-blueprint/agent-blueprint-skills
```

Skills appear in your session automatically. Claude loads them when your task matches the skill description.

### OpenAI Codex CLI

Copy skills into your project:

```bash
git clone https://github.com/agent-blueprint/agent-blueprint-skills.git /tmp/ab-skills
cp -r /tmp/ab-skills/skills/* .agents/skills/
```

Or reference them directly from this repo.

### Cursor / Copilot / Windsurf / Any Agent

Clone this repo and point your agent to the SKILL.md files:

```bash
git clone https://github.com/agent-blueprint/agent-blueprint-skills.git .agent-skills
```

The SKILL.md format is an open standard. Any agent that reads markdown can use these skills.

### Manual (single skill)

Copy just the skill you need:

```bash
# ServiceNow AI Agents
curl -o SKILL.md https://raw.githubusercontent.com/agent-blueprint/agent-blueprint-skills/main/skills/servicenow-ai-agents/SKILL.md

# Deployment Methodology
curl -o SKILL.md https://raw.githubusercontent.com/agent-blueprint/agent-blueprint-skills/main/skills/agent-deployment/SKILL.md

# Research Agent
curl -o SKILL.md https://raw.githubusercontent.com/agent-blueprint/agent-blueprint-skills/main/skills/research-agent/SKILL.md
```

## What's in each skill

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
