# Find a D-U-N-S Number

**Source:** [D&B AI — Enterprise Master Data: D-U-N-S lookup](https://dnb.ai/use-cases/data-management/duns-lookup.html)

## What this workflow does

Resolve a business to its **D-U-N-S Number** with match confidence and disambiguation—by name, by name plus address, or by address and phone—with optional firmographic confirmation.

**Tools involved:** **MatchTool** (Company Entity Resolution), **DataTool** (Company Information) for confirmation steps.

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Master data without a global entity key | Assigns verified D-U-N-S for CRM, ERP, and procurement systems |
| Ambiguous legal names or DBAs | Match confidence and method explain why a candidate was chosen |
| Same name in different locations | Location-specific prompts reduce false positives |
| Incomplete intake (address/phone only) | Alternative-identifier matching identifies the entity without a formal name |

Enterprise access adds beneficial ownership, principals, signing authorities, and full corporate hierarchy.

## Workflow steps

### 1. Basic name match

**Prompt:**

> Find the DUNS number for Meridian Precision Components, Inc. in the United States.

**Tool:** MatchTool — Company Entity Resolution

**Expected outcome:** DUNS, primary name, address, match confidence, match method.

### 2. Match with location disambiguation

**Prompt:**

> Look up Meridian Precision Components, Inc. at 2800 Industrial Pkwy, Ste 100, Austin, TX 78744 and confirm the correct DUNS, then return its operating status, marketability flags, primary address, and phone number.

**Tools:** MatchTool, DataTool

**Expected outcome:** Verified DUNS, operating status, verified address/phone/website, out-of-business flag.

### 3. Match by alternative identifiers

**Prompt:**

> I have a company at 2800 Industrial Pkwy, Austin, TX 78744 with phone number +1 512-555-••••. Can you identify this company and provide their firmographic data?

**Tools:** MatchTool, DataTool

**Expected outcome:** Identified entity with DUNS, firmographics, industry, legal form, and status.

---

*Illustrative example on [dnb.ai](https://dnb.ai/use-cases/data-management/duns-lookup.html) uses synthetic company data.*

## Related guides

- [Verify identity and registration details](./verify-identity.md)
- [Identify and resolve incoming records](./resolve-records.md)
- [Cleanse and enrich incoming records](./cleanse-enrich.md)
