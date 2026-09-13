---
name: globaldata-hiring-signal
description: >-
  Read hiring intent from GlobalData job-posting data — what a company or sector is staffing for,
  where, at what seniority and at what salary — and turn it into a trend rather than a snapshot.
api: GlobalData Intelligence Center MCP
generated: '2026-09-13'
method: generated
source: https://mcp.globaldata.com/ (Sections 06, 09, 10)
grounding: >-
  Tool names and sequences are transcribed from GlobalData's published jobs-domain reference and
  worked examples. Nothing here was invented.
operations:
  - resolve_entities
  - list_jobs
  - get_job_descriptions
  - get_jobs_analytics
  - get_jobs_analytics_multi_view
---

# Read hiring signal with GlobalData

Job postings are the fastest-moving domain GlobalData carries — it describes them as near
real-time, against roughly two business days for company financials and news. That makes this the
domain to use when the question is "what is happening now".

## 1. Resolve location and industry

```
resolve_entities(location="Germany", industry="Technology")
```

`companyName` and job title are free text and need no resolution.

## 2. List postings

```
discover_capabilities('jobs')
list_jobs(companyName="X", occupation="Engineering", location=<resolved>)
```

Filters by title, occupation category, seniority level, company, country, state or city, and
posting date. Results carry salary ranges in local currency **and** USD-normalised.

## 3. Full posting content

```
get_job_descriptions(job_ids=[...])
```

Returns description text, salary min and max, education requirements, occupation classification and
skills tags.

## 4. Trend, not snapshot

```
reveal_advanced('jobs')
get_jobs_analytics(groupBy="occupation", industry=<resolved>, from_date=..., to_date=...)
```

Groups by occupation, country, company, education type or date, returning counts and salary
analytics (min, max, median).

For a dashboard-shaped question that needs several cuts at once:

```
get_jobs_analytics_multi_view(...)
```

fetches multiple groupings (occupation + location + education, say) in a single call instead of
several round trips.

## Caveats GlobalData states itself

- Sources are job board aggregators and company career pages, covering GlobalData-tracked companies.
- **Historical postings may be deduplicated**, so a raw historical count is not a raw count of
  advertisements placed.
- **Not all postings include salary data.** Salary analytics need enough postings per bucket to be
  reliable — a median over a thin bucket is noise. Check the count before quoting the median.

## Pair it with buying signals

The `buying_signals` domain (renamed from `leads`) already folds hiring events in alongside deals,
regulatory events and news triggers, classified by supplier relevance. If the question is
commercial rather than analytical, `list_buying_signals` may get there in one call — but
GlobalData notes the classification is rule-based, so verify individually before acting on it.
