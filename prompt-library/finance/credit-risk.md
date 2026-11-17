# Assess credit risk for a company

**Source:** [D&B AI — Finance: Credit risk](https://dnb.ai/use-cases/finance/credit-risk.html)

## What this workflow does

A **credit operations workflow**: queue new applications or portfolio deterioration screens, fetch a full D&B credit profile merged with internal exposure and payment history, apply corporate credit policy to recommend a limit and terms, and draft a credit risk memo (or portfolio risk summary).

**Tools involved:** **MatchTool**, **DataTool**, **RiskTool**, **PaymentTool**, **BusinessEventsTool** (Company Entity Resolution, Company Information, Financial Strength Insights, Payment Insights, Third-Party Risk Insights).

**Prerequisite (from D&B):** Access to first-party credit applications, exposure/payment history, and customer-defined credit policies.

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Backlog of pending credit applications | Prompt lists recent applications with requested limits and next-step guidance |
| Reactive collections without early warning | Alternate prompt finds customers with material Failure Score drops |
| Limit decisions divorced from payment behavior and exposure | Combines PAYDEX, trade experiences, internal invoice history, and outstanding AR |
| Audit trail for credit committee | Policy matrix (pass/fail per criterion) plus structured Credit Risk Memo |
| Portfolio concentration visibility | Alternate memo ranks highest-risk customers by score and receivables |

Enterprise access adds Failure Score time series, percentile benchmarks, credit-limit modeling, and continuous monitoring triggers.

## Workflow steps

### 1. Identify customers

**Prompt:**

> List all new credit applications submitted in the past 7 days that require initial credit review.

**Alternate — monitor for deterioration:**

> Identify existing customers whose Failure Score decreased by more than 10 points this quarter.

**Expected outcome:** Application queue with D-U-N-S, dates, requested credit, status, and context—or deterioration list with score deltas.

### 2. Retrieve data for assessment

**Prompt:**

> Fetch full D&B credit profile for Meridian Precision Components, Inc., including financials, payment behavior, risk scores and fraud indicators. Add internal exposure and 12-month payment history.

**Tools:** MatchTool, DataTool, RiskTool, PaymentTool, BusinessEventsTool

**Expected outcome:** Company overview, financial strength and scores, PAYDEX and trade experiences, filings/events, corporate hierarchy, and illustrative internal exposure.

### 3. Decide on credit limit and risk actions

**Prompt:**

> Apply corporate credit policy to recommend a credit limit for Meridian Precision Components, Inc. Highlight reasons (financials, payment behavior, credit risk scores and exposure).

**Tools:** RiskTool, PaymentTool, DataTool

**Expected outcome:** Policy threshold table, recommended limit, headroom vs. outstanding, terms, review cycle, and approve/hold/decline-style action.

### 4. Create a credit memo

**Prompt:**

> Draft a Credit Risk Memo for Meridian Precision Components, Inc. including credit risk assessment, recommended credit limit, and internal exposure.

**Alternate — portfolio view:**

> Summarize top 10 highest-risk customers based on Failure Score and internal outstanding receivables.

**Tools:** DataTool, RiskTool, PaymentTool, BusinessEventsTool

**Expected outcome:** Dated memo with entity ID, structure, assessment, payment behavior, exposure, recommendation, rationale—or ranked portfolio list with suggested review actions.

---

*The Meridian Precision example on [dnb.ai](https://dnb.ai/use-cases/finance/credit-risk.html) is illustrative and uses synthetic company data.*

## Related finance guides

- [Get a full financial profile](./financial-profile.md)
- [Evaluate a potential partnership](./partnership-evaluation.md)
