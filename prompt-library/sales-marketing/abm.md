# Create an account-based marketing plan

**Source:** [D&B AI — Sales & Marketing: ABM](https://dnb.ai/use-cases/sales-marketing/abm.html)

## What this workflow does

Build **ABM intelligence** for a target account: marketing responsiveness (prospector score, DHQ, marketing risk), key principals, recent news, industry context, and actionable ABM recommendations (multi-threading, messaging hooks, family-tree cross-sell).

**Tools involved:** **MatchTool** → **SalesAndMarketingTool** (Sales & Marketing Insights).

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Generic campaigns sent to the wrong accounts | Scores propensity and data quality before spend |
| Marketing unsure who to engage at the account | Surfaces senior principals and suggested roles to target |
| Missing “why now” for messaging | Ties recent news and sector trends to outreach themes |
| Single-site ABM ignoring corporate structure | Recommends mapping the family tree for expansion |

Enterprise access adds company financials, revenue, and deal-sizing context.

## Workflow steps

### 1. Resolve the company

**Prompt:**

> Find the DUNS number for Meridian Precision Components, Inc. in the United States.

**Tool:** MatchTool — Company Entity Resolution

### 2. Pull ABM intelligence

**Prompt:**

> Create an account-based marketing plan for Meridian Precision Components, Inc. I want to know their marketing responsiveness scores and whether they are a decision headquarters.

**Tool:** SalesAndMarketingTool — Sales & Marketing Insights

**Expected outcome:** Marketing scores, principals/contacts, news items, ABM recommendation (multi-thread, lead messages, family cross-sell).

---

*Illustrative example on [dnb.ai](https://dnb.ai/use-cases/sales-marketing/abm.html) uses synthetic company data.*

## Related guides

- [Expand sales across a corporate family](./corporate-family-expansion.md)
- [Identify market segmentation and create personalized campaigns](./segmentation-campaigns.md)
