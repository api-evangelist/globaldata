---
name: globaldata-company-profile
description: >-
  Build a full profile of a company from GlobalData — identify it, pull its financials and
  structure, then chain outward into its deals, news, filings, jobs and patents on the Company ID.
api: GlobalData Intelligence Center MCP
generated: '2026-09-13'
method: generated
source: https://mcp.globaldata.com/ (Sections 02, 06, 07, 09)
grounding: >-
  Tool names and the call sequences are transcribed from GlobalData's own domain explorer and
  worked-examples sections. No tool or parameter here was invented.
operations:
  - resolve_entities
  - list_companies
  - get_company_descriptions
  - get_company_analytics
  - list_deals
  - list_newsarticles
  - list_filing
  - list_jobs
  - list_patents
---

# Profile a company with GlobalData

`company_id` is the primary join key across every content domain. Get it once, then reuse it.

## 1. Resolve anything that is taxonomy-valued

Call `resolve_entities` for `industry`, `location`, `hqCountry` and `theme`. Do **not** resolve
`companyName` or `keyword` — those are free-text and pass through unchanged.

Read the confidence score. Anything below **0.65** is dropped and reported in `dropped_filters`.

## 2. Find the company

```
list_companies(companyName="Siemens")
```

Extract `company_id` from the result. A second identifier, `cdms_company_id`, is returned
alongside it — ignore it unless a specific tool asks for it by name.

`list_companies` also filters by industry, company type, headquarters country, revenue range and
keyword, so use it for cohort work too ("all technology companies headquartered in the US" is
`resolve_entities` for `hqCountry` and `industry`, then `list_companies` with both).

## 3. Pull the profile

```
get_company_descriptions(company_ids=[<company_id>])
```

Returns overview text, financials (revenue, net income, EBITDA, employee count), trading
information, parent/subsidiary relationships, sector classification and contact details.

## 4. Chain outward on the Company ID

Pass `company_ids=[<company_id>]` into the other domains. Each needs its domain revealed first
(`discover_capabilities('deals')`, `'news'`, `'filings'`, `'jobs'`, `'patents'`), or call `search`
with the domain and keywords and let the facade route.

| You want | Call |
|---|---|
| M&A, licensing, partnerships | `list_deals` |
| Curated news with sentiment | `list_newsarticles` |
| Annual reports, transcripts, presentations | `list_filing` |
| Job postings with normalised salaries | `list_jobs` |
| Patents by assignee | `list_patents` |

## 5. Market-level rollups

For cohort analytics rather than one company, `reveal_advanced('companies')` then:

```
get_company_analytics(groupBy="industry", industry=<resolved>)
```

Groups by industry, country, revenue band or financial year and returns counts plus metric
summaries — revenue, net income, market cap, employee count, growth rates.

## Before you report a number

Read `applied_filters`. It is the true scope after canonicalisation, and it is the only thing that
tells you whether your filters survived. Check `dropped_filters`, `warnings` and `scope_warning`
too. A wrong resolution silently rescopes everything downstream, and the count will look perfectly
reasonable while being about the wrong universe.

Present resolution first (terms, canonical values, confidence, anything dropped), then the data.
