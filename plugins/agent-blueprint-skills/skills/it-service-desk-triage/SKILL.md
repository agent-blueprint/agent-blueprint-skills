---
name: it-service-desk-triage
description: >-
  Blueprint pattern for an AI agent team that triages, classifies, and resolves
  IT service desk tickets. 5-agent architecture with routing orchestration.
  Use when a user asks you to build IT help desk automation, ticket triage,
  incident classification, service desk agents, or ITSM AI. Triggers: IT help
  desk, ticket triage, service desk automation, incident classification, ITSM
  agents, auto-resolve tickets, L1 support automation, help desk AI, incident
  routing, service request automation, ticket categorization.
version: 0.1.0
author: Agent Blueprint
license: Apache-2.0
---

# IT Service Desk Triage -- Agent Team Blueprint Pattern

An architecture pattern for automating IT service desk operations with a
5-agent team. Covers ticket intake through resolution or escalation.

This is a **pattern**, not a full implementation plan. It gives you the agent
team structure, roles, orchestration, and integration points. For a complete
blueprint with business case, financial projections, phased implementation
plan, and deployment guides, generate one via the Agent Blueprint MCP server
(`agentblueprint` npm package) or at [agentblueprint.ai](https://agentblueprint.ai).

---

## The Problem

IT service desks handle high volumes of repetitive requests. L1 analysts spend
most of their time on password resets, access requests, and known-issue
lookups. Classification is inconsistent. Routing errors add days to resolution.
Knowledge articles exist but aren't surfaced at the right time.

## Agent Team (5 Agents)

### 1. Triage Agent (Entry Point)

**Role:** First responder. Receives every incoming ticket, extracts structured
data, and decides the routing path.

**Inputs:** Ticket description (free text), submitter info, attachments

**Outputs:** Structured ticket with:
- Category (hardware, software, access, network, other)
- Priority (P1-P4) based on impact and urgency
- Affected service/CI (matched against CMDB)
- Initial sentiment assessment

**Decision logic:**
- If P1/P2 with service outage indicators → Escalation Agent immediately
- If matches known auto-resolvable pattern → Resolution Agent
- If category is ambiguous → Classification Agent for deeper analysis
- All others → Classification Agent

**Tools needed:** Ticket read, CMDB lookup, user profile lookup

### 2. Classification Agent

**Role:** Deep categorization when the Triage Agent needs more context. Analyzes
ticket content, user history, and related incidents to produce a precise
classification and assignment group recommendation.

**Inputs:** Structured ticket from Triage Agent, user's ticket history

**Outputs:**
- Refined category and subcategory
- Assignment group recommendation
- Related active incidents (potential duplicates or parent incidents)
- Suggested knowledge articles

**Decision logic:**
- If duplicate of active major incident → link and notify submitter
- If knowledge article matches with >80% confidence → Resolution Agent
- If requires human judgment (approval, physical action) → route to assignment group
- Otherwise → Resolution Agent with recommended approach

**Tools needed:** Ticket search (history), incident correlation, knowledge base
search, assignment group lookup

### 3. Knowledge Search Agent

**Role:** Specialist in finding and ranking relevant knowledge articles,
previous resolutions, and runbook steps. Called by other agents when they
need documented solutions.

**Inputs:** Ticket category, symptoms, affected CI/service

**Outputs:**
- Ranked list of knowledge articles with relevance scores
- Extracted resolution steps from best match
- Confidence level (high/medium/low)
- Flag if knowledge is stale (last updated >6 months ago)

**Tools needed:** Knowledge base search (semantic + keyword), article metadata
read, resolution history search

### 4. Resolution Agent

**Role:** Attempts automated resolution for known patterns. Executes runbook
steps or provides guided resolution to the end user.

**Inputs:** Structured ticket, recommended resolution approach, knowledge
article steps

**Outputs:**
- Resolution attempt result (success/partial/failed)
- Actions taken (audit trail)
- User communication (resolution summary or next steps)

**Auto-resolvable patterns (examples):**
- Password reset → trigger reset workflow, send instructions
- Software access request → check entitlements, submit access request
- VPN connectivity → guided troubleshooting, reset VPN profile
- Known error with documented workaround → provide workaround steps

**Decision logic:**
- If resolution succeeds → close ticket, notify submitter
- If partial resolution → update ticket with progress, escalate remainder
- If resolution fails → Escalation Agent with full attempt log

**Tools needed:** Ticket update, workflow trigger, notification send, access
management API, user communication

### 5. Escalation Agent

**Role:** Handles tickets that cannot be auto-resolved. Packages context for
human analysts, routes to the correct team, and monitors SLA compliance.

**Inputs:** Ticket with full history (triage data, classification, resolution
attempts)

**Outputs:**
- Escalation package (structured summary for human analyst)
- Assignment to correct group with priority context
- SLA countdown notification if approaching breach

**Decision logic:**
- If P1 → immediate page to on-call, create bridge/war room
- If SLA approaching breach → notify team lead
- If multiple escalations for same CI → flag potential problem record

**Tools needed:** Ticket update, assignment, notification/paging, SLA check,
problem record creation

---

## Orchestration Pattern

**Pattern:** Routing (Triage → specialist agents)

```
Ticket arrives
    │
    ▼
┌──────────┐
│  Triage  │──── P1/P2 outage ──────────────┐
│  Agent   │                                 │
└────┬─────┘                                 │
     │                                       │
     ▼                                       ▼
┌──────────────┐                    ┌─────────────┐
│Classification│                    │ Escalation  │
│    Agent     │                    │   Agent     │
└──────┬───────┘                    └─────────────┘
       │                                   ▲
       ▼                                   │
┌──────────────┐     failed           │
│  Resolution  │─────────────────────────┘
│    Agent     │
└──────┬───────┘
       │ success
       ▼
   Ticket closed
```

The Knowledge Search Agent is a **utility agent** called by Classification
and Resolution agents as needed, not a step in the main flow.

**Human-in-the-loop checkpoints:**
- Escalation Agent always routes to humans (by design)
- Resolution Agent actions on sensitive systems require approval
- P1 incidents always involve human oversight

---

## Integration Points

These are the external systems the agent team connects to. Platform-agnostic --
map to your ITSM platform's equivalents.

| Integration | Purpose | Agent(s) |
|-------------|---------|----------|
| **Ticketing system** | Read/write tickets, update status, add comments | All |
| **CMDB/Asset database** | Look up affected CIs, services, relationships | Triage, Classification |
| **Knowledge base** | Search articles, retrieve resolution steps | Knowledge Search, Resolution |
| **Identity/access management** | Password resets, access provisioning | Resolution |
| **Notification system** | Email, chat, paging for escalations | Resolution, Escalation |
| **Workflow engine** | Trigger automated runbooks and approval flows | Resolution |
| **Incident correlation** | Find related/duplicate incidents | Classification |
| **SLA tracking** | Monitor response and resolution SLA compliance | Escalation |

---

## Key Metrics

| Metric | What it measures |
|--------|-----------------|
| Auto-resolution rate | % of tickets resolved without human intervention |
| Mean time to triage | Time from ticket creation to structured classification |
| Routing accuracy | % of tickets assigned to correct group on first attempt |
| First-contact resolution | % resolved without reassignment or escalation |
| SLA compliance | % of tickets meeting response and resolution targets |
| Deflection rate | % of tickets resolved via knowledge article before escalation |

---

## Common Pitfalls

- **Over-automating P1s.** Critical incidents need human judgment. The agent
  team packages context and pages humans -- it doesn't resolve P1s autonomously.
- **Stale knowledge base.** Auto-resolution quality depends entirely on KB
  freshness. Build a feedback loop: failed resolutions flag articles for review.
- **Too many categories.** Start with 5-8 top-level categories. Deep taxonomies
  cause classification errors and don't improve routing.
- **Ignoring the human handoff experience.** When the Escalation Agent hands
  to a human, the analyst should receive a structured summary, not a raw
  ticket dump. This is where most implementations lose value.

---

## Getting a Full Blueprint

This pattern gives you the architecture. A full Agent Blueprint adds:

- **Business case** with ROI projections, cost-benefit analysis, and payback
  period tailored to your org size and ticket volume
- **Implementation plan** with phased rollout, pilot scope, test gates, and
  go-live criteria
- **Deployment guide** as an Agent Skill directory (12-14 files) that your
  coding agent can follow step-by-step
- **Platform-specific expert skills** for your ITSM platform

Generate a full blueprint: install the `agentblueprint` MCP server
(`npx agentblueprint`) or visit [agentblueprint.ai](https://agentblueprint.ai).

---

*Built by [Agent Blueprint](https://agentblueprint.ai) -- AI advisory for
enterprise agent deployment.*
