# Worked Examples

Three use cases evaluated through the framework from the main skill.
Each shows the thinking process, not just the conclusion.

---

## Example 1: IT Support Ticket Triage (Simple)

**Scenario:** A company gets 200+ IT support tickets per day. Currently, a
Level 1 team reads each ticket, classifies it (hardware, software, network,
access), assigns priority (P1-P4), and routes to the right team. Takes 5-10
minutes per ticket. They want to automate this.

### Walking through the framework

1. **Trigger:** New ticket submitted via portal or email
2. **Outcome:** Ticket classified, prioritized, and assigned to the correct team
3. **Steps:** Read ticket → classify category → assess priority → check for
   duplicates → route to team → notify submitter
4. **Which steps require judgment?**
   - Classifying the category when the description is ambiguous ("my computer
     is slow" could be hardware, software, or network)
   - Assessing priority when severity isn't explicit ("this is urgent" vs.
     actual business impact)
   - Duplicate detection requires understanding semantic similarity, not just
     keyword matching
5. **Which steps are always the same?** Routing to the assigned team,
   sending the notification. These are workflow.
6. **Systems:** Ticketing platform (read tickets, update fields, assign),
   knowledge base (check for known issues), notification system (email/Slack)
7. **What can go wrong?** Misclassification delays resolution. Wrong priority
   means P1 incidents sit unattended. Tolerable if there's human review for P1.
8. **Human checkpoints:** Auto-classified P1 tickets should notify a human
   immediately, not just sit in a queue.
9. **Success metrics:** Average triage time, classification accuracy vs. human
   baseline, P1 response time.

### Verdict: Agent

This is agent territory. The classification step requires interpreting
unstructured text and making a judgment. A rules-based workflow would need
hundreds of keyword mappings and still miss edge cases.

**Pattern:** Single agent with human-in-the-loop for P1 tickets.
**Tools:** Read ticket, search knowledge base, update ticket fields, send
notification (4 tools).
**Guardrails:** Cannot close tickets. Cannot change SLA. Must escalate to
human if confidence is low.

---

## Example 2: Monthly Invoice Processing (Workflow, Not Agent)

**Scenario:** A company processes 50 vendor invoices per month. Each invoice
arrives as a PDF. The process: extract key fields (vendor, amount, date, PO
number), match to a purchase order, flag discrepancies over 5%, route for
approval based on amount thresholds ($5K manager, $25K director, $50K VP).

### Walking through the framework

1. **Trigger:** Invoice PDF received via email or upload
2. **Outcome:** Invoice matched to PO, discrepancies flagged, sent to the
   right approver
3. **Steps:** Extract fields from PDF → match to PO → compare amounts → flag
   discrepancies → route for approval based on amount
4. **Which steps require judgment?** At first glance, extracting fields from a
   PDF seems like it needs judgment. But modern document extraction tools
   (OCR + template matching) handle standard invoices deterministically.
   The matching logic is straightforward (PO number lookup). The routing is
   pure rules (amount thresholds).
5. **Which steps are always the same?** All of them. Extract, match, compare,
   route. The path never changes.
6. **Systems:** Document extraction, ERP/procurement system, approval workflow
7. **What can go wrong?** OCR misreads a field. PO number not found. These are
   exception paths, not judgment calls.
8. **Human checkpoints:** Approval is already human. Exceptions (no PO match,
   OCR failures) route to AP team.
9. **Success metrics:** Processing time per invoice, exception rate, accuracy
   vs. manual processing.

### Verdict: Workflow

Every step follows a deterministic path. The "judgment" in field extraction
is better handled by specialized document extraction tools than an LLM agent.
An agent here would be slower, more expensive, and less reliable than a
workflow with good exception handling.

**Exception:** If invoices arrive in wildly different formats (handwritten,
multi-language, non-standard layouts) and the extraction step genuinely
requires interpreting ambiguous documents, the extraction step alone could
be an agent. But the rest stays workflow.

---

## Example 3: RFx Procurement Evaluation (Complex, Multi-Agent)

