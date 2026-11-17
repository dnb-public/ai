# Track risk trends over time

**Source:** [D&B AI — Compliance: Historical risk](https://dnb.ai/use-cases/compliance/historical-risk.html)

## What this workflow does

You resolve a company to its DUNS, then request **historical and trending risk context**—how failure score, delinquency score, and related indicators have moved over time—not only a single point-in-time rating. The guide shows a current risk snapshot plus trend commentary; deeper time series (failure/delinquency over time, PAYDEX trends, national percentile movement) are available with enterprise access.

**Tools involved:** **MatchTool** (entity resolution) → **RiskTool** (Financial Strength Insights).

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Approving relationships based on a stale or one-day snapshot | Surfaces whether risk is improving, stable, or deteriorating |
| Portfolio or vendor monitoring without building custom analytics | Natural-language prompts request trend-oriented risk views from D&B |
| Explaining credit or compliance decisions to stakeholders | Combines current ratings with narrative trend context |
| Early warning before a counterparty crosses internal thresholds | Historical movement helps flag deterioration before it becomes a default event |

Use this after [assessing current financial risk](./risk-assessment.md) when the decision depends on **direction of travel**, not just today’s score.

## Workflow steps

### 1. Resolve the company

**Prompt:**

> Find the DUNS number for Meridian Precision Components, Inc. in the United States.

**Tool:** MatchTool — Company Entity Resolution

**Expected outcome:** DUNS, primary name, address, and high match confidence.

### 2. Pull historical risk data

**Prompt:**

> Show me the historical risk data for Meridian Precision Components, Inc. I want to see how their failure score and delinquency score have trended over time.

**Tool:** RiskTool — Financial Strength Insights

**Expected outcome:** Current D&B rating, financial strength, risk indicator, SER, SSI, and commentary on whether indicators suggest stable or changing financial health.

---

*The Meridian Precision example on [dnb.ai](https://dnb.ai/use-cases/compliance/historical-risk.html) is illustrative and uses synthetic company data.*

## Related compliance guides

- [Assess financial risk for a company](./risk-assessment.md)
- [Run a full due diligence check](./due-diligence.md)
