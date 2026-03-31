---
name: agent-fundamentals
description: >-
  Agentic AI fundamentals for enterprise. What agents are, when to use them vs.
  workflows or chatbots, agent anatomy, architecture patterns, decision
  frameworks, and common failure modes. Use when someone is new to agents,
  evaluating whether agents fit their use case, or needs conceptual grounding
  before deploying. Triggers: what is an agent, agent vs workflow, should I use
  agents, how do agents work, new to agents, agent fundamentals, when to use
  agents, agent design patterns, explain agentic, agent architecture, learn
  about agents, agentic AI concepts.
version: 0.1.0
author: Agent Blueprint
license: Apache-2.0
---

# Agentic AI Fundamentals

A practical guide to understanding AI agents in enterprise environments.
What they actually are, when they make sense, and when they don't.

If you already know agents and want to deploy them, skip this and load
`agent-deployment` instead.

---

## What Is an Agent?

An AI agent is an LLM that can **decide what to do next** and **take actions**
through tools.

**Chatbot:** You ask a question, it answers. One turn, one response. No actions.

**Workflow:** Predefined steps that always run in the same order. If X then Y.
Deterministic and predictable.

**Agent:** You give it a goal, it figures out the steps. It calls tools, reads
results, decides what to do next, and keeps going until the task is done or it
needs a human.

The key difference: **does the task require judgment at runtime?**
- Steps are always the same → workflow
- Steps depend on what the LLM discovers along the way → agent

---

## When to Use Agents (and When Not To)

Most processes people want to "agentify" are better as workflows. Agents add
value when the task genuinely requires reasoning and dynamic decisions.

### Agent territory
- Interpreting unstructured input (emails, documents, tickets) and deciding
  what to do with it
- Steps aren't predictable upfront. The path depends on what the agent finds.
- Multiple tools need to be selected and combined based on context
- Synthesizing information from several sources into a judgment
- Human-quality reasoning at decision points

### Workflow territory
- Steps are always the same regardless of input
- Logic is "if X then Y" with no ambiguity
- Speed and reliability matter more than flexibility
- Guaranteed execution paths needed for compliance or audit
- Cost per execution needs to be minimal

### Chatbot territory
- User asks questions, gets answers
- No action to take, just information retrieval
- Conversational, not task-oriented

### Simple prompt territory
- One-shot tasks: summarize this, rewrite that, extract these fields
- No tools needed, no multi-step reasoning

### The over-agenting trap

The most common mistake. An agent that always follows the same three steps
in the same order is just an expensive, slow, unreliable workflow. If you
can draw the complete flowchart before runtime, you don't need an agent.

Signs you've over-agented:
- The agent always calls the same tools in the same sequence
- You're fighting non-determinism when you want predictable behavior
- Execution costs are high for what should be simple operations
- You keep adding instructions to force the agent down a specific path

---

## Anatomy of an Agent

Every agent has the same building blocks, regardless of platform.

### Instructions
The system prompt. Defines purpose, behavior, rules, and decision criteria.

Specific instructions produce reliable agents:
> "When you receive an RFP, extract vendor name, due date, estimated value,
> and category. If value exceeds $100K, route to senior review. Otherwise,
> create a standard evaluation record."

Vague instructions produce unpredictable agents:
> "Be helpful with procurement tasks."

### Tools
Actions the agent can take. Each tool has a name, description, and defined
inputs/outputs. Tools connect the agent to the world:
- **Read tools**: Query databases, search documents, call APIs
- **Write tools**: Create records, send messages, update systems
- **Compute tools**: Run calculations, transform data, generate files

An agent without tools is just a chatbot.

**Tool count matters.** LLMs degrade with more than 10-15 tools. They pick
wrong tools, hallucinate tool names, or call tools unnecessarily. If an agent
needs 20 tools, split it into two agents.

### Memory and Context
What the agent knows:
- **Session context**: Current conversation, uploaded documents, task parameters
- **Persistent context**: Previous interactions, user preferences, past decisions
- **Retrieved context**: Data fetched by tools during execution

### Guardrails
What the agent CANNOT do matters as much as what it should do:
- **Input guardrails**: Reject harmful or irrelevant inputs
- **Output guardrails**: Validate responses before delivering them
- **Action guardrails**: Limit which tools can be called when
- **Escalation rules**: When to stop and involve a human

An unguarded agent will eventually do something unexpected. Define boundaries.

