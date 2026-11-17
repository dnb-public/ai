# Expand sales across a corporate family

**Source:** [D&B AI — Sales & Marketing: Corporate family expansion](https://dnb.ai/use-cases/sales-marketing/corporate-family-expansion.html)

## What this workflow does

Use **corporate linkage** to grow wallet share: map parent/subsidiary relationships against existing customers, find look-alike entities in the family, rank Decision HQ / buying index / growth, and identify equivalent contacts for warm expansion.

**Tools involved:** **MatchTool**, **DataTool**, **OwnershipTool**, **SalesAndMarketingTool**, **ContactTool** (Entity Resolution, Hierarchies & Connections, Company Information, Sales & Marketing Insights, Principals & Contacts).

**Prerequisite (from D&B):** Access to first-party account/customer status to flag whitespace vs. existing relationships.

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Land-and-expand stuck at one subsidiary | Surfaces non-customer entities in the same global ultimate |
| Unclear where enterprise decisions are made | Decision HQ and regional HQ labels guide enterprise entry points |
| Random cross-sell without profile fit | Look-alike scoring by industry and size within the family |
| Cold outreach at sister companies | Contact mapping by role parity (for example, CEO to group CEO) |

Enterprise access adds financials, spend capacity, growth trajectory, and full principal lists per entity.

## Workflow steps

### 1. Identify customer

**Prompt:**

> Show me companies that are members of a corporate group, including parent and subsidiary relationships.

**Tools:** MatchTool, DataTool, OwnershipTool

**Expected outcome:** Family tree table with relationship types and existing-customer flags; whitespace count.

### 2. Identify look-alikes

**Prompt:**

> Identify companies within the Meridian Industrial Group (D-U-N-S ••••••397) that have similar profiles to Meridian Precision Components, Inc. (D-U-N-S ••••••035) based on industry, size, and spend.

**Tools:** MatchTool, DataTool

**Expected outcome:** Ranked subsidiaries with industry/size match scores.

### 3. Identify DHQ, buying index and growth trajectory

**Prompt:**

> Provide Decision HQ, Buying Index, and Growth Trajectory for all companies in the Meridian Industrial Group (D-U-N-S ••••••397), highlighting top 3 with highest buying power.

**Tools:** DataTool, SalesAndMarketingTool

**Expected outcome:** DHQ/buying/growth table and recommendation for enterprise entry (for example, regional HQ).

### 4. Get in touch with similar contact

**Prompt:**

> List procurement and sourcing contacts for companies in the Meridian Industrial Group (D-U-N-S ••••••397), prioritizing those with similar roles to Sarah Chen (CEO).

**Tool:** ContactTool — Principals & Contacts

**Expected outcome:** Group-wide contact list and warm-intro strategy referencing the existing subsidiary relationship.

---

*Illustrative example on [dnb.ai](https://dnb.ai/use-cases/sales-marketing/corporate-family-expansion.html) uses synthetic company data.*

## Related guides

- [Create an account-based marketing plan](./abm.md)
- [Run account profiling and build a sales playbook](./sales-playbook.md)
