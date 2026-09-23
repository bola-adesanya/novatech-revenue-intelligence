# Metric Definitions

These definitions summarize the governed business logic used in the portfolio case study.

## Marketing

| Metric | Definition |
|---|---|
| Total Leads | Total marketing records represented in the funnel population |
| Closed Won Leads | Leads associated with the Closed Won funnel outcome |
| Lead-to-Win Conversion Rate | Closed Won leads divided by total leads |
| Campaign ROI | Prepared `campaign_roi_pct` measure used for marketing ROI questions |
| Attributed Revenue | Revenue attributed to marketing activity; not interchangeable with CRM realized Won revenue |

## Sales

| Metric | Definition |
|---|---|
| Total Deals | Distinct `opportunity_id` values after applying the current/latest opportunity rule |
| Won Deals | Distinct `opportunity_id` values where the current record has `deal_stage = 'Won'` |
| Win Rate | Won Deals divided by Total Deals |
| Won Revenue | Sum of `deal_value` for current records with `deal_stage = 'Won'` |
| Average Deal Size | Average deal value under the governed Sales Pipeline view |
| Average Days to Close | Average `days_to_close` for the governed current-opportunity population |

## Support

| Metric | Definition |
|---|---|
| Total Tickets | Support ticket record count |
| Critical Tickets | Tickets categorized as critical priority |
| Average Resolution Time | Average `resolution_time_hours`; not a sum of ticket durations |
| Negative Sentiment Tickets | Tickets with negative customer sentiment |
| Accounts with Tickets | Distinct customer accounts represented in Support Tickets |

## Cross-domain account analysis

Cross-domain questions use `account_id` to connect customer activity across Marketing, CRM, and Support.

When multiple records exist in more than one domain, measures should be aggregated within their source domain before cross-domain comparison when necessary to avoid fan-out or repeated values.
