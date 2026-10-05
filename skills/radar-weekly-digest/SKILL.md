---
name: radar-weekly-digest
description: Runs the weekly Medtech Opportunity Radar digest. Reads TinyFish monitor runs, finds new opportunities, scores them against the user's rubric in Notion, logs them, and reports deadlines and stale sources.
---

# Weekly digest

Find the Notion page "Medtech Opportunity Radar" and the databases Sources, Opportunities and Briefs by searching for them by name. Use their data source IDs in the steps below.

You run the weekly digest for the user's Medtech Opportunity Radar. Work only with public job and opportunity information. Never log in to any portal, never apply for anything, never send messages to third parties.

Context: the user's profile and criteria are on the Notion page. Their criteria and scoring rubric are on the Notion page 'Medtech Opportunity Radar'. Read it first and follow it.

Notion data sources: Sources (columns Name, Page URL, Tier, Monitor type, Monitor ID, Status, Last checked, Notes). Opportunities (columns Title, Organisation, Type, Deadline, Link, Fit score, Why it matches, Status, Found on, Source).

Steps:
1. Query the Sources database for rows with a Monitor ID. For each, use the TinyFish tools (list_monitors, get_monitor, get_run) to read the latest run of that monitor. If a monitor has no run in the last 8 days, or the last run failed or returned near-empty content, set that source's Status to Stale and list it in the digest under Needs attention. Otherwise keep Status Active and set Last checked to today.
2. For each active monitor, work out what is NEW compared with the previous run: new postings, new programmes, opened or changed application windows, new deadlines, new events. Ignore layout, navigation, footer, cookie and marketing changes. For search monitors, only consider results listed as new. Ignore results from linkedin.com, indeed.com and jooble.org.
3. For each genuinely new item, fetch the item's own page with the TinyFish fetch tool if more detail is needed (deadline, eligibility, dates, pay, location). Do not guess a deadline: leave it empty if unknown.
4. Before adding anything, query Opportunities and skip anything already there (match on Link or Title plus Organisation). If an existing row's details have changed, update that row instead of creating a new one, and set its Status back to New.
5. Score each item 0 to 5 against the rubric. Add it to Opportunities with Status New, Found on today's date, a one-sentence Why it matches that states any eligibility catch (work rights, year of study, location, unpaid) plainly, and Source set to the monitor name. Only items scoring 3 or more go in the digest; still log lower scores with Status Ignored so they are not re-evaluated next week.
6. Mark any Opportunities row whose Deadline has passed as Closed.

Final message (this is what the user reads, keep it under 250 words, plain language): 1) New this week: for each item scoring 3 or more, one line with organisation, type, deadline or 'no date yet', and the eligibility catch. 2) Deadlines in the next 21 days, from the whole Opportunities database. 3) Needs attention: stale sources or anything uncertain. If nothing new, say so in one line and still report deadlines and needs attention. Do not invent opportunities, deadlines or eligibility details; say 'unclear' when a page does not say.
