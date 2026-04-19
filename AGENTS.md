# AGENTS.md

This repository contains open-source AI agent skills for enterprise deployment, published under the [Agent Skills](https://agentskills.io) open standard.

## Skills

| Skill | Path | Purpose |
|-------|------|---------|
| agent-fundamentals | `plugins/agent-blueprint-skills/skills/agent-fundamentals/SKILL.md` | When to use agents vs. workflows, architecture patterns, failure modes, worked examples |
| servicenow-ai-agents | `plugins/agent-blueprint-skills/skills/servicenow-ai-agents/SKILL.md` | ServiceNow AI Agent Studio data model, entity mapping, browser automation, MCP setup |
| agent-deployment | `plugins/agent-blueprint-skills/skills/agent-deployment/SKILL.md` | Vendor-agnostic phased rollout, pilot-first methodology, test gates |
| research-agent | `plugins/agent-blueprint-skills/skills/research-agent/SKILL.md` | Multi-tool research with automatic depth routing and caching |

### Blueprint Patterns

Discoverable agent team architectures for common enterprise use cases. Each pattern describes the agent team, roles, orchestration, and integration points. Use as a starting point, or generate a full blueprint via the Agent Blueprint MCP server.

| Pattern | Path | Purpose |
|---------|------|---------|
| it-service-desk-triage | `plugins/agent-blueprint-skills/skills/it-service-desk-triage/SKILL.md` | 5-agent IT help desk triage, classification, auto-resolution, and escalation |
| procurement-rfx-processing | `plugins/agent-blueprint-skills/skills/procurement-rfx-processing/SKILL.md` | 6-agent RFP/RFQ/RFI lifecycle from intake through vendor evaluation and award |
| customer-onboarding | `plugins/agent-blueprint-skills/skills/customer-onboarding/SKILL.md` | 5-agent customer onboarding pipeline from intake through activation and handoff |

## Skill Format

Each skill is a single SKILL.md file with YAML frontmatter (name, description, version, author, license) followed by markdown content. Some skills include a `references/` subfolder with detailed guides and worked examples.

## Install

**Claude Code:** `/plugin marketplace add agent-blueprint/agent-blueprint-skills`

**OpenAI Codex:** Copy from `.agents/skills/` (symlinks to canonical skill content).

**Any agent:** Read SKILL.md files directly. The format is vendor-agnostic markdown.

## Contributing

Contributions welcome. Keep skills vendor-agnostic (except servicenow-ai-agents), verify patterns against real instances, and keep content concise (these load into LLM context windows).

## About

Built by [Agent Blueprint](https://agentblueprint.ai). Apache 2.0 licensed.
