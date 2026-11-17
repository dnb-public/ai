# Run account profiling and build a sales playbook

**Source:** [D&B AI — Sales & Marketing: Sales playbook](https://dnb.ai/use-cases/sales-marketing/sales-playbook.html)

## What this workflow does

Turn CRM pipeline priority into a **seller-ready playbook**: rank opportunities, generate a D&B-enriched company summary, identify procurement/sourcing contacts (aligned to existing relationships), and draft personalized discovery questions tied to verified signals.

**Tools involved:** **DataTool**, **SalesAndMarketingTool**, **ContactTool** (Company Information, Global/Company Financials, Sales & Marketing Insights, Principals & Contacts).

**Prerequisite (from D&B):** Access to first-party CRM pipeline, opportunity, account, and contact data.

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Reps prioritizing the wrong deals | Surfaces highest weighted opportunities from CRM |
| Last-minute prep before executive meetings | One-shot company summary with risk, growth, and group context |
| Single-threaded deals | Maps buying committee and multi-thread recommendations |
| Generic discovery questions | Questions anchored to expansion, certification, labor, hierarchy, and commodity risk |

Enterprise access adds revenue, financial overview, and deal-sizing context.

## Workflow steps

### 1. Identify customers or prospects

**Prompt:**

> Show me all active opportunities in my pipeline sorted by deal size and closing probability.

**Expected outcome:** Ranked opportunity table with D-U-N-S, stage, and prep recommendation for top accounts.

### 2. Create summary

**Prompt:**

> Generate a company summary for D-U-N-S ••••••035.

**Tools:** DataTool, SalesAndMarketingTool

**Expected outcome:** Industry, size, financial health, growth signals, corporate family, challenges, and marketing scores.

### 3. Identify contact person(s)

**Prompt:**

> List procurement and sourcing contacts for D-U-N-S ••••••035, prioritizing those with similar roles to my existing contact Sarah Chen.

**Tool:** ContactTool — Principals & Contacts

**Expected outcome:** Contact table with roles and multi-thread outreach recommendation.

### 4. Prepare questions or content

**Prompt:**

> Draft 5 personalized outreach questions for D-U-N-S ••••••035 based on their procurement challenges and growth trajectory.

**Tools:** DataTool, SalesAndMarketingTool

**Expected outcome:** Five questions each tied to a specific D&B or news signal.

---

*Illustrative example on [dnb.ai](https://dnb.ai/use-cases/sales-marketing/sales-playbook.html) uses synthetic company data.*

## Related guides

- [Create an account-based marketing plan](./abm.md)
- [Expand sales across a corporate family](./corporate-family-expansion.md)
