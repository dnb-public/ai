# Get a full financial profile

**Source:** [D&B AI — Finance: Financial profile](https://dnb.ai/use-cases/finance/financial-profile.html)

## What this workflow does

After entity resolution, you pull a **full financial profile** for a company: firmographic context, financial strength and D&B rating, employee and industry data, and—where entitled—historical financial statements and year-over-year comparison.

**Tools involved:** **MatchTool** (Company Entity Resolution) → **DataTool** (Company Information, Global Financials).

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Analysts assembling financial snapshots from multiple sources | One prompt chain returns a consolidated D&B-backed profile tied to a verified DUNS |
| Credit, FP&A, or strategy teams needing size and strength bands quickly | D&B rating, financial strength class, and risk indicator in one response |
| Due diligence before deeper modeling | Baseline employees, industry codes, legal form, and operating status before pulling enterprise statements |
| Historical comparison for trend conversations | Guide positions historical statements and YoY views as enterprise extensions of the same flow |

Enterprise access adds full income statement, balance sheet, cash flow history, revenue trends, PAYDEX, and credit limit recommendations.

## Workflow steps

### 1. Resolve the company

**Prompt:**

> Find the DUNS number for Meridian Precision Components, Inc. in the United States.

**Tool:** MatchTool — Company Entity Resolution

**Expected outcome:** DUNS, primary name, address, high match confidence.

### 2. Pull the full financial profile

**Prompt:**

> Get me the full financial profile for Meridian Precision Components, Inc., including historical financial statements.

**Tool:** DataTool — Company Information, Global Financials

**Expected outcome:** Employee count, industry (SIC/NAICS), start year, legal form, status, D&B rating, financial strength class, risk indicator, and a short summary narrative.

---

*The Meridian Precision example on [dnb.ai](https://dnb.ai/use-cases/finance/financial-profile.html) is illustrative and uses synthetic company data.*

## Related finance guides

- [Evaluate a potential partnership](./partnership-evaluation.md)
- [Assess credit risk for a company](./credit-risk.md)
