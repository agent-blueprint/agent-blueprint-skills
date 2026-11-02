# [Agent name]: one-page summary

Version [1], [date]. Stage: [Proposed, Approved, Live or Measured].

## Summary

- Decision needed: [build, pilot, keep running, expand or stop]
- Allowed: [what it does alone, what needs approval, its hard limits]
- Costs: [cost to build, and monthly cost including human time]
- Worth it: [cash impact, time freed up, payback, how confident you are]

How to fill this in: when you don't know an answer, write `[Open]` with who decides and your suggested default. Don't guess a number. End every rule with `Enforced by:` and the setting or system that makes it true, or `Not enforced:` and why. An instruction the agent is only asked to follow counts as not enforced.

## 1. Purpose

- Does: [the job, for whom, in one or two sentences]
- Doesn't do: [what's out of scope]

## 2. Authority

- Can do on its own: [actions]. Enforced by: [setting or system]
- Needs human approval: [actions, and who approves]. Enforced by: [setting or system]
- Must never: [actions]. Enforced by: [setting or system] or Not enforced: [why]
- Tools and data: [each connection, and whether it reads or changes things]
- Model and data handling: [vendor and model, how sensitive the data is, where it goes]
- Runs as: [whose account or which service account, with what permissions]
- Limit on what it can spend or move: [amount and period]. Enforced by: [setting or system]
- Limit on its own usage bill: [amount and period]. Enforced by: [setting or system]
- If it makes a mistake: [how it gets undone, or that it can't be]

## 3. Proof

- Tested with: [cases, including attempts to manipulate it and the failure that matters most]
- Passes when: [result, and who set that bar]
- Results: [link or Open]
- Every action is recorded in: [where to look]

## 4. Owner

- Accountable person: [name and role]
- Who can stop it, and how: [person, and the exact switch]. Enforced by: [setting or system]
- If it's stopped, the work goes to: [person or team]
- Who gets told when it acts: [person or channel, or nobody]
- Who reviews its work: [person, how often, hours a month]

## 5. Cost

- Volume: [runs per month], [measured or estimated]
- Usage cost per run: [tokens, credits or dollars], [measured or estimated]
- Monthly cost: [usage + fixed costs + human review + upkeep + expected cost of mistakes]
- Cost to build: [amount or Open]

## 6. Promise

- The one number: [metric, and where it's measured]
- Today: [baseline, or "no baseline yet, measuring until (date)"]
- Target and date: [value by date]
- This means: [cash saved, staff time freed up or better service]
- Must not get worse: [the failure that matters more than the gain]
- Stop or rethink if: [the result that ends it, set in advance]
- Instead of this: [what you'd do without the agent, such as nothing or simpler automation]
- Other value: [revenue, quality or risk reduced, if time saved doesn't capture it]
- ROI math: [runs handled x minutes saved per run / 60 x hourly cost = monthly value; monthly value - monthly cost = monthly net; cost to build / monthly net = payback in months; repeat at half the target]
- Cash or time: [how much of the net is real cash, and how much is staff time that only becomes cash if spending drops]
- Whose estimates these are: [name]

## 7. Result

Leave this empty until the check-back.

- Check-back date: [usually 90 days after going live]
- Measured: [the number from section 6, and the failure rate]
- Still matches this page: [yes, or what changed in its tools, permissions or settings]
- Decision: [keep, change or retire], by [name], on [date]

Approved by: [name, role, date]
