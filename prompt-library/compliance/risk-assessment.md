# Assess financial risk for a company

**Source:** [D&B AI — Compliance: Risk assessment](https://dnb.ai/use-cases/compliance/risk-assessment.html)

## What this workflow does

After you identify the correct legal entity, you pull its **current financial risk profile** from verified Dun & Bradstreet data. Typical outputs include failure and delinquency-related scores, D&B rating, financial strength classification, supplier evaluation risk (SER), supplier stability indicator (SSI), and an overall business-risk summary.

**Tools involved:** **MatchTool** (entity resolution) → **RiskTool** (Financial Strength Insights).

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Onboarding vendors, partners, or counterparties without a consistent risk read | One prompt chain yields a standardized risk snapshot tied to a verified DUNS |
| Manual lookups across rating agencies or internal spreadsheets | AI orchestrates match + risk in natural language |
| Ambiguous company names or duplicate legal entities | Match step resolves identity and match confidence before risk is retrieved |
| Compliance and credit teams needing auditable, repeatable checks | Same prompt pattern works for any entity once MCP credentials are in place |

Enterprise tiers can extend the same flow with failure score percentiles, delinquency trends, PAYDEX history, and credit limit recommendations (as noted on the D&B site).

## Workflow steps

### 1. Resolve the company

**Prompt:**

> Find the DUNS number for Meridian Precision Components, Inc. in the United States.

**Tool:** MatchTool — Company Entity Resolution

**Expected outcome:** Verified DUNS, primary name, address, match confidence, and match method (for example, name + address + registration number).

### 2. Pull the risk profile

**Prompt:**

> What is the current financial risk profile for Meridian Precision Components, Inc.? Include the failure score and delinquency score.

**Tool:** RiskTool — Financial Strength Insights

**Expected outcome:** Overall business risk, D&B rating, financial strength band, risk indicator, SER, SSI, and a short narrative summary.

---

*The Meridian Precision example on [dnb.ai](https://dnb.ai/use-cases/compliance/risk-assessment.html) is illustrative and uses synthetic company data.*

## Related compliance guides

- [Track risk trends over time](./historical-risk.md)
- [Run a full due diligence check](./due-diligence.md)
