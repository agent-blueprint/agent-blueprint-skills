# Deployment Patterns

## Why Simulation-Only Pilots Fail

A pilot with simulation scripts proves the agent can chain tools. A real pilot
proves the agent delivers business value. Only the second justifies expansion.

Enterprise consensus:
- Orgs that validate pilots with real data scale 2.5x faster than those using
  simulation-only validation (Microsoft Agent Readiness Framework).
- 46% of orgs cite integration as their #1 obstacle to scaling agents (Claude
  2026 State of AI Agents). Simulation hides integration problems until scale-up,
  when fixing them requires redesign across ALL agents simultaneously.
- Simulation pilots generate zero measurable business value. Leaders approve
  expansion based on "this agent reduced reconciliation time by 40%", not "the
  LLM chains tools correctly with fake data" (McKinsey agentic AI research).
- Forrester's dual-roadmap model: skills roadmap + foundations roadmap must
  advance in lockstep. A simulation pilot validates the skills roadmap but leaves
  the foundations roadmap (governance, observability, security, integrations)
  completely unvalidated.

## Pilot Progression Pattern

### Phase A: Simulation (validate orchestration)

Deploy the pilot agent with simulation scripts. These return realistic but
hardcoded data. The goal is to validate:
- The LLM correctly sequences tool calls
- Parameter passing works between tools
- The agent follows its instructions (gates, conditions, output format)
- Security controls are properly configured

Test programmatically. Read the full execution trace. Verify every tool
produced output (not null). If any tool returns null, the script format is
wrong. Fix before proceeding.

### Phase B: Real integrations (validate business value)

Once Phase A passes, replace simulation scripts one at a time with real
implementations that connect to actual data sources, APIs, and platform tables.

For each tool conversion:
1. Identify the real data source (database, API, file system)
2. Write the integration script
3. Test with 3-5 real cases
4. Compare output against known-good results
5. Move to the next tool

Do NOT convert all tools at once. Convert one, verify, then the next. This
isolates integration failures.

### Phase C: Expand

Only after the pilot passes end-to-end with real data:
1. Report metrics to stakeholders
2. Begin Phase 2 agents from the implementation roadmap
3. Each new agent follows the same Phase A to Phase B progression

## Test Strategy

### Programmatic Testing

Always test programmatically, not through UI. Programmatic tests are:
- Repeatable (same input, same test)
- Traceable (full execution trace with tool inputs/outputs)
- Automatable (can be scripted for regression)

### What to Verify in Execution Traces

1. **Tool outputs are not null** -- null means the script format is wrong
2. **Tool sequence matches expectations** -- the LLM should call tools in
   the order specified by the agent instructions
3. **Parameter values are correct** -- the LLM should pass the right data
   to each tool, not hallucinated values
4. **Final output matches expected recommendation** -- the agent's conclusion
   should be consistent with the data it received

### Testing Through UI vs API

Some platforms have a testing UI that behaves differently from the API.
Test through BOTH. Known differences include:
- UI may pass tool references instead of data between chained tools
- UI may require additional compilation/publishing steps
- API testing with admin credentials may bypass security controls

If API tests pass but UI tests fail, the issue is usually in how the platform
resolves inter-tool data passing, not in your tool scripts.