**Scenario:** A company issues RFPs for technology purchases. When vendor
responses arrive, they need to be evaluated against requirements, scored,
compared, and a recommendation produced. Currently takes a procurement team
2-3 weeks per RFP cycle with 5+ vendors.

### Walking through the framework

1. **Trigger:** Vendor response documents received for an active RFP
2. **Outcome:** Scored evaluation matrix, comparative analysis, recommendation
   with justification, draft award letter
3. **Steps:**
   - Parse vendor response documents (varying formats, lengths, structures)
   - Extract responses mapped to each RFP requirement
   - Score each response against evaluation criteria
   - Check compliance (mandatory requirements, certifications, insurance)
   - Compare vendors across all dimensions
   - Produce recommendation with supporting evidence
   - Generate draft award letter for the selected vendor
4. **Which steps require judgment?**
   - Parsing responses: vendors bury answers in different sections, use
     different terminology for the same concepts
   - Scoring: "meets requirement" vs. "partially meets" requires interpreting
     nuanced vendor language
   - Compliance checking: some certifications are equivalent but named
     differently (SOC 2 Type II vs. SSAE 18)
   - Comparative analysis: weighing tradeoffs across vendors
   - Recommendation: synthesizing all evidence into a defensible choice
5. **Which steps are always the same?** Generating the evaluation matrix
   template, formatting the award letter, sending notifications.
6. **Systems:** Document storage (read vendor responses), procurement system
   (RFP requirements, evaluation criteria), communication (notifications,
   award letters)
7. **What can go wrong?** Misinterpreting a vendor's response leads to unfair
   scoring. Missing a compliance gap means selecting a non-compliant vendor.
   These errors have financial and legal consequences.
8. **Human checkpoints:** Final recommendation must be reviewed by procurement
   lead. Compliance findings reviewed by legal. Award letter approved before
   sending.
9. **Success metrics:** Evaluation cycle time, scoring consistency across
   evaluators, stakeholder satisfaction with recommendation quality.

### Verdict: Multi-agent with human-in-the-loop

Almost every step requires genuine judgment. Multiple areas of expertise
needed (document analysis, compliance, comparative evaluation). Too complex
for a single agent.

**Pattern:** Orchestrator + workers with human-in-the-loop at critical gates.

**Agents:**
- **Document Analysis Agent**: Parses vendor responses, extracts answers
  mapped to requirements (tools: read documents, search within documents)
- **Compliance Agent**: Checks mandatory requirements, certifications,
  insurance (tools: read compliance requirements, verify certifications)
- **Evaluation Agent**: Scores responses, produces comparative matrix
  (tools: read extracted answers, read scoring criteria, write scores)
- **Recommendation Agent**: Synthesizes scores and compliance into a
  recommendation (tools: read evaluation data, generate report)

**Orchestration:** A workflow (not an agent) coordinates the sequence:
documents arrive → document analysis → compliance check + evaluation
(parallel) → human review gate → recommendation → human approval → award
letter generation.

**Guardrails:** No agent can send external communications. Scores must include
justification text, not just numbers. Compliance findings require evidence
references. Final award requires human approval.

**Why the orchestrator is a workflow, not an agent:** The coordination sequence
is the same every time. Documents always get analyzed before they get scored.
Compliance always runs parallel to evaluation. There's no decision about
what to do next. The judgment lives inside each specialist agent, not in
the coordination layer.

---

## Key Takeaways

1. **Not every step in a process needs the same solution.** Some steps are
   agents, some are workflows, some are just API calls. Mix and match.

2. **The judgment test is the deciding factor.** Walk through each step and ask
   "does this require interpreting something ambiguous?" If yes, agent. If no,
   workflow.

3. **Start simple, add complexity.** Example 1 is a single agent. Example 3
   needs four. But even Example 3 starts with a pilot (deploy one agent,
   prove it works, then expand).

4. **Orchestration is usually a workflow.** The coordination layer that decides
   "do A then B then C" is almost always deterministic. The intelligence lives
   in the individual agents, not in the routing.

5. **Guardrails scale with consequences.** Ticket triage (Example 1) has low
   stakes, minimal guardrails. Procurement evaluation (Example 3) has legal
   and financial risk, heavy guardrails with multiple human gates.
