# Verify identity and registration details

**Source:** [D&B AI — Enterprise Master Data: Verify identity](https://dnb.ai/use-cases/data-management/verify-identity.html)

## What this workflow does

Validate **legal identity and registration context** for an entity: names and HQ contacts, government and commercial registration identifiers, industry classifications, and public listing status—each as a focused prompt chain after match.

**Tools involved:** **MatchTool**, **DataTool** (Company Entity Resolution, Company Information).

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| KYC and vendor master requiring registry-aligned names | Registered name vs. trade style with registry match indicators |
| Compliance forms needing EIN, CAGE, UEI, LEI | Structured registration identifier pull (extended blocks on enterprise) |
| Incorrect or missing SIC/NAICS in internal systems | Primary industry, activity description, and optional UNSPSC |
| Public vs. private entity assumptions | Stock listing check plus ownership/hierarchy context when not listed |

Enterprise access adds full registration detail, beneficial ownership, signing authority, and complete family tree.

## Workflow steps

### 1. Verify identity and contacts

**Prompt:**

> Look up Meridian Precision Components, Inc. in the US and give me its DUNS, primary name, trade style names, headquarters address, phone number, and website.

**Tools:** MatchTool, DataTool

**Expected outcome:** DUNS, primary/registered name, HQ address, telephone, website.

### 2. Pull registration identifiers

**Prompt:**

> For Meridian Precision Components, Inc. (US), return the DUNS and all registration numbers available, including EIN, CAGE, UEI, and LEI if present.

**Tools:** MatchTool, DataTool

**Expected outcome:** Verified DUNS, legal form, start year; note on extended identifiers via enterprise.

### 3. Check industry classification

**Prompt:**

> Find Meridian Precision Components, Inc. in the US and tell me its primary SIC industry description, activity description, and any UNSPSC codes.

**Tools:** MatchTool, DataTool

**Expected outcome:** SIC, NAICS, industry and activity descriptions.

### 4. Check stock exchange listings

**Prompt:**

> Look up Meridian Precision Components, Inc. and check whether it has any stock exchange listings.

**Tools:** MatchTool, DataTool

**Expected outcome:** Listing presence (or private/subsidiary context with parent and global ultimate).

---

*Illustrative example on [dnb.ai](https://dnb.ai/use-cases/data-management/verify-identity.html) uses synthetic company data.*

## Related guides

- [Find a D-U-N-S Number](./duns-lookup.md)
- [Cleanse and enrich incoming records](./cleanse-enrich.md)
