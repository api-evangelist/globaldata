---
name: globaldata-news-sentiment
description: >-
  Track news volume and sentiment for a company, industry or theme with GlobalData, and report it
  with the caveats the data actually carries.
api: GlobalData Intelligence Center MCP
generated: '2026-09-13'
method: generated
source: https://mcp.globaldata.com/ (Sections 06, 09, 10)
grounding: >-
  Tool names, resolvable dimensions and sequences are transcribed from GlobalData's published
  news-domain reference and worked examples. Nothing here was invented.
operations:
  - resolve_entities
  - list_newsarticles
  - get_newsarticle_descriptions
  - get_news_analytics
  - list_socialmediaposts
---

# Track news sentiment with GlobalData

## 1. Resolve the news dimensions

The news domain adds two resolvable fields to the common set: `newsCategory` and `sentiment`
(Positive / Negative / Neutral). Both are fixed-choice, so they resolve by exact or alias match
first and an AI-assisted fuzzy match second.

```
resolve_entities(sentiment="Negative", industry="Renewable Energy")
```

## 2. List articles

```
discover_capabilities('news')
list_newsarticles(companyName="X", sentiment=<resolved>, from_date=..., to_date=...)
```

Filters by company, industry, theme, news category, sentiment, location and date range. Returns
article summaries with sentiment scores and tagged entities.

## 3. Full article

```
get_newsarticle_descriptions(article_ids=[...])
```

Headline, body text, publication date, source, sentiment score, and the companies, industries and
themes mentioned.

## 4. The trend is the point

```
reveal_advanced('news')
get_news_analytics(groupBy="month", industry=<resolved>, from_date=..., to_date=...)
```

Aggregates volume and sentiment over time by company, industry, month or theme — which is what
surfaces coverage trends and sentiment shifts. A single month's sentiment average, on its own,
tells you very little.

## 5. Social, if the question is reach rather than record

```
discover_capabilities('social')
list_socialmediaposts(companyName="X", sentiment=<resolved>, from_date=...)
```

Posts mentioning tracked companies, industries or themes, with sentiment scores and entity tags.
GlobalData notes that platform coverage and recency depend on its licensing agreements, so treat
absence as unknown rather than as silence.

## The caveat you must carry into the report

GlobalData states plainly that **the sentiment score is model-derived — treat it as a signal, not a
definitive label.** Report it as a modelled score, name the window, and name the scope you read out
of `applied_filters`. Sentiment numbers travel badly once separated from all three.

News coverage comes from licensed news feeds, press releases, trade publications and analyst
commentary, curated to GlobalData-tracked companies, industries and themes — not the open web.
