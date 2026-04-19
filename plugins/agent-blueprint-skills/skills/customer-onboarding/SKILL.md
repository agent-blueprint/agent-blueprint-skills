---
name: customer-onboarding
description: >-
  Blueprint pattern for an AI agent team that orchestrates customer onboarding.
  5-agent architecture with pipeline orchestration. Use when a user asks you
  to build customer onboarding automation, client intake, account provisioning,
  new customer setup, or onboarding workflow agents. Triggers: customer
  onboarding, client intake, account setup, new customer, onboarding
  automation, KYC, identity verification, customer provisioning, welcome
  workflow, client activation, account creation, onboarding agents,
  customer lifecycle.
version: 0.1.0
author: Agent Blueprint
license: Apache-2.0
---

# Customer Onboarding -- Agent Team Blueprint Pattern

An architecture pattern for automating customer onboarding with a 5-agent
team. Covers initial intake through full account activation and handoff
to ongoing service.

This is a **pattern**, not a full implementation plan. It gives you the agent
team structure, roles, orchestration, and integration points. For a complete
blueprint with business case, financial projections, phased implementation
plan, and deployment guides, generate one via the Agent Blueprint MCP server
(`agentblueprint` npm package) or at [agentblueprint.ai](https://agentblueprint.ai).

---

## The Problem

Customer onboarding is the highest-stakes moment in the customer lifecycle.
Slow onboarding drives churn. Manual handoffs between departments drop tasks.
Document collection drags for weeks. Compliance checks (KYC/AML, credit,
identity) are bottlenecks. New customers get inconsistent experiences
depending on which team handles their case.

## Agent Team (5 Agents)

### 1. Intake Agent (Entry Point)

**Role:** Receives new customer applications, validates completeness, creates
the onboarding case, and kicks off the pipeline. Single entry point regardless
of channel (web form, email, API, sales handoff).

**Inputs:** Customer application data, channel metadata, sales context
(if warm handoff), attached documents

**Outputs:** Onboarding case with:
- Customer profile (name, entity type, contact info, industry)
- Product/service tier selected
- Document checklist (required vs. received vs. missing)
- Risk tier (standard, enhanced, high) based on initial signals
- Onboarding timeline with milestones

**Decision logic:**
- If application is complete → Verification Agent immediately
- If documents are missing → Communication Agent to request, then pause pipeline
- If high-risk signals (industry, geography, entity structure) → flag for
  enhanced due diligence, set risk tier to "enhanced"
- If existing customer (upsell/cross-sell) → pull existing profile, skip
  redundant verification steps

**Tools needed:** Application form read, customer database lookup (existing
customer check), document checklist generator, case creation

### 2. Verification Agent

**Role:** Validates customer identity, documents, and compliance requirements.
Runs all checks that must pass before account provisioning.

**Inputs:** Customer profile, submitted documents, risk tier

**Outputs:**
- Verification results matrix (each check: pass/fail/pending)
- Document authenticity assessment
- Compliance clearance (KYC/AML pass, sanctions screening, credit check)
- Risk score with supporting factors
- Exceptions requiring human review

**Verification checks (configurable by industry):**
- Identity verification (government ID, business registration)
- Address verification
- Sanctions and PEP screening
- Credit/financial checks (if applicable)
- Industry-specific compliance (healthcare: HIPAA BAA, finance: SOX, etc.)
- Beneficial ownership (for business entities)

**Decision logic:**
- If all checks pass → Provisioning Agent
- If check fails with clear disqualifier → reject, Communication Agent
  notifies with reason
- If check is inconclusive → flag for human compliance review, pause pipeline
- If document quality is poor (blurry, expired) → Communication Agent requests
  resubmission

**Tools needed:** Identity verification API, sanctions screening, credit check
API, document OCR/validation, compliance rules engine

### 3. Provisioning Agent

**Role:** Creates and configures all accounts, access, and resources the
customer needs. Executes the setup that used to require coordination across
3-5 internal teams.

**Inputs:** Verified customer profile, product/service tier, compliance clearance

**Outputs:**
- Account created with correct tier and configuration
- User credentials generated and stored securely
- Access permissions set per product tier
- Billing/subscription activated
- Integration endpoints configured (API keys, SSO, etc.)
- Welcome materials prepared (customized to tier and industry)

**Provisioning steps (parallel where possible):**
- CRM record creation/update
- Billing account and subscription setup
- Product access and licensing
- SSO/authentication configuration
- API credentials and integration setup
- Storage/workspace allocation
- Notification preferences defaulted

**Decision logic:**
- If provisioning step fails → retry once, then flag for ops team
- If tier requires custom configuration → create setup task for implementation team
- Once all provisioning complete → Communication Agent sends activation package

**Tools needed:** CRM API, billing system API, identity provider (SSO setup),
product access management, API key generation, workspace provisioning

### 4. Communication Agent

**Role:** Manages all customer-facing communications throughout onboarding.
Ensures the customer always knows their status and next action.

**Inputs:** Onboarding case, milestone events, action items

**Outputs:**
- Welcome communications (branded, tier-appropriate)
- Document request messages with clear instructions
- Status updates at each milestone
- Activation package (credentials, getting-started guide, key contacts)
- Escalation alerts for stalled onboarding cases
- Feedback request at onboarding completion

**Communication channels:** Email, in-app notifications, SMS (configurable).
Channel preference set during intake.

**Decision logic:**
- If document request unanswered after 48 hours → send reminder
- If second reminder unanswered → escalate to account owner
- If onboarding stalled >7 days → alert customer success manager
- If activation confirmed → trigger 30-day check-in schedule

**Tools needed:** Email/notification system, template engine, document upload
portal, scheduling system, escalation workflow

### 5. Handoff Agent

**Role:** Finalizes onboarding and transitions the customer to ongoing service.
Ensures nothing falls through the gap between "onboarded" and "active customer."

**Inputs:** Completed onboarding case, all provisioning and verification results

**Outputs:**
- Customer success package (structured summary for the assigned CSM)
- Health score baseline (initial engagement metrics)
- Onboarding retrospective (cycle time, friction points, exceptions)
- Scheduled touchpoints (30/60/90-day check-ins)
- Case closed with full audit trail

**Handoff package includes:**
- What was sold (products, tier, commitments)
- What was configured (integrations, customizations, access)
- What was flagged (exceptions, special requirements, risk notes)
- Who the contacts are (decision-maker, technical contact, billing contact)
- Next expected actions (training, first usage, integration completion)

**Decision logic:**
- If customer hasn't logged in within 7 days of activation → Communication
  Agent sends nudge, alert CSM
- If exceptions were raised during onboarding → include prominently in
  handoff package
- If onboarding exceeded target duration → flag for process improvement review

**Tools needed:** Customer success platform, health score calculation, calendar
scheduling, case management, analytics

---

## Orchestration Pattern

**Pattern:** Pipeline with parallel branches

Onboarding is fundamentally sequential (you can't provision before verifying),
but several steps within each stage run in parallel.

```
Application arrives
        │
        ▼
  ┌───────────┐
  │  Intake   │◄──── documents missing ────┐
  │  Agent    │                            │
  └─────┬─────┘                            │
        │                          ┌───────────────┐
        ▼                          │Communication  │
  ┌──────────────┐                 │    Agent      │
  │ Verification │── doc issues ──►│  (requests    │
  │    Agent     │                 │   resubmit)   │
  └──────┬───────┘                 └───────────────┘
         │                                ▲
         ▼                                │
  ┌──────────────┐                        │
  │ Provisioning │── activation ──────────┘
  │    Agent     │   package
  └──────┬───────┘
         │
         ▼
  ┌──────────────┐
  │   Handoff    │
  │    Agent     │
  └──────┬───────┘
         │
         ▼
   Active customer
```

The Communication Agent runs **alongside the pipeline**, not as a step in it.
Any agent can trigger communications. The pipeline pauses when waiting for
customer action (document submission, verification response).

**Human-in-the-loop checkpoints:**
- Compliance exceptions (Verification Agent flags, human reviews)
- High-risk customer approval (enhanced due diligence requires human sign-off)
- Custom provisioning (non-standard configurations routed to implementation team)
- Stalled onboarding escalation (Communication Agent alerts CSM)

---

## Integration Points

| Integration | Purpose | Agent(s) |
|-------------|---------|----------|
| **CRM** | Customer records, account creation, contact management | Intake, Provisioning, Handoff |
| **Identity verification** | ID validation, document authentication | Verification |
| **Compliance/screening** | KYC/AML, sanctions, PEP, credit checks | Verification |
| **Billing/subscription** | Account setup, payment method, plan activation | Provisioning |
| **Identity provider** | SSO configuration, user provisioning | Provisioning |
| **Document management** | Store/retrieve customer documents, track completeness | Intake, Verification |
| **Notification system** | Email, SMS, in-app messaging | Communication |
| **Customer success platform** | Health score, touchpoint scheduling, CSM assignment | Handoff |
| **Analytics** | Onboarding cycle time, conversion, drop-off tracking | Handoff |

---

## Key Metrics

| Metric | What it measures |
|--------|-----------------|
| Time to activation | Days from application to first product login |
| Completion rate | % of started onboardings that reach active status |
| Document cycle time | Days from first request to all documents received |
| Verification pass rate | % of customers clearing verification on first attempt |
| Drop-off point analysis | Where in the pipeline customers abandon onboarding |
| 30-day engagement | % of onboarded customers actively using the product at day 30 |

---

## Common Pitfalls

- **Treating onboarding as a single event.** It's a pipeline with async waits.
  Design for the customer going quiet for 3 days mid-document-collection,
  then picking back up.
- **Blocking the entire pipeline on one check.** Run independent verification
  checks in parallel. Don't wait for credit check results before starting
  sanctions screening.
- **Forgetting the handoff.** Onboarding isn't done when the account is
  provisioned. It's done when the customer is successfully using the product.
  The Handoff Agent is the most commonly skipped and most impactful agent.
- **Over-automating compliance.** Verification agents handle the volume.
  Humans handle the exceptions. Never auto-approve a flagged compliance check.
- **Channel inconsistency.** If the customer started onboarding via email,
  don't switch to in-app notifications for document requests. Meet them where
  they are.

---

## Getting a Full Blueprint

This pattern gives you the architecture. A full Agent Blueprint adds:

- **Business case** with ROI projections based on your onboarding volume,
  current cycle time, and churn-from-slow-onboarding metrics
- **Implementation plan** with phased rollout starting from a single customer
  segment through full onboarding automation
- **Deployment guide** as an Agent Skill directory (12-14 files) that your
  coding agent can follow step-by-step
- **Platform-specific expert skills** for your CRM and identity platform

Generate a full blueprint: install the `agentblueprint` MCP server
(`npx agentblueprint`) or visit [agentblueprint.ai](https://agentblueprint.ai).

---

*Built by [Agent Blueprint](https://agentblueprint.ai) -- AI advisory for
enterprise agent deployment.*
