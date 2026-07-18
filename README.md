# Aire Baths — Financial & Workforce Dashboard

A single-file, self-contained dashboard covering P&L, recruiting, retention,
staffing, customer, membership, and marketing analytics for Aire Baths, a
10-location spa business built on Snowflake.

**Live version:** https://claude.ai/code/artifact/888dea37-bb9c-4b91-b611-09dbfd572656

## What's in it

Three tabs, each with its own KPI strip and charts:

| Tab | Covers |
|---|---|
| **Financial** | Revenue vs. profit trend, cost composition, profit by location, cost optimization (overtime, revenue/employee), an interactive Room Investment ROI calculator, refund analysis with a location/service drill-down |
| **Workforce** | Executive summary & staffing alerts, recruiting funnel, turnover by role, recruiting ROI / GEM spend, demand-vs-staffing trends by city, a retention heatmap (role × city), labor cost trends, 3-month hiring forecast, new-location staffing model |
| **Customer & Growth** | Customer tiers & churn risk, high-value at-risk customers, membership analytics, marketing campaign ROI |

Every number is a real result from a live query against
`AIRE_LOCAL_DB.WORKFORCE_ANALYTICS` in Snowflake — nothing here is placeholder
data. See [CLAUDE.md](./CLAUDE.md) for the full data-source breakdown, known
data quirks, and the reference Cortex Analyst agent config this dashboard was
built to complement.

## Running it locally

No build step, no dependencies — it's plain HTML/CSS/vanilla JS with inline
SVG charts.

```bash
python3 -m http.server
```

Then open `http://localhost:8000/dashboard.html`.

Or just double-click `dashboard.html` to open it directly in a browser (the
interactive Room ROI calculator and tab navigation both work from a local
file, no server required).

## Project files

- `dashboard.html` — the entire dashboard (markup, styles, data, and charting
  code in one file)
- `CLAUDE.md` — project documentation for working on this repo with Claude
  Code: data source, dashboard structure, known data quirks, and the
  `AIRE_WORKFORCE_AGENT` Cortex Analyst configuration this project mirrors
