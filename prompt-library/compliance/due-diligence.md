# Run a full due diligence check

**Source:** [D&B AI — Compliance: Due diligence](https://dnb.ai/use-cases/compliance/due-diligence.html)

## What this workflow does

You run a **consolidated due diligence pass** on a single entity: firmographics and corporate structure, financial risk indicators, and sales & marketing signals—in one conversational workflow instead of three separate research tracks.

**Tools involved:** **MatchTool** → **DataTool** (Company Information) + **RiskTool** (Financial Strength Insights) + **SalesAndMarketingTool** (Sales & Marketing Insights).

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| KYC, vendor diligence, or M&A screening that needs multiple data domains | One prompt aggregates identity, risk, and commercial engagement indicators |
| Analysts switching between systems for firmographics, credit, and propensity data | MCP tools return a unified narrative suitable for gateway or committee review |
| Missing corporate hierarchy or ultimate-parent context | Response can include global/domestic ultimate and family-tree scope |
| Sales-led deals that still require compliance sign-off | Balances low financial risk with prospector score, data quality (DHQ), and marketing risk |

Enterprise access can add PAYDEX, payment experiences, beneficial ownership, full financial statements, and board composition (per the D&B guide).

## Workflow steps

### 1. Resolve the company

**Prompt:**

> Find the DUNS number for Meridian Precision Components, Inc. in the United States.

**Tool:** MatchTool — Company Entity Resolution

**Expected outcome:** DUNS, primary name, address, and high match confidence.

### 2. Pull firmographic, financial, and sales & marketing data

**Prompt:**

> Run a full due diligence check on Meridian Precision Components, Inc. I need firmographic data, financial risk indicators, and sales and marketing insights all in one view.

**Tools:** DataTool, RiskTool, SalesAndMarketingTool

**Expected outcome (illustrative structure):**

- **Firmographic profile:** Name, address, phone, website, employees, SIC/NAICS, start year, legal form, operating status  
- **Financial risk indicators:** Overall business risk, D&B rating, financial strength, SER, SSI  
- **Sales & marketing indicators:** Prospector score, DHQ, marketing risk  
- **Corporate hierarchy:** Global ultimate, domestic ultimate, family-tree size  
- **Summary:** Pass/fail style gateway narrative for standard due diligence

---

*The Meridian Precision example on [dnb.ai](https://dnb.ai/use-cases/compliance/due-diligence.html) is illustrative and uses synthetic company data.*

## Related compliance guides

- [Assess financial risk for a company](./risk-assessment.md)
- [Track risk trends over time](./historical-risk.md)
