# Identify market segmentation and create personalized campaigns

**Source:** [D&B AI — Sales & Marketing: Segmentation & campaigns](https://dnb.ai/use-cases/sales-marketing/segmentation-campaigns.html)

## What this workflow does

End-to-end **marketing operations**: enrich CRM/prospect records with D&B firmographics and hierarchy, define ICP and personas from the enriched base, rank target accounts, and generate persona- and stage-aligned campaign plans including ABM plays for high-value accounts.

**Tools involved:** **MatchTool**, **DataTool**, **OwnershipTool**, **SalesAndMarketingTool**, **ContactTool** (Entity Resolution, Company Information, Global/Company Financials, Hierarchies & Connections, Sales & Marketing Insights).

**Prerequisite (from D&B):** Access to CRM and marketing datasets plus connected activation for segmentation and campaigns.

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Segmentation on stale or blank CRM fields | Side-by-side CRM vs. D&B enrichment (D-U-N-S, SIC/NAICS, parent, GU) |
| ICP definitions disconnected from real wins | Benchmarks ICP attributes on a reference customer profile |
| Marketing lists without prioritization | Ranks accounts by revenue potential, acquisition ease, strategic fit |
| One-size-fits-all messaging | Campaign plan by persona pain points, channels, and ABM tactics |

Enterprise access adds growth indicators, Prospector scores, Buying Index, DHQ, intent, and analytics.

## Workflow steps

### 1. Enrich customer and prospect base

**Prompt:**

> Gather all available customer and prospect data from CRM and marketing systems. Enrich the dataset with D&B firmographics including company size, industry classification, revenue, and location. Additionally, append corporate hierarchy details such as parent company and subsidiaries.

**Tools:** MatchTool, DataTool, OwnershipTool

**Expected outcome:** Enrichment table showing corrected/appended fields and hierarchy readiness for segmentation.

### 2. Analysis & segmentation

**Prompt:**

> Analyze the enriched dataset to define the Ideal Customer Profile (ICP) using key attributes such as decision headquarter, spend capacity, and growth trajectory. Group accounts into segments and create personas.

**Tools:** DataTool, SalesAndMarketingTool

**Expected outcome:** ICP criteria, segment label, persona narrative, and pain points.

### 3. Select target accounts

**Prompt:**

> Apply the ICP and segmentation criteria to filter eligible accounts. Rank by revenue potential, ease of acquisition, and strategic fit. Generate a prioritized account list.

**Tools:** DataTool, SalesAndMarketingTool

**Expected outcome:** Ranked target list with prospector score and strategic-fit rationale.

### 4. Build personalized campaigns

**Prompt:**

> Create campaign messaging tailored to each persona's pain points and buying stage. Align with preferred channels and content formats. For high-value accounts, implement ABM strategies.

**Tools:** DataTool, SalesAndMarketingTool, ContactTool

**Expected outcome:** Messaging themes, channel mix, and numbered ABM plays (email, social, executive, cross-sell via parent).

---

*Illustrative example on [dnb.ai](https://dnb.ai/use-cases/sales-marketing/segmentation-campaigns.html) uses synthetic company data.*

## Related guides

- [Create an account-based marketing plan](./abm.md)
- [Look up a company overview](./company-overview.md)
