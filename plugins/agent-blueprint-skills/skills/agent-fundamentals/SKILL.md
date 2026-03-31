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
Grounded in published research from Anthropic, OpenAI, LangChain, McKinsey,
and MIT Sloan.

If you already know agents and want to deploy them, skip this and load
`agent-deployment` instead.

---

## What Is an Agent?

The clearest definition comes from Anthropic ("Building Effective Agents,"
Dec 2024):

- **Workflow**: LLMs and tools orchestrated through **predefined code paths**.
  The developer controls execution flow.
- **Agent**: LLMs **dynamically direct their own processes and tool usage**.
  The model controls execution flow.

The key distinction is **who decides what happens next**: the developer
(workflow) or the LLM (agent).

An agent is an LLM that can:
1. Receive a goal or task
2. Decide which tools to call and in what order
3. Read results and adjust its approach
4. Keep going until the task is done or it needs human input

This definition is consistent across Anthropic, OpenAI, Google, Microsoft,
and LangChain as of 2026.

**What an agent is NOT:**
- A chatbot (chatbots answer questions but don't take actions)
- A workflow with an LLM step (the LLM does work but doesn't control the flow)
- A simple prompt (one-shot tasks with no tool use or multi-step reasoning)

---

## When to Use Agents (and When Not To)

Anthropic's core principle: "Find the simplest solution possible, and only
increase complexity when needed."

This is backed by industry experience: most successful production deployments
use workflows or workflow-plus-LLM, not autonomous agents. Only a small
fraction of real production workloads need fully autonomous agents.

### Use an agent when:
- The task requires interpreting unstructured input and deciding what to do
- The sequence of steps isn't predictable upfront
- Multiple tools need to be selected and combined based on context
- The task involves complex decision-making with nuanced judgment (OpenAI)
- Rule sets have become too unwieldy to maintain deterministically (OpenAI)

### Use a workflow when:
- Steps are always the same regardless of input
- Logic is "if X then Y" with no ambiguity
- Speed, reliability, and cost matter more than flexibility
- You need guaranteed execution paths for compliance or audit
- You can draw the complete flowchart before runtime

### Use a workflow with an LLM step when:
- Most steps are deterministic but one or two require interpretation
- You want LLM reasoning at specific points but not dynamic control
- This covers ~80% of "agentic" use cases in practice

### The over-agenting trap

The most common failure in enterprise agent projects. An agent that always
follows the same steps in the same order is just an expensive, slow,
unreliable workflow.

Signs:
- The agent always calls the same tools in the same sequence
- You're fighting non-determinism when you want predictable behavior
- You keep adding instructions to force the agent down a specific path
- Execution costs are high for what should be simple operations

Anthropic warns: "Agentic systems often trade latency and cost for better
task performance, and you should consider when this tradeoff makes sense."

---

## Anatomy of an Agent

Every agent has the same building blocks, regardless of platform.

### Instructions
The system prompt. Defines purpose, behavior, rules, and decision criteria.
Anthropic's guidance: "Think of this as writing a great docstring for a
junior developer."

Specific instructions produce reliable agents:
> "When you receive an RFP, extract vendor name, due date, estimated value,
> and category. If value exceeds $100K, route to senior review. Otherwise,
> create a standard evaluation record."

Vague instructions produce inconsistent agents:
> "Be helpful with procurement tasks."

### Tools
Actions the agent can take. Each tool has a name, description, and defined
inputs/outputs. Tools connect the agent to systems:
- **Read tools**: Query databases, search documents, call APIs
- **Write tools**: Create records, send messages, update systems
- **Compute tools**: Run calculations, transform data, generate files

An agent without tools is just a chatbot.

**Tool count matters.** LLM performance degrades as tool count grows. Models
pick the wrong tool more often, hallucinate tool names, and spend more tokens
on selection logic. Anthropic's heuristic: use dynamic tool loading when you
have more than 10 tools or tool definitions consume more than 10K tokens. If
an agent needs many tools, split into multiple agents with focused tool sets.

Anthropic also recommends: "Poka-yoke your tools" -- design tool interfaces
so it's harder to make mistakes. Rich descriptions with examples improve
selection accuracy dramatically.

### Memory and Context
What the agent knows:
- **Session context**: Current conversation, documents, task parameters
- **Working memory**: Intermediate state passed between steps
- **Long-term memory**: External storage queried via tools (databases, vector stores)
- **Retrieved context**: Data fetched by tools during execution

### Guardrails
Constraints on what the agent can and cannot do. Every platform vendor
(Anthropic, OpenAI, Microsoft) emphasizes guardrails as architectural
requirements, not optional add-ons.

- **Input guardrails**: Validate inputs before they reach the agent's reasoning
- **Output guardrails**: Validate responses before delivering to users
- **Action guardrails**: Scope tool permissions to minimum necessary access
- **Escalation rules**: When to stop and involve a human
- **Iteration limits**: Maximum steps to prevent runaway agents and cost explosions

88% of organizations with agent deployments report confirmed or suspected
security incidents (Gravitee, 2026). Define what the agent CANNOT do.

### Orchestration
How the agent fits into a larger system. See Architecture Patterns below.

---

## Architecture Patterns

Anthropic documents a progression from simple to complex. Start at the top
and only move down when the simpler option fails.

### 1. Prompt Chaining
Sequential LLM calls with programmatic checks between steps. Each step
processes the output of the previous one. The developer controls the sequence.

**When:** Task decomposes into fixed subtasks with clear handoffs.
**Example:** Generate report outline → write each section → review and edit.
**Not an agent.** This is a workflow with LLM steps.

### 2. Routing
Classifies input and directs to specialized handlers. One LLM call decides
where the request goes, then a specific handler processes it.

**When:** Distinct categories that need different handling.
**Example:** Customer message → classify as billing/technical/sales → route
to specialized handler.

### 3. Orchestrator + Workers
A central LLM dynamically breaks down tasks and delegates to specialized
workers. The orchestrator decides what subtasks are needed at runtime.

**When:** Complex tasks where you can't predict the subtasks upfront.
**Example:** Coding agent that decides which files to read, what to modify,
and how to test based on a bug report.

**Platform note:** On most enterprise platforms, the orchestrator maps to the
platform's native workflow or pipeline layer, not a separate agent record.

### 4. Evaluator-Optimizer
Generator produces output, evaluator assesses it, loop continues until
quality threshold is met.

**When:** Clear evaluation criteria exist and iterative refinement adds value.
**Example:** Code generation where tests provide automatic evaluation.

### 5. Autonomous Agent
LLM using tools in a loop based on environmental feedback. The agent decides
every step dynamically.

**When:** Open-ended problems where you can't predict the number of steps.
**Most complex. Most expensive. Hardest to control.** Only use when simpler
patterns genuinely can't handle the task.

### Multi-Agent Patterns (LangChain, 2026)

When a single agent isn't enough:

- **Subagents**: Supervisor coordinates specialists as tools. Strong context
  isolation between agents.
- **Handoffs**: Active agent transfers control to another based on context.
  Each agent can invoke others via tool calling.
- **Pipeline**: Agents execute in sequence, each output feeding the next.
- **Human-in-the-loop**: Agent pauses at defined checkpoints for human review.
  Can be added to any pattern.

### Choosing a Pattern

| Question | Pattern |
|----------|---------|
| Can the task be decomposed into fixed steps? | Prompt chaining (workflow) |
| Does input need classification before handling? | Routing |
| Can one agent handle it with a few tools? | Single autonomous agent |
| Are subtasks unpredictable at design time? | Orchestrator + workers |
| Does output quality need iterative refinement? | Evaluator-optimizer |
| Are there high-stakes decision points? | Add human-in-the-loop |

Start with the simplest pattern. Most production systems are patterns 1-2,
not pattern 5 (LangChain, 2026).

---

## Common Failure Modes

40%+ of agentic AI projects will be abandoned by end of 2027 (Gartner, June
2025). 42% of companies abandoned most AI initiatives in 2025, up from 17%
in 2024 (S&P Global). Understanding why helps avoid the same mistakes.

| Failure | What happens | Evidence |
|---------|-------------|----------|
| **Over-agenting** | Using agents for tasks workflows handle better. High cost, low reliability. | Most successful deployments are workflows, not agents (Anthropic 2024, LangChain 2026) |
| **Tool sprawl** | Too many tools. LLM picks wrong ones, hallucinates tool names. | Anthropic recommends dynamic tool loading at >10 tools or >10K tokens of tool definitions. |
| **Vague instructions** | Inconsistent behavior. Same input produces different results. | Anthropic: instructions should read like "a great docstring for a junior developer" |
| **Missing guardrails** | Agent sends wrong emails, updates wrong records, surfaces confidential data. | 88% of orgs with agents report security incidents (Gravitee 2026) |
| **Skipping validation** | "Works in demos" but fails on real data. No execution trace review. | Only 52% run offline evaluations, 37% run online evaluations (LangChain 2026) |
| **Ignoring sociotechnical work** | Focus on model, ignore governance, data quality, org readiness. | 80% of implementation effort is sociotechnical, not model work (MIT Sloan) |
| **Cost explosions** | Agent loops trigger unbounded API costs. No iteration limits. | Agentic workflows have variable, unpredictable costs (MIT Sloan) |
| **No fallback** | Agent fails silently. Nobody knows until a customer complains. | Only 6% of companies fully trust agents for core processes (HBR/Workato 2026) |

---

## Evaluating a Use Case

When someone brings a potential agent use case, walk through these questions:

1. **What triggers this process?** What event or input starts it?
2. **What's the outcome?** What should exist when it completes?
3. **What steps happen in between?** Map them out.
4. **Can you draw the complete flowchart before runtime?** If yes, build a
   workflow. You don't need an agent. (This eliminates ~80% of candidates.)
5. **Which steps require interpreting ambiguous input or making judgment calls?**
   These are where agents add value.
6. **What systems does it need to touch?** These become tools. Count them.
   If >10, consider splitting into multiple agents.
7. **What can go wrong?** Where are the failure points and consequences?
8. **Who approves what?** Where are the human checkpoints?
9. **How will you measure success?** What proves this delivers value?

**If question 5 produces zero items, build a workflow.**

See [references/WORKED_EXAMPLES.md](references/WORKED_EXAMPLES.md) for
three detailed walkthroughs applying this framework.

---

## Key Design Principles

From Anthropic's "Building Effective Agents":

1. **Simplicity** -- "Maintain simplicity in your agent's design." Only add
   complexity when simpler approaches demonstrably fail.
2. **Transparency** -- "Explicitly show the agent's planning steps." Users
   and operators need to understand what the agent is doing and why.
3. **Tool documentation** -- "Carefully craft your agent-computer interface
   through thorough tool documentation and testing." Anthropic reports
   spending more time optimizing tools than overall prompts.

From OpenAI's "Practical Guide to Building Agents":

4. **Start from existing processes** -- Use operating procedures, support
   scripts, and policy documents as the basis for agent instructions.
5. **Clear failure thresholds** -- "When agents exceed specified retry limits
   or repeatedly fail to understand intent, control should escalate to human
   intervention."

---

## The Current Landscape (2026)

For context on where the industry stands:

- **57% of organizations** have agents in production (LangChain 2026)
- **Only 6%** fully trust agents for core business processes (HBR/Workato)
- **30% median productivity gain** in targeted applications (Goldman Sachs)
- **Quality is the #1 barrier** to moving agents to production (LangChain)
- **89% have observability** implemented; it's considered table stakes
- **Multi-model is the norm**: 75%+ of orgs use multiple LLM providers

The technology works. The challenge is governance, data quality, and
organizational readiness.

---

## Ready to Build?

Once you understand the concepts:

- **Deploy agents** → Load `agent-deployment` for structured, vendor-agnostic
  methodology (pilot-first, test gates, phased rollout)
- **Deploy on ServiceNow** → Also load `servicenow-ai-agents` for platform-specific
  data model, entity mapping, and configuration
- **Research first** → Load `research-agent` for multi-tool research methodology

The progression: **understand** (this skill) → **deploy** (agent-deployment) →
**platform** (servicenow-ai-agents or equivalent)

---

## Sources

- Anthropic, "Building Effective Agents" (Dec 2024)
- Anthropic, "Advanced Tool Use" (Nov 2025)
- OpenAI, "A Practical Guide to Building AI Agents" (2025)
- LangChain, "State of Agent Engineering" (2026)
- LangChain, "Choosing the Right Multi-Agent Architecture" (2026)
- McKinsey, "State of AI" (2025)
- MIT Sloan, "5 Heavy Lifts Deploying AI Agents" (2025-2026)
- Gartner, Agentic AI Predictions (June 2025)
- Gravitee/Beam.ai, "AI Agent Sprawl" (2026)
- HBR/Workato, "Enterprise AI Trust Gap" (2026)

---

*Built by [Agent Blueprint](https://agentblueprint.ai) -- AI advisory for enterprise agent deployment.*
