---
name: globaldata-deal-activity
description: >-
  Scan M&A, licensing, partnership and joint-venture activity in a sector or geography with
  GlobalData, and turn it into counts and values you can defend.
api: GlobalData Intelligence Center MCP
generated: '2026-09-13'
method: generated
source: https://mcp.globaldata.com/ (Sections 06, 09, 10)
grounding: >-
  Tool names, resolvable dimensions and the call sequences come from GlobalData's own domain
  explorer and worked examples. Nothing here was invented.
operations:
  - resolve_entities
  - list_deals
  - get_deals_descriptions
  - get_deals_analytics
---

# Scan deal activity with GlobalData

## 1. Resolve the deal dimensions

The deals domain has its own resolvable fields on top of the common ones:

- Common: `industry`, `location`, `theme`, `hqCountry`
- Deals-specific: `dealType`, `dealStatus`, `dealRationale`, `acquirerCountry`

`dealType`, `dealStatus` and `dealRationale` are fixed-choice fields. They take a different
resolution path from open taxonomy: an exact or alias match first, then an AI-assisted fuzzy match
as a second attempt before the value is dropped. A near-miss like "Geographical Expansion" resolves
to "Geographic Expansion" rather than failing silently — but check the `resolved` block for how the
match was made before you trust it.

## 2. List the deals

```
resolve_entities(dealType="Acquisition", location="Asia-Pacific", industry="Technology")
list_deals(dealType=<resolved>, location=<resolved>, industry=<resolved>, from_date="2026-01-01")
```

Returns deal summaries with parties, deal value and deal date.

For a single company's deal history, skip the resolution step — `companyName` is free text:

```
list_deals(companyName="X")
```

## 3. Get the full record

```
get_deals_descriptions(deal_ids=[...])
```

Full profiles: description, all parties, disclosed financials, rationale, advisors, investors, and
related company and industry tags.

## 4. Aggregate

```
reveal_advanced('deals')
get_deals_analytics(groupBy="dealType", from_date=..., to_date=...)
```

Groups by deal type, status, year, geography, industry or theme. Returns counts and total or
average deal values.

## Caveats GlobalData states itself

- **Deal value may be undisclosed.** An average value is computed over the deals that disclosed
  one, so a total is a floor, not a sum of all activity.
- **Some deals are announced after close**, so a date-bounded window is a window on announcement,
  not on completion.
- Sources are company announcements, press releases, financial news and transaction databases.

## Do not quote a count you have not scoped

`list_deals` needs at least one selective filter — a bare geography is rejected pre-flight with
`result_set_too_large`, and the error lists the filters that would make the query valid. Once it
runs, read `applied_filters` before quoting anything. Report the scope alongside the number.
