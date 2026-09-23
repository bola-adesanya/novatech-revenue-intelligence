# AI Validation and Semantic-Layer Governance

## Why validation mattered

Natural-language BI can produce an answer that sounds reasonable while still using the wrong business grain, aggregation, or filter. This project therefore treated AI responses as **claims to verify**, not as automatically governed metrics.

The validation method used three controls:

1. ask the same question before and after Topic configuration;
2. compare the result with the published dashboard;
3. inspect Amazon Quick's Explanation output when results differed.

## Test 1 — Marketing conversion

**Question:** How many leads reached Closed Won, and what percentage of all leads is that?

| Source | Result |
|---|---|
| Governed dashboard | 412 Closed Won / 2,240 total = **18.39%** |
| Topic result | 412 Closed Won / 2,240 total = **18.39%** |

**Outcome:** Exact agreement.

The semantic layer preserved the meaning of Closed Won and the marketing population correctly.

## Test 2 — Sales performance

**Question:** Across all sales deals, what is the win rate, Won revenue, and average time to close?

### Governed dashboard

- **496** current opportunities
- **314** Won opportunities
- **63.3%** win rate
- **$707,151** Won revenue
- **67.1 days** average time to close

### Initial Topic answer

- **499** CRM rows
- **315** Won rows
- **63.13%** win rate
- **$707,201** Won revenue
- **66.76 days** average time to close

### After semantic enrichment

The Topic was updated to understand that `opportunity_id` is the deal grain and that deal counts should use distinct opportunities rather than CRM row counts.

The revised answer moved to:

- **496 distinct opportunities**
- **315** Won opportunities
- **63.51%** win rate
- **$707,201** Won revenue
- **66.8 days** average time to close

### Root cause of the remaining difference

The dashboard applies a **latest-opportunity-record** rule before calculating current pipeline metrics. The analysis determines the latest close date for each `opportunity_id`, flags the matching record as the current version, and filters the Sales Pipeline sheet to those latest records.

The Topic query corrected the opportunity grain but did not reproduce that analysis-level latest-record filter. That explains the remaining one-opportunity, $50 revenue, and small close-time differences.

**Outcome:** Partial disagreement with a documented root cause.

### Governance implication

This is the most important result in the project. The AI answer became materially better after semantic enrichment, proving that metadata and instructions influence generated analysis. But semantic guidance did not automatically recreate every governed analysis-level rule.

For executive use, the dashboard remains authoritative for standardized sales metrics unless the latest-record logic is promoted into a reusable governed data layer that both the dashboard and AI query path consume.

## Test 3 — Cross-domain customer health

**Question:** Which account has the most support tickets, and what Won revenue is associated with that account?

| Source | Result |
|---|---|
| Governed dashboard | **ACCT-041**, 334 tickets, **$40,722** Won revenue |
| Topic result | **ACCT-041**, 334 tickets, **$40,722** Won revenue |

**Outcome:** Exact agreement.

This question required reasoning across Support Tickets and CRM Deals through the shared `account_id` relationship, so the match provided a direct test of the cross-dataset semantic model.

## Responsible AI takeaway

The project reinforced four practical principles for generative BI:

- **Semantic metadata is executable governance.** Field descriptions, synonyms, and instructions affect generated queries.
- **Record grain must be explicit.** “Deal” cannot be allowed to mean “CRM row” when historical row versions exist.
- **Explanations matter.** Inspecting assumptions, filters, calculations, and generated query logic is essential when numbers differ.
- **Agreement is not the only useful outcome.** A well-understood disagreement can expose hidden business rules that should be moved into a governed shared layer.

Amazon Quick's Explanation feature is particularly useful because numerical answers can expose their sources, assumptions, filters, calculations, and generated SQL for verification.
