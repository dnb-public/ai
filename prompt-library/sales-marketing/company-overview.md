# Look up a company overview

**Source:** [D&B AI — Sales & Marketing: Company overview](https://dnb.ai/use-cases/sales-marketing/company-overview.html)

## What this workflow does

Resolve a company to its DUNS, then pull a **company overview**: official identity, contact attributes, industry classification, employee count, founding year, legal form, status, and optional industry context.

**Tools involved:** **MatchTool** (Company Entity Resolution) → **DataTool** (Company Information).

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Reps researching accounts before calls | Fast firmographic snapshot without manual web research |
| Incomplete CRM records | Verified name, address, SIC/NAICS, and employee count from D&B |
| Qualification and territory planning | Size, industry, and operating status in one prompt |
| Foundation for deeper sales or marketing flows | Same match + data pattern extends to ABM, playbooks, and segmentation |

Enterprise access adds revenue, financial overview, and deal-sizing context.

## Workflow steps

### 1. Resolve the company

**Prompt:**

> Find the DUNS number for Meridian Precision Components, Inc. in the United States.

**Tool:** MatchTool — Company Entity Resolution

**Expected outcome:** DUNS, primary name, address, match confidence and method.

### 2. Pull the company overview

**Prompt:**

> Look up Meridian Precision Components, Inc. and give me a company overview: industry, employee count, revenue, and founding date.

**Tool:** DataTool — Company Information

**Expected outcome:** Official name, DUNS, contact fields, SIC/NAICS, employees, year started, legal form, status, and industry context.

---

*Illustrative example on [dnb.ai](https://dnb.ai/use-cases/sales-marketing/company-overview.html) uses synthetic company data.*

## Related guides

See [README](./README.md) for the full sales & marketing library.
