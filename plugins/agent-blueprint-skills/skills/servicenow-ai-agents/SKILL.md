---
name: servicenow-ai-agents
description: >-
  ServiceNow AI Agent Studio reference for coding agents deploying AI agents
  on ServiceNow. Data model, entity mapping, URL patterns, tool types,
  prompting patterns, MCP setup, and browser navigation. Use when building,
  configuring, or debugging AI agents on ServiceNow (Zurich or later).
  Triggers: servicenow, AI Agent Studio, agent studio, sn_aia, agentic
  workflow, now assist agents, servicenow agent deployment.
version: 0.1.0
author: Agent Blueprint
license: Apache-2.0
---

# ServiceNow AI Agent Studio Reference

A community reference for coding agents deploying AI agents on ServiceNow's
AI Agent Studio. Covers the data model, entity relationships, tool types,
prompting patterns, and browser navigation.

This skill provides the platform knowledge. For deployment methodology
(phased rollout, test gates, pilot-first approach), load the companion
`agent-deployment` skill.

For expert-level deployment support with battle-tested patterns, version-specific
configuration, and security hardening, see [Agent Blueprint](https://agentblueprint.ai).
Agent Blueprint customers with access to `servicenow-expert` should prefer that
skill for production deployment, debugging, and security guidance. This
community skill remains a standalone starter reference.

---

## Search First, Always

ServiceNow is a massive platform that changes every release. Do NOT rely on
training data for ServiceNow-specific behavior. Search the official docs and
community proactively, not just when stuck.

**Before creating records:** search for current field requirements.
**Before configuring tools:** search for tool type behavior on the target version.
**Before telling the user to do anything manually:** search for a programmatic approach.
**When something fails:** search the community forums for the error message.

### Resources

- **ServiceNow Docs** (primary): https://www.servicenow.com/docs/
- **ServiceNow Community** (real-world solutions): https://www.servicenow.com/community/
- **AI Agent Studio docs** (Zurich): https://www.servicenow.com/docs/bundle/zurich-intelligent-experiences/page/administer/now-assist-ai-agents/concept/ai-agent-studio.html
- **AI Agents Prompting Guide**: https://www.servicenow.com/community/s/cgfwn76974/attachments/cgfwn76974/now-assist-articles/487/2/AI%20Agents%20Prompting%20Guide.pdf

---

## First Steps

When you get instance access, do ALL of these before creating any records:

1. **Confirm the instance version.** Query `sys_properties` for `glide.buildtag`:
   ```bash
   curl -s -u user:pass https://INSTANCE.service-now.com/api/now/table/sys_properties?sysparm_query=name=glide.buildtag&sysparm_fields=value
   ```
   Confirm with the user: "Connected to **[instance]** running **[version]**."

2. **Verify your access works.** Try a simple table query to confirm credentials
   and permissions. If using MCP tools, test `discover_table_schema` on a table.

3. **Extract schemas for the tables you'll work with.** Query `sys_dictionary`
   or use `discover_table_schema` on `sn_aia_usecase`, `sn_aia_agent`,
   `sn_aia_tool`, and other AI Agent Studio tables. Do not guess field names.

4. **Research the instance version.** Search the web for version-specific docs,
   best practices, and known limitations. Tell the user what you found.

---

## AI Agent Studio Data Model

### Core Tables

| Table | Entity | Purpose |
|-------|--------|---------|
| `sn_aia_usecase` | Agentic Workflow | Orchestrates worker agents |
| `sn_aia_agent` | AI Agent | Worker with tools and instructions |
| `sn_aia_team` | Team | Groups agents for a workflow |
| `sn_aia_team_member` | Team Member | Links agent to team (junction) |
| `sn_aia_tool` | Tool | Action available to agents |
| `sn_aia_agent_tool_m2m` | Agent-Tool Link | Links tools to agents (junction) |
| `sn_aia_trigger_configuration` | Trigger | Record-based triggers for workflows |

### Additional Tables (version-dependent)

| Table | Entity | Notes |
|-------|--------|-------|
| `sn_aia_agent_config` | Agent Config | Extended config (proficiency, active) |
| `sn_aia_version` | Version | Version management (Australia+) |
| `sn_aia_execution_plan` | Execution Plan | Runtime execution tracking |
| `sn_aia_execution_task` | Execution Task | Individual tool calls and reasoning steps |
| `sn_aia_strategy` | Strategy | Orchestration strategy configuration |

### Relationship Diagram

```
sn_aia_usecase (workflow)
  |-- .team --> sn_aia_team
  |               |-- sn_aia_team_member (junction)
  |                     |-- .agent --> sn_aia_agent
  |                           |-- sn_aia_agent_tool_m2m (junction)
  |                                 |-- .tool --> sn_aia_tool
  |-- sn_aia_trigger_configuration.usecase --> (triggers)
  |-- sn_aia_version.target_id --> (versions, Australia+)
```

### Key Fields Per Table

- **`sn_aia_usecase`**: name, description, **base_plan** (NOT instructions), team, execution_mode, strategy, record_type
- **`sn_aia_agent`**: name, description, role, **instructions**, **proficiency**, compiled_handbook, agent_type, strategy
- **`sn_aia_tool`**: name, description, tool_type, script, input_schema, output_schema, execution_mode
- **`sn_aia_trigger_configuration`**: usecase, target_table, condition, channel, active, run_as

### Critical Field Disambiguation

| If you want... | Use this field | On this table | NOT this field |
|---|---|---|---|
| Workflow orchestration instructions | `base_plan` | `sn_aia_usecase` | ~~instructions~~ (doesn't exist on this table) |
| Agent step-by-step logic | `instructions` | `sn_aia_agent` | |
| Agent persona/context | `role` | `sn_aia_agent` | |
| Agent proficiency description | `proficiency` | `sn_aia_agent` | |

Always verify field names against the live instance. UI labels don't always
match database column names:
- "list of steps" (UI) = `instructions` (DB field on `sn_aia_agent`)
- "Base plan" (UI) = `base_plan` (DB field on `sn_aia_usecase`)

---

## Entity Mapping (Blueprint to ServiceNow)

When translating an AI agent blueprint to ServiceNow:

| Blueprint Role | ServiceNow Entity | Table |
|---------------|-------------------|-------|
| Manager / Orchestrator | Agentic Workflow | `sn_aia_usecase` |
| Worker | AI Agent | `sn_aia_agent` |

**The Manager agent IS the agentic workflow.** Do not create a separate
`sn_aia_agent` record for the orchestrator. Its routing logic, escalation
rules, and orchestration instructions go into the workflow's `base_plan` field.
ServiceNow's ReAct engine handles agent selection, sequencing, and handoff natively.

If the blueprint defines N agents (e.g., "5 agents: 1 manager + 4 workers"),
create N-1 `sn_aia_agent` records. The manager becomes the workflow record.

---

## Tool Types

| Type | Use Case |
|------|----------|
| Record Operation | CRUD on ServiceNow records (built-in) |
| Script | Custom server-side logic |
| Flow/Subflow | Multi-step automation via Flow Designer |
| Search Retrieval | AI Search / RAG against knowledge bases |
| Web Search | External internet search |
| MCP Tool | External tools via MCP servers |
| Catalog Item | Present service catalog items to users |

### Script Tool Format

Script tools use a self-executing function pattern:

```javascript
(function(inputs) {
  // Your logic here
  return JSON.stringify(result);
})(inputs);
```

Use `return`, not `gs.info()`. The return value is what the agent receives.

---

## Agent Prompting Patterns

Use Markdown formatting. Write in second person imperative ("Analyze this...").
Be specific with tool names and conditions. Define gates ("Do NOT proceed until...").

```markdown
# Objectives
"Your objective is to..."

# Validations (before execution)
"First check if..."

# Steps
"1. Use the [Tool Name] tool to..."

# Expected Output
"Present a resolution plan with..."

# Constraints
Do's and don'ts.
```

**Name input parameters explicitly in instructions.** The ReAct engine is
dramatically more reliable when instructions reference specific parameter names
rather than vague descriptions like "query the relevant tables."

---

## MCP Server Setup

### Recommended: servicenow-mcp-server

The most widely used community MCP server for ServiceNow:
- **npm**: `servicenow-mcp-server`
- **GitHub**: https://github.com/michaelbuckner/servicenow-mcp

Provides: table queries, schema discovery, background script execution,
incident management, knowledge base access.

### Alternative: nowsdk-ext-mcp

More tools including flow development and diagnostics:
- **GitHub**: https://github.com/sonisoft-cnanda/nowsdk-ext-mcp
- Requires Node 22+ and `@servicenow/sdk`

### No MCP? Use REST API

Most ServiceNow work can be done via REST API:

```bash
# Query a table
curl -s -u user:pass "https://INSTANCE.service-now.com/api/now/table/sn_aia_agent?sysparm_query=sys_scope=APP_SCOPE_ID&sysparm_fields=name,sys_id"

# Create a record
curl -s -u user:pass -X POST -H "Content-Type: application/json" \
  "https://INSTANCE.service-now.com/api/now/table/sn_aia_agent" \
  -d '{"name":"My Agent","description":"Agent description"}'
```

**Auth note:** Basic auth on every request can trigger CSRF failures on some
tables. For multi-request workflows, capture session cookies from the first
authenticated request and reuse them.

---

## Scoped App Setup

Create scoped apps on `sys_app` (the Application table). This gives you a
proper application scope that contains all your AI Agent Studio records.

```bash
curl -s -u user:pass -X POST -H "Content-Type: application/json" \
  "https://INSTANCE.service-now.com/api/now/table/sys_app" \
  -d '{"name":"My AI Agents","scope":"x_myco_ai_agents","version":"1.0.0"}'
```

After creating, switch to the new scope before creating agent records. Records
created in the wrong scope require manual moves.

---

## Browser Navigation (Playwright)

### AI Agent Studio URLs

| Page | URL Pattern |
|------|------------|
| Overview | `/now/agent-studio/overview` |
| Create & Manage | `/now/agent-studio/create-manage/` |
| Use Cases tab | `/now/agent-studio/create-manage/params/selected-tab/use_cases` |
| Playground | `/now/agent-studio/playground` |
| Test Execution | `/now/agent-studio/playground/params/execution-plan/{sys_id}` |
| Settings | `/now/agent-studio/settings` |
| Use Case Setup | `/now/agent-studio/usecase-guided-setup/{sys_id}/params/step/details` |

### Classic UI (Most Reliable Fallback)

| Page | URL Pattern |
|------|------------|
| List view | `/{table}_list.do` (e.g., `/sn_aia_agent_list.do`) |
| Record view | `/{table}.do?sys_id={sys_id}` |
| Background Scripts | `/sys.scripts.do` |
| System Properties | `/sys_properties_list.do` |

**When workspace URLs fail, fall back to classic UI.** The `*_list.do` and
`*.do` patterns are the most predictable across all ServiceNow versions.

### ServiceNow Studio (New)

| Page | URL Pattern |
|------|------------|
| Home | `/now/servicenow-studio/home` |
| App Builder | `/now/servicenow-studio/builder?table=sys_app&sysId={sys_id}` |

Do NOT use `$studio.do` (legacy Studio). It shows a limited file tree and
misses AI Agent artifacts.

### Timing

| Scenario | Wait Time |
|----------|-----------|
| After login | 3-5 sec |
| Workspace page load | 5-6 sec |
| Classic UI page load | 2-3 sec |
| After form submission | 2-3 sec |
| Agent test execution | 15+ sec |

Use time-based waits, not text-based waits. ServiceNow's dynamic content
makes text-based waits unreliable.

### Common Selectors

| Element | Selector |
|---------|----------|
| All menu | `menuitem "All"` |
| Filter textbox | `textbox "Enter search term to filter All menu"` |
| Playground: workflow radio | `radio "Agentic workflow"` |
| Playground: select workflow | `combobox "Select one"` |
| Playground: task input | `textbox "Task"` |
| Playground: start | `button "Start test"` |

### Navigation Tips

- **Iframes:** Classic UI inside Next Experience uses iframes. Element refs may have `f2e` prefix.
- **Session timeout:** Redirects to `/navpage.do`. Re-run login flow.
- **Read-only records:** Records from other app scopes show "Read-only" banners. Switch scope to edit.
- **All Menu search:** Click `menuitem "All"`, type in the filter textbox, click matching result.

---

## Testing Workflows in Playground

1. Navigate to `/now/agent-studio/playground`
2. Select `radio "Agentic workflow"`
3. Pick the workflow from `combobox "Select one"`
4. Enter test task in `textbox "Task"`
5. Click `button "Start test"`
6. Wait 15+ seconds for execution
7. Read decision logs from the execution trace
8. If agents request input, respond in the chat input

### Programmatic Testing

For repeatable, traceable testing, invoke agents via API rather than UI.
See the ServiceNow docs for the Execution Plan API.

---

## Deployment Sequence

For a structured deployment approach with phased rollout, test gates, and
pilot-first methodology, load the `agent-deployment` skill from this plugin.

The recommended sequence:
1. Read the blueprint or requirements
2. Set up platform access (MCP or REST)
3. Discover the target environment (schemas, version, existing config)
4. Create Phase 1 records (agents, teams, workflows, tools)
5. Configure security
6. Test with simulation scripts first
7. Replace with real integrations
8. Validate end-to-end before expanding

---

## Contributing

Found a missing URL pattern, selector, or useful tip? Contributions welcome
at https://github.com/agent-blueprint/agent-blueprint-skills.

---

*Built by [Agent Blueprint](https://agentblueprint.ai) -- AI advisory for enterprise agent deployment.*
