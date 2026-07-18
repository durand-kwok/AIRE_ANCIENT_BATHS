# Aire Baths — Financial & Workforce Dashboard

Single-file HTML dashboard (`dashboard.html`) covering P&L, recruiting, retention,
customer, membership, and marketing analytics for Aire Baths, a 10-location spa
business. Self-contained (no external dependencies, no build step) — open it
directly in a browser. Also published as a Claude Artifact:
https://claude.ai/code/artifact/888dea37-bb9c-4b91-b611-09dbfd572656

## Data source

Built against the Snowflake schema `AIRE_LOCAL_DB.WORKFORCE_ANALYTICS`, queried
live via an MCP `query_data` tool during development (all figures in the
dashboard are real query results, not placeholders).

Note: the reference Streamlit apps and Cortex Analyst agent described below run
against `AIRE_BATHS_HR_DEMO.WORKFORCE_ANALYTICS` — a different database name in
the same Snowflake account. Treat these as separate environments with an
equivalent schema shape, not the same database, until confirmed otherwise.

Key tables/views used: `T_V_LOCATION_PNL`, `T_V_RECRUITING_FUNNEL`,
`T_V_RETENTION_ANALYSIS`, `T_V_DEMAND_VS_HEADCOUNT`, `T_V_HIRING_PREDICTIONS`,
`T_V_INVESTMENT_ROI`, `T_V_NEW_LOCATION_STAFFING_MODEL`, `T_V_CAMPAIGN_EFFECTIVENESS`,
`T_V_MEMBERSHIP_FUNNEL` / `T_V_MEMBERSHIP_LTV` / `T_V_MEMBERSHIP_RENEWALS`,
`CUSTOMER_TIERS_AI`, `SALESFORCE_BOOKINGS` (refunds), `GEM_RECRUITING_COSTS`,
`WORKDAY_HCM_EMPLOYEES`, `WORKDAY_RECRUITING_REQUISITIONS`, `LOCATION_MASTER`.

## Dashboard structure

Three tabs, each with its own KPI strip:

- **Financial** — revenue vs. profit trend, cost composition, profit by location,
  cost optimization (overtime, revenue/employee), an interactive Room Investment
  ROI calculator (location + build-cost inputs recompute client-side), refund
  analysis with a location/service drill-down.
- **Workforce** — executive summary, staffing alerts, recruiting funnel,
  turnover by role, recruiting ROI / GEM spend, demand-vs-staffing sparklines by
  city, a retention heatmap (role × city), labor cost trends, 3-month hiring
  forecast, new-location staffing model.
- **Customer & Growth** — customer tiers & churn risk, high-value at-risk
  customers, membership analytics, marketing campaign ROI.

Modeled on two existing internal Streamlit apps, `AIRE_FINANCIAL_DASHBOARD` and
`AIRE_WORKFORCE_DASHBOARD`, screenshotted by the user as the spec for what to
surface — every KPI shown in those screenshots was cross-checked against a live
query before being included.

## Known data quirks (verified against real query results, not bugs to "fix")

- `WORKDAY_HCM_EMPLOYEES` has active-status records for **Austin and Chicago
  only** — every other location shows zero active headcount, which is why
  headcount-by-location and revenue-per-employee panels only show two cities.
- `T_V_LOCATION_PNL` has $0 costs in the first (2024-05) and last (2026-05)
  months in the window — boundary/partial months, excluded from financial
  totals and trend charts.
- London is the only profitable location; company-wide net margin is ≈ -40%.
- Turnover is severe across every role (66–100% trailing 12-month rate);
  staffing is understaffed in 138 of 140 observed location-months.
- Customer churn data (`CUSTOMER_TIERS_AI`) has no rows with `HOME_LOCATION_ID`
  resolving to London — churn-by-city panels cover the other 9 cities only.

## Reference: `AIRE_WORKFORCE_AGENT` (Cortex Analyst agent config)

Snowflake Intelligence agent in `AIRE_BATHS_HR_DEMO.WORKFORCE_ANALYTICS`
(created 2026-05-12). Captured here so the dashboard's scope and tone can stay
in sync with what the agent already promises users.

### Response instructions (tone/formatting)

