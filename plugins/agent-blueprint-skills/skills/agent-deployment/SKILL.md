---
name: agent-deployment
description: >-
  Vendor-agnostic AI agent deployment methodology. Phased rollout, pilot-first
  approach, test gates, access tier adaptation, and progress tracking. Works
  with any enterprise platform (ServiceNow, Salesforce, custom). Use when
  deploying AI agents, planning agent rollouts, or building implementation
  plans. Triggers: agent deployment, deploy agents, implementation plan,
  rollout strategy, pilot plan, agent testing, phased deployment.
version: 0.1.0
author: Agent Blueprint
license: Apache-2.0
---

# AI Agent Deployment Methodology

A vendor-agnostic methodology for deploying AI agents in enterprise environments.
Designed to work with any platform. Focused on reducing risk through phased
rollout, pilot-first validation, and structured test gates.

This skill defines the methodology. For platform-specific knowledge, load a
companion skill (e.g., `servicenow-ai-agents` for ServiceNow).

---

## Before You Start

1. **Read the blueprint or spec.** Understand the agent architecture: how many
   agents, what tools they need, how they interact, what business problem they
   solve.

2. **Check for a platform-specific skill.** If one exists, load it for
   platform-specific setup, data model, and configuration patterns.

3. **Check for prior work.** Look for deployment history, progress files, or
   implementation state from previous sessions. Don't repeat work.

---

## Access Tier Adaptation

Adapt your approach based on what access the user provides. Don't present
access tiers as a menu. Respond naturally to what the user gives you.

- **Full access (read + write):** Execute steps directly on the target platform.
  Create records, write scripts, configure integrations, test. Act first, report after.

- **Read-only access:** Connect to discover schemas and read existing config.
  For steps that create or modify records, generate all records and scripts as
  local files and walk the user through creating them. After the user acts,
  verify by reading back from the platform.

- **No access:** Work from the spec and skill knowledge only. Generate everything
  as local files with step-by-step instructions. Ask the user to share results
  (screenshots, IDs, error messages) after each action.

Shift fluidly within any access level. Even with full access, some tasks
require the user to act (UI-only config, credential vaults, approval workflows).

---

## Deployment Sequence

**Start with the pilot.** Deploy only Phase 1 agents first. Validate
end-to-end before expanding.

### Step 1: Read the implementation roadmap
The blueprint's implementation plan defines what gets built in each phase.
Identify the Phase 1 agents exactly as specified — typically one orchestrator
and one worker. Do not add agents from later phases. If the blueprint has no
phased roadmap, pick the single agent with the clearest business value and
fewest dependencies.

### Step 2: Set up platform access
Follow the platform skill's setup instructions if one is loaded. Otherwise,
configure access using your own knowledge of the target platform.

### Step 3: Discover the target environment
Explore schemas, existing config, and platform version before creating
anything. Never assume you know the schema. Query it.

### Step 4: Create Phase 1 records
Agents, teams, workflows, tools, and linking records. **Create only the
agents specified for Phase 1 in the implementation plan.** If you are about
to create more worker agents than the plan specifies for this phase, stop
and confirm with the user before proceeding.

Note: the blueprint may define a Manager/Orchestrator agent alongside
Workers. On most platforms, the Manager maps to the native orchestration
layer (workflow, pipeline, coordinator), not a separate agent record. Check
the platform skill for the exact entity mapping.

### Step 5: Organize records
Move records into the appropriate scope or project structure.

### Step 6: Verify deployment
Confirm all records exist and are correctly configured.

### Step 7: Implement tool scripts
Start with **simulation scripts** to validate the LLM orchestration (tool
chain, parameter passing, sequencing). This is step 1 of 2. See
[references/DEPLOYMENT_PATTERNS.md](references/DEPLOYMENT_PATTERNS.md)
for why simulation-first matters.

### Step 8: Configure security
Set up access controls appropriate for the platform. **VERIFY** security
is actually configured. Do not assume success. Query the platform to confirm.

### Step 9: TEST BEFORE EXPANDING (gate, not optional)
Invoke the pilot agent programmatically. Read the full execution trace.
Verify each tool produced output. If any tool returns null, the script
format is wrong. Fix before proceeding. Re-test until the full tool chain
completes. Do NOT proceed to step 10 until the pilot passes.

### Step 10: Make the pilot real
Replace simulation scripts with real implementations that connect to actual
data sources, APIs, and platform tables. See
[references/DEPLOYMENT_PATTERNS.md](references/DEPLOYMENT_PATTERNS.md)
for the progression pattern.

### Step 11: Expand to next phase
**Do not build Phase 2 agents before Phase 1 is validated with real data.**
After Phase 1 passes end-to-end with real integrations, proceed to Phase 2
agents as defined in the implementation plan. Each new agent follows the same
simulation-to-real progression (Steps 7-10).

---

## Maintaining Progress

Keep a progress file updated throughout the engagement. Update after each milestone:

- **Environment details**: platform, version, environment, project/scope ID
- **What's deployed**: agents, tools, workflows created (with IDs)
- **What's left**: remaining phases, pending work
- **Lessons learned**: platform-specific behavior, things that failed and why
- **User decisions**: choices that override blueprint defaults

---

## References

- [DEPLOYMENT_PATTERNS.md](references/DEPLOYMENT_PATTERNS.md) -- Why
  simulation-only pilots fail, the pilot progression pattern, and test
  strategy guidance.

---

*Built by [Agent Blueprint](https://agentblueprint.ai) -- AI advisory for enterprise agent deployment.*
