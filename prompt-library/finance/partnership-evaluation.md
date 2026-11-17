# Evaluate a potential partnership

**Source:** [D&B AI — Finance: Partnership evaluation](https://dnb.ai/use-cases/finance/partnership-evaluation.html)

## What this workflow does

You assess **partnership viability** by combining financial risk indicators with sales and marketing signals, plus industry context, into a single synthesis—not just credit scores or propensity alone.

**Tools involved:** **MatchTool** → **RiskTool** (Financial Strength Insights) + **SalesAndMarketingTool** (Sales & Marketing Insights).

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| BD or corp dev approving partners on revenue story alone | Balances growth/propensity (prospector score, DHQ) against SER, SSI, and D&B rating |
| Risk teams blocking deals without commercial context | Same workflow surfaces both sides for a shared viability narrative |
| Inconsistent “go/no-go” for alliances, JVs, or channel partners | Repeatable prompt pattern after DUNS resolution |
| Need for plain-language recommendation | Guide includes viability synthesis (for example, low risk + growth mode + sector tailwinds) |

Enterprise access can add PAYDEX, full financials, beneficial ownership, and family-tree analysis for deeper partnership diligence.

## Workflow steps

### 1. Resolve the company

**Prompt:**

> Find the DUNS number for Meridian Precision Components, Inc. in the United States.

**Tool:** MatchTool — Company Entity Resolution

**Expected outcome:** DUNS, primary name, address, high match confidence.

### 2. Pull risk and sales and marketing data

**Prompt:**

> I'm evaluating a potential partnership with Meridian Precision Components, Inc. Give me both their risk profile and their sales and marketing insights so I can assess viability.

**Tools:** RiskTool, SalesAndMarketingTool

**Expected outcome:**

- **Risk profile:** Overall business risk, D&B rating, financial strength, SER, SSI  
- **Sales & marketing:** Prospector score, DHQ, marketing risk  
- **Industry context:** Sector trends and illustrative news  
- **Viability synthesis:** Combined recommendation (for example, strong partnership candidate)

---

*The Meridian Precision example on [dnb.ai](https://dnb.ai/use-cases/finance/partnership-evaluation.html) is illustrative and uses synthetic company data.*

## Related finance guides

- [Get a full financial profile](./financial-profile.md)
- [Assess credit risk for a company](./credit-risk.md)
