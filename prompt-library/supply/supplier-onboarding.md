# Onboard a supplier

**Source:** [D&B AI — Supply: Supplier onboarding](https://dnb.ai/use-cases/supply/supplier-onboarding.html)

## What this workflow does

End-to-end **supplier onboarding**: verify identity against D&B, reconcile the entity against ERP/CRM/procurement records, harmonize master data with verified D-U-N-S and firmographics, then propose approval status updates and stakeholder notifications.

**Tools involved:** **MatchTool**, **DataTool** (Company Information, Global Financials, Company Financials), plus agent integration with first-party systems (read/write supplier records and approval workflows).

**Prerequisite (from D&B):** The agent needs read/write access to supplier records and approval workflows across ERP, CRM, and procurement systems.

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Duplicate or incomplete supplier master records | Cross-system match highlights duplicates (for example, truncated names with no D-U-N-S) |
| Slow onboarding while analysts re-key D&B data | Verified D-U-N-S and attributes flow into create/update actions on internal records |
| Siloed ERP vs. CRM vs. portal data | One workflow updates ERP, creates linked CRM/portal records, and aligns naming and address |
| Handoffs between procurement and compliance after approval | Status change and templated notifications to compliance and procurement with next steps |

Enterprise access can add corporate hierarchy, beneficial ownership, and principals for a fuller supplier master record.

## Workflow steps

### 1. Access supplier data

**Prompt:**

> Provide me with D-U-N-S number and registered company names for Meridian Precision Components, 2800 Industrial Pkwy, Austin, TX 78744.

**Tools:** MatchTool, DataTool — Company Entity Resolution

**Expected outcome:** Verified D-U-N-S, registered name, address, match confidence, active status, industry, employees, start year.

### 2. Check supplier records

**Prompt:**

> Check if Meridian Precision Components, Inc. (D-U-N-S ••••••035) exists in internal systems, retrieve internal IDs, and highlight potential duplicates.

**Expected outcome:** Cross-system table (ERP, CRM, procurement portal) with match flags, internal IDs, and duplicate-risk recommendations.

### 3. Data harmonization

**Prompt:**

> If Meridian Precision Components, Inc. (D-U-N-S ••••••035) already exists in internal systems, update the record with D-U-N-S and available D&B data. If it doesn't exist, create a new record.

**Tool:** DataTool — Company Information, Global Financials, Company Financials

**Expected outcome:** Update vs. create plan (for example, enrich ERP SUP-04821 and sync CRM/portal with parent D-U-N-S and contact attributes).

### 4. Trigger workflows

**Status prompt:**

> Update status for supplier Meridian Precision Components, Inc. (D-U-N-S ••••••035) to approved in internal systems.

**Notifications prompt:**

> Notify compliance and procurement teams of status change for Meridian Precision Components, Inc. (D-U-N-S ••••••035).

**Expected outcome:** Proposed approved status across systems, notification drafts, onboarding checklist, and annual review scheduling—awaiting human confirmation before execution.

---

*The Meridian Precision example on [dnb.ai](https://dnb.ai/use-cases/supply/supplier-onboarding.html) is illustrative and uses synthetic company data.*

## Related supply guides

- [Assess supplier risk](./supplier-risk.md)
- [Onboard a third party](./third-party-onboarding.md)
