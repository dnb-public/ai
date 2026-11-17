# Assess supplier risk

**Source:** [D&B AI — Supply: Supplier risk](https://dnb.ai/use-cases/supply/supplier-risk.html)

## What this workflow does

**Supplier risk review** at scale: identify suppliers needing review (new adds or sector screens), pull a full D&B profile (firmographics, structure, ownership, UBO, risk, compliance, filings) combined with internal spend, apply customer risk policy to produce a **RAG rating**, compare quarter-over-quarter movement, and draft a supplier risk memo with citations and recommended actions.

**Tools involved:** **MatchTool**, **DataTool**, **RiskTool**, **OwnershipTool**, **PaymentTool**, **BusinessEventsTool** (Company Information, Third-Party Risk Insights, Filings & Events, Hierarchies & Connections, Ownership Insights, Financial Strength Insights, Payment Insights).

**Prerequisite (from D&B):** Access to first-party supplier records, spend/criticality data, and customer-defined risk policies and thresholds.

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Backlog of new suppliers without timely risk review | Prompts list recent adds or sector cohorts that need screening |
| Inconsistent risk decisions across buyers | Policy-driven RAG table maps D&B metrics to green/amber/red thresholds |
| Missing context on spend exposure vs. external scores | Internal 12-month spend and criticality sit beside SER, SSI, failure/delinquency, PAYDEX |
| Audit and committee requests for written rationale | Memo template ties D&B citations, structure, filings, and proportional actions |
| Portfolio benchmarking | Alternate prompt surfaces top performers by risk indicators |

Enterprise access can add full financials, historical PAYDEX, and board composition for critical suppliers.

## Workflow steps

### 1. Identify suppliers

**Prompt:**

> List suppliers added in the last 30 days that require initial risk review.

**Alternate — sector screen:**

> Identify suppliers in Machine Shops and Turned Products (SIC 3599 / NAICS 332710) with elevated SSI/SER in the past quarter.

**Tools:** MatchTool, DataTool — Company Entity Resolution, Company Information

**Expected outcome:** Named suppliers with D-U-N-S, add date, category, and whether elevated signals exist in the cohort.

### 2. Retrieve data

**Prompt:**

> Fetch full D&B profile for Meridian Precision Components, Inc. (D-U-N-S ••••••035) — firmographics, corporate structure, ownership, UBO, risk, compliance — add our 12-month spend.

**Alternate — scores only:**

> Retrieve latest SER, SSI, Failure and Delinquency Scores for Meridian Precision Components, Inc. (D-U-N-S ••••••035).

**Tools:** DataTool, RiskTool, OwnershipTool, PaymentTool, BusinessEventsTool

**Expected outcome:** Firmographics, hierarchy, UBOs, risk scores, PAYDEX, filings (or lack thereof), and illustrative internal spend/criticality.

### 3. Decide actions

**RAG rating prompt:**

> Create a RAG rating for Meridian Precision Components, Inc. (D-U-N-S ••••••035) based on our risk policy and add corporate structure relations. Describe the rationale.

**Quarter-over-quarter prompt:**

> Compare current vs. prior-quarter risk indicators for Meridian Precision Components, Inc. (D-U-N-S ••••••035); highlight significant changes.

**Tools:** RiskTool, OwnershipTool — Third-Party Risk Insights, Hierarchies & Connections, Financial Strength Insights

**Expected outcome:** Policy matrix with pass/fail per criterion, overall RAG (for example, GREEN), group-level context, and QoQ delta narrative.

### 4. Create memo

**Prompt:**

> Draft a memo with RAG rating, D&B citations, and internal spend context; include recommended actions proportional to impact.

**Alternate:**

> Create a memo listing the 10 best performing companies when it comes to risk indicators.

**Expected outcome:** Supplier Risk Memo with rating, indicator table, spend context, structure, and monitoring/escalation recommendations (or ranked top performers list).

---

*The Meridian Precision example on [dnb.ai](https://dnb.ai/use-cases/supply/supplier-risk.html) is illustrative and uses synthetic company data.*

## Related supply guides

- [Onboard a supplier](./supplier-onboarding.md)
- [Onboard a third party](./third-party-onboarding.md)
