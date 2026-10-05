# Notion schema

Create these three databases as children of the "Medtech Opportunity Radar" page.
Statements are in the Notion MCP `create-database` DDL syntax.

## Sources (what is being watched, and whether it still works)

```
CREATE TABLE ("Name" TITLE, "Page URL" URL,
  "Tier" SELECT('1 UCL and NHS':green, '2 Companies':blue, '3 Aggregators':gray, '4 Competitions':purple),
  "Monitor type" SELECT('fetch':blue, 'search':orange),
  "Monitor ID" RICH_TEXT,
  "Status" SELECT('Active':green, 'Stale':red, 'Paused':gray, 'Untested':yellow),
  "Last checked" DATE, "Notes" RICH_TEXT)
```

## Opportunities (scored findings)

```
CREATE TABLE ("Title" TITLE, "Organisation" RICH_TEXT,
  "Type" SELECT('Summer placement':blue, 'Industrial year':green, 'Research studentship':purple,
                'Graduate scheme':orange, 'Competition':pink, 'Hackathon':yellow, 'Other':gray),
  "Deadline" DATE, "Link" URL, "Fit score" NUMBER, "Why it matches" RICH_TEXT,
  "Status" SELECT('New':blue, 'Reviewing':yellow, 'Applied':green, 'Ignored':gray, 'Closed':red),
  "Found on" DATE, "Source" RICH_TEXT)
```

## Briefs (company briefs)

```
CREATE TABLE ("Company" TITLE, "Created" DATE,
  "Purpose" SELECT('Application':blue, 'Interview':green, 'Exploring':gray),
  "Website" URL)
```

## Brief-request columns (added to Opportunities after Briefs exists)

```
ADD COLUMN "Prepare brief" CHECKBOX COMMENT 'Tick this to request a company brief. The brief agent picks it up within a few hours.';
ADD COLUMN "Brief status" SELECT('Not requested':gray, 'Requested':yellow, 'Ready':green, 'Failed':red);
ADD COLUMN "Brief" RELATION('<Briefs data source UUID, bare, without the collection:// prefix>')
```

Gotcha: the RELATION target must be a bare UUID. `collection://...` is rejected.