### Orchestration
How the agent fits into a larger system. See Architecture Patterns below.

---

## Architecture Patterns

### Single Agent
One agent, one task, one set of tools.

**When:** The task is well-scoped and one "role" handles everything.
**Example:** A ticket triage agent reads incoming support tickets, classifies
severity, extracts key details, and routes to the right team.

Start here. Most successful enterprise agent deployments begin with a single
agent proving value before expanding.

### Orchestrator + Workers
A coordinator routes work to specialist agents based on the task.

**When:** Complex tasks require different expertise at different stages.
**Example:** An RFx procurement system where a coordinator routes to specialists
for document analysis, vendor evaluation, compliance checking, and award processing.

**Platform note:** On most enterprise platforms, the orchestrator maps to the
platform's native workflow or pipeline layer, not a separate agent record.
The "manager" is the workflow; the "workers" are the agents.

### Pipeline
Agents execute in sequence, each one's output feeding the next.

**When:** Multi-stage processing where each stage transforms the data.
**Example:** Document intake → extraction → validation → enrichment → filing.
Each stage has different tools and different expertise.

### Human-in-the-Loop
Agent works, then pauses at defined checkpoints for human review.

**When:** High-stakes decisions, regulated processes, or anything where errors
have significant consequences.
**Example:** Agent drafts a contract amendment, pauses for legal review before
sending. Human approves, rejects, or modifies.

Can be added to any other pattern. Not a pattern by itself.

### Choosing a Pattern

| Question | Pattern |
|----------|---------|
| Can one agent handle the task with 3-5 tools? | Single agent |
| Distinct phases requiring different expertise? | Pipeline |
| Dynamic routing between specialists at runtime? | Orchestrator + workers |
| High-stakes decision points? | Add human-in-the-loop to any pattern |

Start with the simplest pattern that works. Add complexity only when the
simpler version fails.

---

## Common Failure Modes

| Failure | What happens | Fix |
|---------|-------------|-----|
| **Over-agenting** | Building agents for tasks that should be workflows. High cost, low reliability, fighting non-determinism. | If you can draw the flowchart upfront, make it a workflow. |
| **Tool sprawl** | Too many tools (15+). Agent picks wrong tools, hallucinates tool names. | Split into multiple agents with focused tool sets. |
| **Vague instructions** | Inconsistent behavior. Agent interprets the same input differently each time. | Write specific decision criteria, output formats, escalation conditions. |
| **Missing guardrails** | Agent sends emails it shouldn't, updates wrong records, surfaces confidential data. | Define what the agent CANNOT do. Test edge cases. |
| **No validation** | Agent "works" in demos, fails on real data. Nobody reads execution traces. | Test programmatically. Read the full trace. Verify every tool output. |
| **No fallback** | Agent fails silently. Nobody knows until a customer complains. | Define failure paths: retry, escalate, or degrade gracefully. |
| **Skipping simulation** | Jump straight to real integrations. Integration failures cascade across all agents. | Start with simulation scripts, validate orchestration, then connect real data one tool at a time. |

---

## Evaluating a Use Case

When someone brings a potential agent use case, walk through these questions:

1. **What triggers this process?** What event or input starts it?
2. **What's the outcome?** What should exist when it completes?
3. **What steps happen in between?** Map them out.
4. **Which steps require judgment?** These are where agents add value.
5. **Which steps are always the same?** These should be workflows.
6. **What systems does it need to touch?** These become tools.
7. **What can go wrong?** Where are the failure points? What are the consequences?
8. **Who approves what?** Where are the human checkpoints?
9. **How will you measure success?** What proves this agent delivers value?

**If question 4 produces zero items, you don't need an agent.**

See [references/WORKED_EXAMPLES.md](references/WORKED_EXAMPLES.md) for
three detailed walkthroughs applying this framework to real use cases.

---

## Ready to Build?

Once you understand the concepts:

- **Deploy agents** → Load `agent-deployment` for a structured, vendor-agnostic
  methodology (pilot-first, test gates, phased rollout)
- **Deploy on ServiceNow** → Also load `servicenow-ai-agents` for platform-specific
  data model, entity mapping, and configuration patterns
- **Research first** → Load `research-agent` for multi-tool research methodology

The progression: **understand** (this skill) → **deploy** (agent-deployment) →
**platform** (servicenow-ai-agents or equivalent)

---

*Built by [Agent Blueprint](https://agentblueprint.ai) — AI advisory for enterprise agent deployment.*