> You are the Aire Baths Workforce Intelligence Agent. Provide clear,
> data-driven insights about HR, recruiting, staffing, and workforce planning.
> Always use location names (cities) instead of IDs.
> Round numbers to 2 decimal places. Format currency with $ prefix.
> When showing recruiting funnels, present stages in order: Applied → Screened
> → Interviewed → Offered → Hired.
> When discussing staffing gaps, always relate demand (bookings) to available
> therapist headcount.
> For retention analysis, flag locations with turnover rates above 15% as high
> risk.

### Orchestration instructions (tool usage / topic coverage)

> You are a workforce analytics assistant for Aire Baths, a luxury spa company.
>
> Use the WORKFORCE_ANALYTICS tool for all data questions about:
> - Employee headcount, roles, compensation, tenure
> - Recruiting funnels, time-to-fill, cost-per-hire, source channels
> - Booking demand vs staffing levels and recruiting pipeline health
> - Labor costs and payroll analysis
> - Operational costs (rent, utilities, supplies, marketing)
> - Recruiting costs from GEM (job boards, agencies, sourcing tools)
> - Revenue, profitability, and P&L by location
> - Retention and turnover analysis
> - New location staffing recommendations
> - Customer profiles, tiers, lifetime value, and churn risk
> - Marketing campaigns, ROI, conversion rates, and channel performance
> - Customer 360 views and personalized AI recommendations
>
> Always provide actionable insights alongside raw data.

### Example questions (as configured on the agent)

Financial-flavored set:
1. How many customers repeated the experience in the last 6 months?
2. What is the refund rate breakdown by location?
3. If we build another massage room in London costing 200K, what is the payback period?
4. Which location has the highest revenue per treatment room?
5. What is the cost per booking by location and how has it trended?
6. Where are we overspending on labor relative to revenue generated?
7. What is the profit margin trend by location over the last 6 months?
8. If we reduce overtime by 20 percent across all locations, how much do we save?
9. Which location has the best ratio of revenue to total operating costs?
10. What is our customer acquisition cost by referral source?
11. How much revenue are we losing to refunds and which locations need intervention?
12. What is the revenue per employee by location and are we optimally staffed?
13. Denver has the highest refund rate at 2%. What services are driving refunds there?
14. Miami refund rate is 1.5%. Break down refunds by service type and show the cost impact.
15. London shows the best ROI per room. What is driving the higher revenue per room?
16. Labor cost exceeds revenue at Denver. What roles are driving the highest labor cost?

Workforce-flavored set:
1. Which locations are currently understaffed relative to booking demand?
2. Compare booking demand vs therapist headcount for Miami.
3. Where is booking demand growing fastest quarter over quarter?
4. Are there locations where bookings are growing but we have no open requisitions?
5. What is our average tenure by role?
6. Which locations have the highest turnover rate?
7. Is there a correlation between compensation band and turnover?
8. Show me retention risk by city.
9. What is the profit margin for each location?
10. Show me revenue vs total costs by location for the last quarter.
11. Which location is the most profitable?
12. What percentage of revenue goes to labor costs by city?
13. How much are we spending on recruiting per location from GEM?
14. Show the full P&L breakdown for New York.
15. What are our total operational costs by category?
16. How many staff do we need for the Portland opening?
17. What is the recommended hiring timeline for new locations?

> Note: items 3, 8, 11, 13, 14, 15, and 16 in the financial-flavored list, and
> several in the workforce-flavored list, were partially cropped in the source
> screenshots — wording above is transcribed as legibly as possible and may
> not be verbatim in every case. Re-check against the live agent config before
> treating this as authoritative.

## Working on this repo

- `dashboard.html` is the only source file — edit it directly, there's no
  build step. It's plain HTML/CSS/vanilla JS with inline SVG charts (no chart
  library, no CDN dependencies — required for the strict CSP the published
  Artifact runs under).
- To preview locally: `python3 -m http.server` from this directory, then open
  `http://localhost:8000/dashboard.html`.
- To republish after edits: use the Artifact tool with the same URL
  (`https://claude.ai/code/artifact/888dea37-bb9c-4b91-b611-09dbfd572656`) so
  the existing link keeps working instead of minting a new one.
