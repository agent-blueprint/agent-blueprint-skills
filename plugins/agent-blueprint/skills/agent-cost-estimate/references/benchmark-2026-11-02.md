# What everyday AI agent jobs cost: measured runs, 2026-11-02

Ten realistic jobs, five runs each on Claude Code (claude-opus-5-5) and Codex (gpt-6-astra). All 100 runs passed their checks. Typical cost per job was $0.09 to $0.35 for Claude Code and an estimated $0.13 to $0.34 for Codex. Cost tracked how much the agent had to read more than how hard the job was.

Claude cost is what Claude Code reports, priced at Anthropic API rates. Codex cost is estimated from its tokens at OpenAI's standard API prices for gpt-6-astra on that date ($10 per million input tokens, $1 cached input, $50 output). Subscriptions charge differently. Tokens are the stable measure; prices change.

## How to use this for an estimate

1. Pick the job below closest to yours and scale by how much more or less the agent must read. Each job says how big its input was; real emails, threads and documents are usually longer.
2. Multiply typical input and output tokens by your model's current prices. Most input is cached (about 81% for Claude Code and 88% for Codex here), and cached input is much cheaper.
3. Budget for the worst case. Most jobs' most expensive run was within 1.3x of typical; one data-cleanup run was 2.3x.
4. Expect a floor even for tiny jobs: about 29k input tokens for Claude Code and 60k for Codex here, because each agent re-reads its own instructions and tools at every step.

## Results per job (typical, then worst run of five)

### SQL questions (sql)

Answer five questions from a small SQLite shop database (150 customers, 1,200 orders).

- Claude Code: passed 5/5. Input 46k tokens (worst 59k), output 1,861, 4 tool calls, 21 s. Cost $0.09 (worst $0.12).
- Codex: passed 5/5. Input 61k tokens (worst 61k), output 673, 3 tool calls, 21 s. Estimated cost $0.13 (worst $0.17).

### Merge duplicated code (refactor)

Replace three copies of a send-email function with one, without changing any message sent.

- Claude Code: passed 5/5. Input 29k tokens (worst 29k), output 1,975, 2 tool calls, 20 s. Cost $0.09 (worst $0.09).
- Codex: passed 5/5. Input 64k tokens (worst 82k), output 1,186, 4 tool calls, 32 s. Estimated cost $0.25 (worst $0.26).

### Find two bugs (bughunt)

A failing report test whose real causes are a CSV parser that drops quoted lines and a month-key format.

- Claude Code: passed 5/5. Input 40k tokens (worst 51k), output 1,821, 3 tool calls, 20 s. Cost $0.10 (worst $0.11).
- Codex: passed 5/5. Input 64k tokens (worst 79k), output 767, 5 tool calls, 27 s. Estimated cost $0.15 (worst $0.21).

### Sales analysis (analysis)

Five metrics from 5,000 orders in a CSV: revenue by region, top products, refund rate, best month, average order.

- Claude Code: passed 5/5. Input 49k tokens (worst 50k), output 2,542, 4 tool calls, 26 s. Cost $0.11 (worst $0.12).
- Codex: passed 5/5. Input 61k tokens (worst 70k), output 736, 3 tool calls, 25 s. Estimated cost $0.17 (worst $0.18).

### Data cleanup (clean)

Clean 360 customer rows: trim, fix case, three date formats, phone numbers, drop blanks, resolve 50 duplicates.

- Claude Code: passed 5/5. Input 54k tokens (worst 121k), output 2,920, 4 tool calls, 29 s. Cost $0.13 (worst $0.21).
- Codex: passed 5/5. Input 62k tokens (worst 70k), output 845, 3 tool calls, 26 s. Estimated cost $0.15 (worst $0.17).

### Add a feature with tests (feature)

Add capped discount codes (like SAVE20CAP50) to a small shop package, with new tests, checked by hidden tests.

- Claude Code: passed 5/5. Input 43k tokens (worst 56k), output 3,549, 3 tool calls, 33 s. Cost $0.14 (worst $0.16).
- Codex: passed 5/5. Input 64k tokens (worst 72k), output 1,468, 4 tool calls, 37 s. Estimated cost $0.21 (worst $0.28).

### Email triage (triage200)

Categorize 200 short support emails (about 20 words each, 3,900 words in all) into five categories and flag the 15 that are urgent.

- Claude Code: passed 5/5. Input 57k tokens (worst 58k), output 2,414, 3 tool calls, 28 s. Cost $0.18 (worst $0.20).
- Codex: passed 5/5. Input 113k tokens (worst 124k), output 836, 4 tool calls, 31 s. Estimated cost $0.31 (worst $0.35).

### Refund decisions (replies)

Decide 14 short refund requests under a one-page written policy with proration, a fee, an approval threshold and an unknown sender.

- Claude Code: passed 5/5. Input 70k tokens (worst 85k), output 4,591, 5 tool calls, 45 s. Cost $0.19 (worst $0.20).
- Codex: passed 5/5. Input 80k tokens (worst 80k), output 1,039, 4 tool calls, 36 s. Estimated cost $0.22 (worst $0.25).

### Questions across 60 notes (corpus)

Answer five questions across 60 meeting notes (6,200 words) where decisions change over time.

- Claude Code: passed 5/5. Input 102k tokens (worst 129k), output 2,250, 5 tool calls, 25 s. Cost $0.22 (worst $0.23).
- Codex: passed 5/5. Input 98k tokens (worst 119k), output 559, 4 tool calls, 25 s. Estimated cost $0.27 (worst $0.35).

### Long contract (longdoc)

Pull 12 current terms from a 16,000-word contract with a later amendment and an outdated exhibit.

- Claude Code: passed 5/5. Input 148k tokens (worst 169k), output 1,781, 5 tool calls, 21 s. Cost $0.35 (worst $0.36).
- Codex: passed 5/5. Input 151k tokens (worst 166k), output 976, 6 tool calls, 36 s. Estimated cost $0.34 (worst $0.44).

## Method

- Every job uses made-up data and has a fixed pass check; code jobs also run hidden tests the agent never sees. A reference solution for each job passes the same checks, and an untouched copy fails them.
- Each run starts in a fresh copy of the job folder. Agents ran without personal settings, memory, skills or MCP servers, with only their built-in file and shell tools. No web access was needed.
- Claude Code 2.1.307 and Codex CLI 0.154.0, default reasoning effort, one Mac, on 2026-11-02.

## Limits

- The jobs are small and synthetic. Real work is larger and messier.
- Both agents passed everything, so this measures the cost of work done right, not where agents fail.
- One day, one model per agent, five runs per job.

Per-run data: runs.json. Medians and worst cases: summary.json. Published by Agent Blueprint (agentblueprint.ai).
