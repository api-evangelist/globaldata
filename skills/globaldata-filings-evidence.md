---
name: globaldata-filings-evidence
description: >-
  Pull evidence out of regulatory filings with GlobalData — find the filing, then extract the
  specific passages (risk factors, ESG disclosures, forward-looking statements) without reading the
  whole document.
api: GlobalData Intelligence Center MCP
generated: '2026-09-13'
method: generated
source: https://mcp.globaldata.com/ (Sections 06, 09, 10)
grounding: >-
  Tool names and sequences are transcribed from GlobalData's published filings-domain reference and
  worked examples. Nothing here was invented.
operations:
  - list_filing
  - get_filingsentences
  - get_filing_document
  - get_filing_document_text
  - get_filings_analytics
---

# Extract evidence from filings with GlobalData

This domain is built for the "find the sentence, cite the source" pattern. Use the sentence-level
tool before reaching for full text — it is the difference between a citation and a download.

## 1. Find the filing

```
discover_capabilities('filings')
list_filing(companyName="X", filingType="Annual Report")
```

Filters by company, filing type, sector, country or date. Covers annual and quarterly reports,
earnings call transcripts and investor presentations.

Extract the `Filing_ID`.

## 2. Extract passages, not documents

```
get_filingsentences(filing_id=<Filing_ID>, keyword="supply chain risk")
```

Returns specific sentences or passages matching a keyword or topic. This is the right tool for ESG
disclosures, risk factors and forward-looking statements — you get quotable text scoped to the
question instead of a document to wade through.

## 3. Navigate structure when you need context

```
get_filing_document(filing_id=<Filing_ID>)
```

Returns the filing broken into labelled sections, so you can jump to the part you need.

## 4. Full text, only when you actually need it

```
get_filing_document_text(filing_id=<Filing_ID>)
```

Documents live in cloud storage and this returns a **time-limited access URL**, not inline text.
Fetch it promptly, and do not persist the URL as if it were stable.

## 5. Aggregate filing activity

```
reveal_advanced('filings')
get_filings_analytics(groupBy="filingType", ...)
```

Aggregates filing volume and sentiment by filing type, sector, country or year.

## Sourcing note

Coverage comes from regulatory filing portals, stock exchange disclosures and company investor
relations pages. Sentiment on this domain — as on news — is model-derived. Treat it as a signal,
not a definitive label, and say so when you report it.
