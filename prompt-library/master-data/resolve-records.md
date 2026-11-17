# Identify and resolve incoming records

**Source:** [D&B AI — Enterprise Master Data: Resolve records](https://dnb.ai/use-cases/data-management/resolve-records.html)

## What this workflow does

Take **messy onboarding intake** from CRM, ERP, or procurement channels, triage for auto-match vs. manual review, search D&B for the correct D-U-N-S, and propose writing the golden key and enriched attributes back to the source system.

**Tools involved:** **MatchTool** (Company Entity Resolution), **DataTool** (Company Information).

**Prerequisite (from D&B):** Access to first-party onboarding records and connected CRM, ERP, and procurement intake systems.

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Web forms with partial addresses and no D-U-N-S | Channel inventory shows gaps and recommends D&B resolution |
| Manual data stewards reviewing every new vendor | Acceptance rules auto-identify high-confidence matches |
| Inconsistent city/name spelling in intake | Match evidence (similarity %, city/country) documents the decision |
| CRM records stuck without a write-back step | Confirmed entity triggers proposed CRM update with hierarchy fields |

Enterprise access extends records toward KYC-ready profiles (UBO, principals, full hierarchy).

## Workflow steps

### 1. Identify all onboarding channels

**Prompt:**

> Agent receives the data in question with additional information on where to gather more information.

**Expected outcome:** Source table (portal, ERP, procurement) with available vs. missing fields and next-step recommendation.

### 2. Triage the data sets

**Prompt:**

> Triage records for automatic identification vs. manual review.

**Tool:** MatchTool — Company Entity Resolution

**Expected outcome:** Auto-match with acceptance rule narrative, or queue for manual review.

### 3. Select and search the D&B database

**Prompt:**

> Can you give me the D-U-N-S for the Company Meridian Precision in Country US in City Austin?

**Tool:** MatchTool

**Expected outcome:** High-confidence D-U-N-S, registered name, verified address, match evidence, active status.

### 4. Trigger workflow for data enrichment and update

**Prompt:**

> Meridian Precision Components, Inc. is the correct company, please write the D-U-N-S in our Database CRM.

**Tool:** DataTool — Company Information

**Expected outcome:** Proposed CRM write (D-U-N-S, name, address, phone, website, status) plus enrichment (SIC/NAICS, employees, start year, parent D-U-N-S).

---

*Illustrative example on [dnb.ai](https://dnb.ai/use-cases/data-management/resolve-records.html) uses synthetic company data.*

## Related guides

- [Find a D-U-N-S Number](./duns-lookup.md)
- [Cleanse and enrich incoming records](./cleanse-enrich.md)
