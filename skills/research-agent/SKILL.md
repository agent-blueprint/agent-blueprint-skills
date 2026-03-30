---
name: research-agent
description: >-
  Multi-tool research methodology with automatic depth routing. Routes queries
  through free tools first (web search, page scraping), escalates to paid tools
  only when needed (Perplexity, Gemini Deep Research). Includes caching, source
  verification, and structured output. Use when researching companies, topics,
  markets, competitors, or any question requiring web information. Triggers:
  research, look into, find out about, investigate, what do we know about,
  deep research, company research, market analysis.
version: 0.1.0
author: Agent Blueprint
license: Apache-2.0
---

# Research Agent

A multi-tool research methodology with automatic depth routing. Start cheap,
escalate when needed, cache everything, cite every claim.

---

## Hard Rules

1. **Never fabricate research findings.** Every claim needs a source.
2. **Check the cache before starting.** Don't re-research what's already fresh.
3. **Start with free tools.** Only escalate to paid tools when free results are thin.
4. **Present findings by relevance, not by tool.** The user doesn't care which
   backend produced the answer.
5. **Disclose uncertainty.** If you couldn't verify a claim, say so explicitly.

---

## Depth Routing

Assess the query and pick the right tool. Always start with the cheapest
tool that can plausibly answer.

| Depth | Tool | When to use | Speed | Cost |
|-------|------|-------------|-------|------|
| **Quick** | WebSearch (native) | Single fact, recent news, quick lookup | Seconds | Free |
| **Scrape** | WebFetch (native) | Read a specific URL the user provides | Seconds | Free |
| **Better scrape** | Firecrawl (MCP) | JS-heavy pages, structured extraction, WebFetch fails | Seconds | ~$0.01 |
| **Broader** | Brave Search (MCP) | WebSearch results thin, need more results or domain filtering | Seconds | Free tier |
| **Medium** | Perplexity Ask (MCP) | AI-synthesized answer with citations, multi-faceted questions | 5-10s | Small |
| **Deep** | Perplexity Research (MCP) | Comprehensive multi-source investigation, company profiles | ~30s | Moderate |
| **Ultra-deep** | Gemini Deep Research (API) | Market landscapes at extreme breadth, when Perplexity is thin | 10-20 min | $2-5 |

### Tool Priority

1. **WebSearch** -- native, free, always try first
2. **WebFetch** -- native, free, for reading specific URLs
3. **Brave Search** -- MCP, when WebSearch is insufficient
4. **Firecrawl** -- MCP, when WebFetch fails (JS rendering, structured extraction)
5. **Perplexity Ask** -- MCP, quick AI-synthesized answers with citations
6. **Perplexity Research** -- MCP, the workhorse for deep research (best quality-to-cost ratio)
7. **Gemini Deep Research** -- API, last resort for extreme breadth

### Routing Signals

**Use Quick (WebSearch/Brave) when:**
- Simple factual question
- Looking up a single data point (funding round, CEO name, HQ location)
- Checking recent news
- Verifying a claim

**Use Medium (Perplexity Ask) when:**
- Need a synthesized answer from multiple sources
- Comparing a few options
- Getting a topic overview with decent depth

**Use Deep (Perplexity Research) when:**
- Full company profile or due diligence
- Preparing for a demo, pitch, or advisory engagement
- Need strategic initiatives, financials, leadership, tech stack

**Escalation pattern:** Start with the cheapest tool that can plausibly answer.
If results are thin, escalate one level. Tell the user before escalating to
paid tiers.

---

## Supplemental Source Checks

After the primary research call, run targeted follow-ups to fill gaps. These
are cheap/free and take about a minute total. Run as many in parallel as possible.

| Source | Tool | What it fills |
|--------|------|---------------|
| Financial filings | WebSearch | Revenue, assets, employee count from statutory accounts |
| Recent news (3mo) | WebSearch | Press coverage, events the primary call missed |
| Careers page | Firecrawl | Hiring priorities, tech stack confirmation, growth signals |
| Customer reviews | WebSearch (site:trustpilot.com, g2.com) | Customer sentiment, pain points |
| Competitive moves | WebSearch per competitor | What competitors are doing |

**When to run supplementals:**
- Always for company research or due diligence
- Always for demo or advisory prep
- Skip for quick lookups or general topic questions

**How to use findings:** Merge into the primary output organized by section
(not by source). Flag anything that contradicts the primary research. Note
gaps that remain even after supplementals.

---

## Caching

Cache research results to avoid redundant queries.

### Cache file format

Filename: `{slugified-subject}-{date}.md`

```markdown
# Research: {Subject}
Researched: {date}
Depth: {quick|medium|deep}
Tools used: {list}

## Findings
{content}

## Sources
- {url} -- {description}

## Gaps
- {anything still unknown}
```

### Freshness rules

| Depth | Fresh for |
|-------|-----------|
| Quick | 24 hours |
| Medium | 3 days |
| Deep | 7 days |
| Ultra-deep | 14 days |

Before starting research, check if a cache file exists. If fresh, use it.
If stale, re-research (but reference old results for comparison).

---

## Output Format

Present findings in a clean, scannable format:

```markdown
## Research: {Subject}
Depth: {level} | Sources: {count} | {date}

### Key Findings
- {finding 1} -- source: {url}
- {finding 2} -- source: {url}

### Details
{structured content organized by relevance}

### Gaps
- {anything you couldn't find or verify}
```

Lead with what matters most. Don't pad.

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| Thin WebSearch results | Try different query terms, escalate to Brave/Perplexity |
| Firecrawl returns empty | Page may be JS-rendered. Try with `waitFor: 5000`. Or try `firecrawl_map` first. |
| Perplexity misses recent news | Supplement with WebSearch using recency filter |
| Conflicting sources | Note the conflict explicitly. Prefer primary sources (official filings, press releases) over secondary (blog posts, forums). |

---

## Adapting to Your MCP Setup

This skill references several MCP servers. Use what you have available:

- **No MCP at all?** WebSearch and WebFetch (native tools) handle most quick
  and medium research. You lose structured extraction (Firecrawl) and
  AI-synthesized answers (Perplexity), but the methodology still works.
- **Only Brave Search?** Use it as your primary search. Skip Firecrawl steps.
- **Only Perplexity?** Jump straight to medium/deep depth. Skip the free tier escalation.
- **Full stack?** Follow the routing table as written.

The methodology is tool-agnostic. The routing table is a recommendation, not
a requirement. Use the best tools you have access to.

---

*Built by [Agent Blueprint](https://agentblueprint.ai) -- AI advisory for enterprise agent deployment.*
