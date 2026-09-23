# Case Study: NovaTech Revenue Intelligence

## Objective

The project scenario placed me in the role of a Business Intelligence Analyst supporting NovaTech Solutions, a B2B SaaS company whose revenue operations data was fragmented across CRM, marketing, and support systems.

The business objective was to replace manual, disconnected reporting with a unified revenue intelligence experience that could answer five classes of questions:

1. How effectively does marketing activity convert into downstream sales outcomes?
2. What is the current state of the sales pipeline?
3. Which accounts show elevated customer-support risk?
4. Can business users ask natural-language questions across domains without manually joining data?
5. Can the organization distinguish AI-generated exploration from governed executive metrics?

## Design approach

I deliberately separated the solution into two analytical paths.

### 1. Governed dashboard path

A unified analytical dataset supports the executive dashboard. The dashboard contains three pages:

- **Marketing Funnel** for lead volume, funnel progression, channel conversion, spend, attributed revenue, and ROI.
- **Sales Pipeline** for current opportunity count, Won deals, win rate, Won revenue, deal size, cycle time, regional performance, and sales-rep performance.
- **Customer Health** for support volume, priority, resolution time, sentiment, product-area trends, and at-risk accounts.

The dashboard is the governed reference for standardized metrics because its calculations, filters, and record-selection rules are explicit and repeatable.

### 2. Natural-language AI path

A multi-dataset Topic connects the CRM, Marketing, and Support datasets through `account_id`. Instead of flattening all three datasets into a single semantic table, the Topic preserves native dataset grain and adds:

- dataset descriptions;
- field descriptions and synonyms;
- usage guidance and aggregation notes;
- explicit dataset relationships;
- cross-dataset custom instructions.

This gives the chat experience enough business context to answer cross-domain questions while keeping the logic inspectable.

## Data quality and preparation

Before building visuals, I verified source counts and categorical values across all three domains. The project used:

- **Marketing Campaigns:** 2,240 records
- **Support Tickets:** 3,000 records
- **CRM Deals:** 499 raw records representing 496 distinct sales opportunities in the governed current-opportunity view

All project datasets were stored in SPICE for responsive analysis.

Data preparation included type corrections, calculated business measures, and cross-source joins for the unified dashboard model. The semantic layer was then enriched independently so AI analysis could understand the meaning—not just the names—of fields.

## Dashboard design decisions

### Marketing Funnel

The Marketing page emphasizes conversion and campaign efficiency. The headline result is **412 Closed Won leads out of 2,240 total leads, or 18.39%**. This creates a baseline against which channel, campaign, and segment performance can be compared.

### Sales Pipeline

The Sales page is built around current opportunity performance, not raw CRM row counts. Governed metrics are:

- **496** current opportunities
- **314** Won opportunities
- **63.3%** win rate
- **$707,151** Won revenue
- **67.1 days** average time to close

A critical design rule is that historical versions of an opportunity should not inflate current sales metrics. The dashboard therefore uses the latest opportunity record and counts opportunities distinctly.

### Customer Health

The Customer Health page brings commercial value and service burden together. In the scenario data, **ACCT-041 — YieldMax Software** had **334 support tickets** and **$40,722 in Won revenue**. That combination makes it useful as an account-level review example because the service burden is meaningful in a revenue context.

## Semantic-layer design

The Topic uses three operational datasets:

- CRM Deals
- Marketing Campaigns
- Support Tickets

CRM is related to Marketing and Support through `account_id`.

I added business semantics to reduce ambiguity. Examples include:

- `account_id` = customer account identifier
- `deal_stage` = CRM sales pipeline stage
- `funnel_stage` = marketing funnel stage, not CRM pipeline stage
- `campaign_roi_pct` = prepared marketing ROI measure
- `resolution_time_hours` = support resolution duration, normally aggregated using averages for comparison
- `customer_sentiment` = support-experience sentiment, not proof of churn or revenue loss by itself

Topic-level instructions also defined which dataset should be primary for specific business questions and warned against fan-out when combining multiple account-level records across domains.

## AI validation and governance lesson

The project did not treat AI-generated answers as automatically correct. I reran the same questions before and after Topic configuration and verified the results against the dashboard.

Two questions matched exactly:

- Marketing conversion: **412 / 2,240 = 18.39%**
- Cross-domain support + CRM: **ACCT-041, 334 tickets, $40,722 Won revenue**

The Sales question exposed the more important lesson. The initial Topic answer counted **499 CRM rows**. After semantic enrichment, the Topic correctly shifted to **496 distinct opportunities**, proving that business semantics changed the generated query. However, it still did not reproduce the dashboard's latest-opportunity-record filter, so the final AI answer remained slightly different from the governed dashboard.

This was not treated as a failure to hide. It became the core governance finding: **generative BI can accelerate exploration, but metric grain and record-selection rules still need governed definitions and human verification.**

## Outcome

The completed solution demonstrates an end-to-end BI pattern that combines:

- source verification;
- no-code data preparation;
- multi-page executive dashboarding;
- semantic-layer design;
- natural-language analytics;
- AI explanation and validation;
- executive communication.

The final operating model is simple: use the dashboard for standardized executive reporting, use natural-language chat for rapid exploration, and validate AI results whenever the answer depends on record grain, filtering, or cross-domain business logic.
