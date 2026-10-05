---
name: radar-setup
description: One-time setup of the Medtech Opportunity Radar. Creates the Notion workspace, registers the TinyFish monitors from config/monitors.json, and schedules the weekly digest and brief-preparer agents. Use when the user says "set up the radar", "install the opportunity radar" or similar.
---

# Set up the Medtech Opportunity Radar

Requires the TinyFish and Notion connectors and scheduled tasks. If either connector is missing, say so and stop.

1. **Ask the user** (one message, then wait): their field and year of study, location, the opportunity types they want, and anything about eligibility they want the agent to rely on. Do not infer or assume work rights or personal circumstances.
2. **Notion.** Create a page "Medtech Opportunity Radar" containing their criteria and the scoring rubric from `notion/rubric-template.md` (0-5, 3 and above goes in the digest). Under it create the three databases Sources, Opportunities, Briefs using `notion/schema.md`. Add the brief-request columns to Opportunities after Briefs exists (relation target must be a bare UUID).
3. **Monitors.** Read `config/monitors.json`. Show the user the list and ask which to keep or replace for their own field. Create the approved ones with TinyFish `create_monitor`, using the `CRON_TZ` prefix as given. Log each in the Sources database with its Monitor ID and Status. If a source returns an HTTP 403 or fails, cancel that monitor and log the source as Stale; do not work around blocks.
4. **Scheduled tasks.** Create two scheduled tasks from the skills `radar-weekly-digest` (Mondays 07:20, `CRON_TZ=Europe/London 20 7 * * 1`) and `radar-brief` (`CRON_TZ=Europe/London 25 8-20/3 * * *`). Each task's prompt is the body of the matching skill, with the user's criteria page named. Tell the user the cadence and that briefs take up to about 3 hours after ticking the checkbox.
5. **Finish** with a short summary: what was created, which sources are weak or stale, and the estimated monthly TinyFish cost (about $0.005 per monitor run).

Guardrails: public information only; never log in, apply, or message third parties.
