# Worked Examples

Three use cases evaluated through the framework from the main skill.
Each shows the thinking process, not just the conclusion.

---

## Example 1: IT Support Ticket Triage (Single Agent)

**Scenario:** A company gets 200+ IT support tickets per day. A Level 1 team
reads each ticket, classifies it (hardware, software, network, access),
assigns priority (P1-P4), and routes to the right team. 5-10 minutes per
ticket. They want to automate this.

### Walking through the framework

1. **Trigger:** New ticket submitted via portal or email.
2. **Outcome:** Ticket classified, prioritized, and assigned to correct team.
3. **Steps:** Read ticket → classify category → assess priority → check for
   duplicates → route to team → notify submitter.
4. **Can you draw the complete flowchart?** Partially. Routing and notification
   are deterministic. But classification of ambiguous tickets ("my computer is
   slow" could be hardware, software, or network) requires interpretation.
5. **Which steps require judgment?** Classification when description is
   ambiguous. Priority assessment when severity isn't explicit ("this is
   urgent" vs. actual business impact). Duplicate detection requires
   semantic similarity, not keyword matching.
6. **Systems:** Ticketing platform (read/update tickets), knowledge base
   (check known issues), notification system (email/Slack). 3-4 tools.
7. **What can go wrong?** Misclassification delays resolution. Wrong priority
   means P1 incidents sit unattended. Tolerable with human review for P1.
8. **Human checkpoints:** P1 tickets should notify a human immediately.
9. **Success metrics:** Triage time, classification accuracy vs. human
   baseline, P1 response time.

### Verdict: Single agent with human-in-the-loop for P1

Classification and priority assessment require interpreting unstructured
text. A rules-based workflow would need hundreds of keyword mappings and
still miss edge cases. But routing and notification are deterministic.

**Pattern:** Single agent for classification/prioritization (Anthropic pattern
5: autonomous agent). Workflow handles routing and notification after the
agent classifies. Human-in-the-loop for P1.

**Tools:** Read ticket, search knowledge base, update ticket fields, send
notification (4 tools -- well within recommended range).

**Guardrails:** Cannot close tickets. Cannot change SLA. Must escalate if
confidence is low. Maximum 3 retry attempts before routing to human queue.

This is a good **first agent project** because: well-scoped, measurable,
low-risk (misclassification is annoying, not catastrophic), and high volume
makes the ROI clear.

---

## Example 2: Monthly Invoice Processing (Workflow, Not Agent)

**Scenario:** A company processes 50 vendor invoices per month. Each arrives
as a PDF. Process: extract fields (vendor, amount, date, PO number), match
to purchase order, flag discrepancies over 5%, route for approval based on
amount ($5K manager, $25K director, $50K VP).

### Walking through the framework

1. **Trigger:** Invoice PDF received via email or upload.
2. **Outcome:** Invoice matched to PO, discrepancies flagged, approval routed.
3. **Steps:** Extract fields from PDF → match to PO → compare amounts → flag
   discrepancies → route for approval.
4. **Can you draw the complete flowchart?** Yes. Extract, match, compare,
   route. The path never varies. Amount thresholds are fixed rules.
5. **Which steps require judgment?** At first glance, PDF field extraction
   seems like it needs judgment. But document extraction tools handle standard
   invoices deterministically. PO matching is a lookup. Routing is pure rules.
6. **Systems:** Document extraction, ERP/procurement system, approval workflow.
7. **What can go wrong?** OCR misreads a field. PO number not found. These
   are exception paths, not judgment calls.
8. **Human checkpoints:** Approval is already human. OCR failures and no-match
   exceptions route to AP team.
9. **Success metrics:** Processing time, exception rate, accuracy vs. manual.

### Verdict: Workflow

Every step follows a deterministic path. This is Anthropic's "prompt
chaining" pattern at most -- sequential steps with programmatic checks.
An agent here would be slower, more expensive, and less reliable.

Remember: 80% of successful production deployments are workflows or
workflow-plus-LLM, not autonomous agents (LangChain 2026). This is firmly
in that 80%.

**Exception:** If invoices arrive in wildly different formats (handwritten,
multi-language, non-standard layouts), the extraction step alone might
benefit from an LLM. But make it a workflow with one LLM step, not an agent
controlling the whole process.

---

## Example 3: RFx Procurement Evaluation (Multi-Agent)

**Scenario:** A company issues RFPs for technology purchases. When vendor
responses arrive, they need evaluation against requirements, scoring,
comparison, and a recommendation. Currently takes 2-3 weeks per cycle with
5+ vendors.

### Walking through the framework

1. **Trigger:** Vendor response documents received for an active RFP.
2. **Outcome:** Scored evaluation matrix, comparative analysis, recommendation,
   draft award letter.
3. **Steps:**
   - Parse vendor response documents (varying formats and structures)
   - Extract responses mapped to each RFP requirement
   - Score each response against evaluation criteria
   - Check compliance (mandatory requirements, certifications)
   - Compare vendors across all dimensions
   - Produce recommendation with evidence
   - Generate draft award letter
4. **Can you draw the complete flowchart?** The sequence is predictable
   (always parse → extract → score → compare → recommend). But within each
   step, the reasoning is dynamic. Vendors bury answers in different
   sections, use different terminology for the same concepts.
5. **Which steps require judgment?** Almost all of them. Parsing responses
   requires interpreting document structure. Scoring requires weighing
   nuanced vendor language. Compliance checking requires recognizing
   equivalent certifications (SOC 2 Type II vs. SSAE 18). Recommendation
   requires synthesizing evidence into a defensible choice.
6. **Systems:** Document storage (read responses), procurement system (RFP
   requirements, criteria), communication (notifications, award letters).
   Multiple tools per agent.
7. **What can go wrong?** Misinterpreting a vendor's response leads to unfair
   scoring. Missing a compliance gap means selecting a non-compliant vendor.
   Financial and legal consequences.
8. **Human checkpoints:** Recommendation reviewed by procurement lead.
   Compliance findings reviewed by legal. Award letter approved before sending.
9. **Success metrics:** Cycle time, scoring consistency, stakeholder
   satisfaction with recommendation quality.

### Verdict: Orchestrator + workers with human-in-the-loop

Multiple areas of expertise needed. Too complex for a single agent (would
require too many tools -- research shows performance degrades beyond 10-15
tools per agent).

**Pattern:** Anthropic's "orchestrator-workers" pattern. A workflow coordinates
the sequence; specialist agents handle the judgment-heavy steps.

**Agents:**
- **Document Analysis Agent**: Parses responses, extracts answers mapped to
  requirements (tools: read documents, search within documents -- 2-3 tools)
- **Compliance Agent**: Checks mandatory requirements, certifications
  (tools: read compliance requirements, verify certifications -- 2-3 tools)
- **Evaluation Agent**: Scores responses, produces comparative matrix
  (tools: read extracted answers, read scoring criteria, write scores -- 3 tools)
- **Recommendation Agent**: Synthesizes into recommendation
  (tools: read evaluation data, generate report -- 2 tools)

**Orchestration is a workflow, not an agent.** The coordination sequence is
always the same: documents → analysis → compliance + evaluation (parallel) →
human gate → recommendation → human approval → award letter. There's no
dynamic decision about what to do next. The intelligence lives inside each
specialist agent, not in the coordination layer.

**Guardrails:** No agent can send external communications. Scores must include
justification text. Compliance findings require evidence references. Final
award requires human approval. Each agent has iteration limits.

**Why not start simpler?** You could start with a single agent handling just
the document analysis step. Prove that works, then add the evaluation agent.
This follows the pilot-first methodology in the `agent-deployment` skill.

---

## Key Takeaways

1. **Not every step needs the same solution.** Some steps are agents, some are
   workflows, some are API calls. Mix and match. Most production systems are
   hybrids (LangChain 2026).

2. **The judgment test decides.** Walk through each step and ask "does this
   require interpreting something ambiguous?" If yes, agent. If no, workflow.
   This is Anthropic's "who controls execution flow" test applied at the
   step level.

3. **Start with the simplest pattern.** Example 1 is a single agent. Example
   3 needs four. But even Example 3 starts with a pilot (one agent, prove
   value, then expand). Anthropic: "Only increase complexity when needed."

4. **Orchestration is usually a workflow.** The coordination layer that decides
   "do A then B then C" is almost always deterministic. The intelligence
   lives in the individual agents.

5. **Guardrails scale with consequences.** Ticket triage (low stakes) gets
   minimal guardrails. Procurement evaluation (legal/financial risk) gets
   multiple human gates, iteration limits, and communication restrictions.
