# Cleanse and enrich incoming records

**Source:** [D&B AI — Enterprise Master Data: Cleanse & enrich](https://dnb.ai/use-cases/data-management/cleanse-enrich.html)

## What this workflow does

A **master data management (MDM) cycle**: identify via D-U-N-S, find cross-system duplicates, propose merge/survivorship, append foundational firmographics and hierarchy, answer steward questions (for example, global ultimate), and apply a confirmed update to CRM/ERP.

**Tools involved:** **MatchTool**, **DataTool**, **OwnershipTool** (Company Entity Resolution, Company Information, Hierarchies & Connections).

**Prerequisite (from D&B):** Read/write access to master records across CRM and ERP, including permission to propose merges and updates.

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Same supplier under CRM and ERP with different names | Duplicate detection by D-U-N-S and fuzzy name/address |
| Survivorship rules unclear during merge | Recommendation for golden record (verified D-U-N-S wins) |
| Lost ERP IDs when deduplicating | Merge plan retains supplier IDs, terms, and cost-center from secondary record |
| Incomplete hierarchy on golden record | Append parent, domestic ultimate, and global ultimate D-U-N-S |
| Data stewards need explainability | Natural-language answers for MDM concepts (for example, global ultimate) |

Enterprise access adds UBO, principals, financials, and full family-tree detail.

## Workflow steps

### 1. Identification with the D-U-N-S Number

**Prompt:**

> See process 1 (Data Management Identification) — resolve input record to a verified D-U-N-S.

**Tool:** MatchTool — Company Entity Resolution

**Expected outcome:** Matched entity, D-U-N-S, address, confidence, and match method.

### 2. Consolidation of similar/same datasets

**Prompt:**

> Can you identify any similar or duplicate datasets by either the same D-U-N-S, similar Name, Address we should consider as duplicate entries?

**Tool:** MatchTool

**Expected outcome:** Side-by-side records with match evidence and survivorship recommendation.

### 3. Elimination of duplicates

**Prompt:**

> Eliminate duplicates based on the consolidation analysis.

**Expected outcome:** Surviving record, fields to merge from duplicate, conflict resolution rules, confirmation gate before merge.

### 4. Append relevant data

**Prompt:**

> Please give me the foundational data set for D-U-N-S ••••••035 including Name, Addresses, Hierarchy information (Parent, Domestic Ultimate, Global Ultimate).

**Tools:** DataTool, OwnershipTool

**Expected outcome:** Identity block and hierarchy block (parent, GU, family-tree size).

### 5. Support data questions

**Prompt:**

> Can you explain me what a global ultimate is?

**Tool:** OwnershipTool — Hierarchies & Connections

**Expected outcome:** Plain-language definition tied to the entity’s family tree.

### 6. Update data

**Prompt:**

> Update the surviving record with the appended foundational data.

**Expected outcome:** Field-by-field change set for CRM/ERP; awaits confirmation before write.

---

*Illustrative example on [dnb.ai](https://dnb.ai/use-cases/data-management/cleanse-enrich.html) uses synthetic company data.*

## Related guides

- [Identify and resolve incoming records](./resolve-records.md)
- [Verify identity and registration details](./verify-identity.md)
