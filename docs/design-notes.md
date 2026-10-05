# Design notes

## Why this idea
Opportunities in this space are scattered across university pages, NHS trusts, company early-careers sites
and aggregators, and the application windows are short. The valuable output is not a list of links, it is
*scored, deduplicated, eligibility-aware* findings plus the research needed to decide whether to apply.

## Decisions and trade-offs

**Cheapest endpoint first.** Search and Fetch are free; Agent steps cost $0.016 each. Monitors do the watching,
Fetch reads details, and Agent is a capped fallback. Fixed monitor cost is roughly 10 daily/weekly fetch monitors
at $0.005 per run, about $0.50 a month.

**Notion as the interface, not a custom app.** No hosting, no auth, and the database views are the product.
The cost is that automation is limited to what Notion's MCP can do.

**Checkbox + poller instead of a button.** An agent-launching Notion button could not be created through the
available tools. A scheduled poller gives the same outcome with up to ~3 hours of latency.

**The digest agent does its own diffing.** The monitor `purpose` field is an instruction, not a guarantee, so
the agent re-checks every candidate against Notion before adding it.

**Refusing to guess.** The most expensive failure for a student is wasting a week on an application they
cannot take up. The brief agent reports what the posting and the employer each say about eligibility and flags
conflicts instead of resolving them. In testing this surfaced a real conflict between an aggregator listing and the
employer's own FAQ.

## What failed or was weak
- Gradcracker blocks automated fetches (403). Logged as Stale, not circumvented.
- Large employers' career pages are static shells over ATS portals, so change detection sees little.
- Search results are noisy; job boards are excluded in the prompt.

## Next steps
1. Run three weeks and measure: items surfaced that the user would not have found manually; digests actually read.
2. Add a deadline reminder agent (14 and 3 days out).
3. Replace weak monitors with employer-specific search queries.
4. Add a "tailor my CV bullet points to this brief" step, with the user supplying the CV.
