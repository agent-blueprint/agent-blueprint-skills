# Refund Helper: one-page summary

This example is made up. The company, people and numbers are fictional.

Version 1, 2026-11-01. Stage: Proposed.

## Summary

- Decision needed: pilot for 90 days.
- Allowed: refunds up to $100 on its own. Anything larger goes to a person, and it can't pay a different card.
- Costs: $6,000 to build and $986 a month, including $590 of staff time and an estimated $288 in mistakes.
- Worth it: probably, but mostly as staff time. It frees up about 50 hours a month, saves no cash unless spending actually drops, and pays back in about 5.4 months at full target and about 15.7 at half. Confidence is low until the pilot measures it.

## 1. Purpose

- Does: reads customer refund requests, checks them against the order and the refund policy, and issues refunds up to $100 for the support team.
- Doesn't do: chargebacks, fraud cases, or anything where the customer disputes the policy.

## 2. Authority

- Can do on its own: issue a refund up to $100 on a verified order. Enforced by: the refund service, which rejects anything over the cap.
- Needs human approval: refunds over $100, and a second refund on the same order. Enforced by: the refund service, which sends these to the support queue.
- Must never: refund to a different card or account. Enforced by: the refund tool, which has no destination field.
- Must never: promise a customer anything outside the policy. Not enforced: this is only an instruction to the agent. The monthly review in section 4 looks for it.
- Tools and data: order lookup (reads), refund policy (reads), support tickets (reads and changes), refund tool (changes).
- Model and data handling: a commercial model API under a company contract. It sees customer names, order details and request text, but no card numbers.
- Runs as: the service account refund-helper, with no admin rights.
- Limit on what it can spend or move: $3,000 in refunds a day. Enforced by: the refund service, which stops and alerts the support lead.
- Limit on its own usage bill: $150 a month. Enforced by: the spending limit on the model vendor account.
- If it makes a mistake: ticket changes can be reversed. A paid refund usually can't be, so it's counted as a cost in section 5.

## 3. Proof

- Tested with: 200 past requests, 20 messages trying to talk it into a bigger refund, and repeat requests for the same order.
- Passes when: 95% of decisions match what the support team decided, with zero refunds over the cap and under 1% paid in error. The support lead set this bar.
- Results: Open. Testing finishes before the pilot starts. Decides: Dana Reyes.
- Every action is recorded in: the refund service log and the ticket history.

## 4. Owner

- Accountable person: Dana Reyes, support lead.
- Who can stop it, and how: Dana or the on-call engineer, using the refund_agent_enabled switch. Enforced by: the refund service.
- If it's stopped, the work goes to: the support queue.
- Who gets told when it acts: the customer by email, and #refund-log for the team.
- Who reviews its work: the support team checks 1 in 10 refunds, about 10 hours a month.

## 5. Cost

- Volume: 1,200 requests a month, measured over the last three months.
- Usage cost per run: $0.04, estimated until the pilot measures it.
- Monthly cost: $986, made up of $48 usage (1,200 x $0.04), $60 hosting, $350 review (10 hours x $35), $240 upkeep (4 engineering hours x $60), and an estimated $288 in mistakes (1% of 720 requests x $40 average, assuming every handled request is paid).
- Cost to build: $6,000.

## 6. Promise

- The one number: support hours spent on refunds each month, from the ticket system.
- Today: 120 hours (1,200 requests at 6 minutes each).
- Target and date: 70 hours by 90 days after going live, including 10 hours of review.
- This means: staff time freed up. No one's job or budget changes on this page.
- Must not get worse: refunds paid in error stay under 1% of what the agent issues. The team's own error rate is Open, and the pilot's first month measures it. Decides: Dana Reyes.
- Stop or rethink if: errors go over 1%, or the agent handles under 30% of requests after 90 days.
- Instead of this: keep handling refunds by hand, or auto-approve only the simplest cases with a fixed rule. The rule wasn't priced for this page.
- ROI math: 720 requests handled x 5 minutes saved / 60 x $35 an hour = $2,100 monthly value; $2,100 - $986 = $1,114 monthly net; $6,000 / $1,114 = about 5.4 months payback at full target. At half the target, value is $1,050, cost is $667 (half the review and mistakes) and net is $383, so payback is about 15.7 months. A 90-day pilot won't pay back on its own.
- Cash or time: cash goes down by about $396 a month (usage, hosting and mistakes). The cash never pays back by itself. The $1,750 of freed-up support time (50 hours) is capacity for other work and becomes cash only if spending drops, and it's what covers the $240 of upkeep.
- Whose estimates these are: Dana Reyes, for the 60% handled and the 5 minutes saved per request.

## 7. Result

Empty until the check-back.

- Check-back date: 90 days after going live.

Approved by: Open. Decides: Sam Ito, head of customer operations.
