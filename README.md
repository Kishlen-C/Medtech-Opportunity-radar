# Medtech Opportunity Radar

A personal agent that watches the web for placements, research studentships, industrial years,
competitions and hackathons in medtech, scores each one against *your* criteria, and prepares a
one-page company brief on demand. Built for the UCL x TinyFish buildathon.

It is deliberately small: **two scheduled agents, a set of TinyFish monitors, and a Notion workspace.**
There is no server to run and no application code. The "code" is the prompts and configuration in this repo.

## What it does

| Agent | When | Output |
|---|---|---|
| **Weekly digest** (`prompts/weekly-digest.md`) | Mondays 07:20 London | Reads every monitor, finds what is *new*, scores it 0-5, logs it in Notion, sends a <250-word digest with upcoming deadlines and stale sources |
| **Brief preparer** (`prompts/brief-preparer.md`) | Every 3 hours, 08:25-20:25 | For any opportunity row where you ticked **Prepare brief**, researches the organisation and writes a 7-section brief: snapshot, the role, what the application needs, what kind of people they want, catches, questions to ask, sources |

## Architecture

```
TinyFish Monitors (cron)         Claude scheduled tasks             Notion
 fetch: 10 pages (UCL, NHS,    -->   Weekly digest agent   --writes--> Opportunities
        employers, aggregators,     reads monitor runs,              Sources (health)
        hackathons)                 dedupes, scores                  Briefs
 search: 2 open-web queries
                                  Brief preparer agent <--polls--  "Prepare brief" checkbox
                                   TinyFish Search + Fetch (free)
```

### TinyFish endpoints used

- **Monitor** - scheduled change detection on career pages (`fetch`) and open-web queries (`search`)
- **Fetch** - read an opportunity's own page and the company's about/careers pages
- **Search** - company news and open-web discovery
- **Agent** - fallback only, hard-capped at 30 steps per brief, for pages that cannot be read any other way

The design rule is *cheapest endpoint first*. Search and Fetch are free, Monitor is $0.005 per completed run,
Agent costs $0.016 per step. At this configuration the monitors cost roughly **$0.50 a month**.

## Guardrails (they are in the prompts, not just here)

- Public information only. Never logs in, never applies, never messages third parties.
- Unknown means `not stated`. Conflicting sources are reported side by side, not resolved by guessing.
- Inferences are labelled `my read`.
- It does not assume your eligibility (work rights, year of study). It states what each source says and flags conflicts.

## Install (Claude Code plugin)

```
/plugin marketplace add Kishlen-C/Medtech-Opportunity-radar
/plugin install medtech-opportunity-radar@medtech-opportunity-radar
```

Then ask Claude: **"set up the radar"**. The `radar-setup` skill asks about your field and criteria, creates the Notion workspace,
registers the TinyFish monitors, and schedules the two agents. Requires the TinyFish and Notion connectors.
The `radar-weekly-digest` and `radar-brief` skills can also be run by hand at any time.
Status: the plugin structure has not been install-tested end to end; the underlying agents have run live.

## Manual setup

You need: a TinyFish account, a Notion workspace, and Claude with the TinyFish and Notion connectors and scheduled tasks.

1. **Notion.** Create a page called "Medtech Opportunity Radar" containing your criteria and scoring rubric
   (see `notion/rubric-template.md`). Create the three databases in `notion/schema.md`.
2. **Monitors.** Create each entry in `config/monitors.json` as a TinyFish monitor (`create_monitor`).
   Replace the sources with ones relevant to you.
3. **Prompts.** In `prompts/`, replace every `{{PLACEHOLDER}}` with your Notion IDs and profile, then create each as a
   Claude scheduled task with the cron shown at the top of the file.
4. **Use it.** Tick **Prepare brief** on any opportunity. The status goes Requested -> Ready.

## Known limitations

- **The "button" is a checkbox.** A polling agent picks it up, so a brief takes up to ~3 hours. A real Notion button needs an in-app automation.
- **Static career pages are weak sources.** Many large employers load listings through ATS portals that a page-change monitor cannot see. Search monitors and aggregators cover the gap.
- **Some sites block scraping.** Gradcracker returns 403 to automated fetches; it is logged as `Stale` rather than worked around.
- **Change-filtering by `purpose` is not guaranteed.** The digest agent does its own diff and relevance check rather than trusting the monitor to filter.
- **Search results are noisy.** The digest ignores LinkedIn, Indeed and Jooble.
- **Unproven at scale.** Built and configured in a single session; the first full weekly cycle is the real test.

See `docs/design-notes.md` for the reasoning and what to improve next.

## Licence

MIT
