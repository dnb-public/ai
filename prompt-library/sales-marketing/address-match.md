# Identify a company by address

**Source:** [D&B AI — Sales & Marketing: Address match](https://dnb.ai/use-cases/sales-marketing/address-match.html)

## What this workflow does

When you have a **street address but no reliable company name**, match the location to a verified entity, then confirm with a full company profile.

**Tools involved:** **MatchTool** (Company Entity Resolution) → **DataTool** (Company Information).

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Leads from maps, shipping labels, or facility visits without legal name | Address-first resolution to DUNS and registered name |
| Vendor or prospect lists with address only | Converts physical location into matchable master data |
| Data quality on inbound web forms | Validates “who is at this address?” before CRM create |
| Field sales or event follow-up | Quick identity confirmation after visiting a site |

Enterprise access adds revenue and financial overview for sizing the opportunity.

## Workflow steps

### 1. Match by address

**Prompt:**

> I have a potential vendor at 2800 Industrial Pkwy, Austin, TX 78744. Can you identify this company?

**Tool:** MatchTool — Company Entity Resolution

**Expected outcome:** Company name, DUNS, full address, match confidence and method.

### 2. Confirm with a company profile

**Prompt:**

> Give me a company overview for the entity at 2800 Industrial Pkwy, Austin, TX 78744.

**Tool:** DataTool — Company Information

**Expected outcome:** Official name, DUNS, phone, website, industry, employees, year started, status.

---

*Illustrative example on [dnb.ai](https://dnb.ai/use-cases/sales-marketing/address-match.html) uses synthetic company data.*

## Related guides

- [Look up a company overview](./company-overview.md)
- [Run account profiling and build a sales playbook](./sales-playbook.md)
