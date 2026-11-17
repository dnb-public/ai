# Onboard a third party

**Source:** [D&B AI — Supply: Third-party onboarding](https://dnb.ai/use-cases/supply/third-party-onboarding.html)

## What this workflow does

**Third-party onboarding** with stronger governance than basic supplier setup: verify identity, enrich records with family tree, principals, and beneficial owners, run a policy-aligned risk assessment (D&B + internal rules), then propose approval and notifications across vendor master, compliance register, and procurement.

**Tools involved:** **MatchTool**, **DataTool**, **OwnershipTool**, **ContactTool**, **RiskTool**, **PaymentTool**, **BusinessEventsTool** (Company Information, Hierarchies & Connections, Ownership Insights, Principals & Contacts, Financial Strength Insights, Third-Party Risk Insights, Payment Insights, Filings & Events).

**Prerequisite (from D&B):** Access to third-party master data, internal risk policies, and connected approval/notification workflows.

## What it solves for

| Challenge | How this workflow helps |
|-----------|-------------------------|
| Third parties treated like low-touch vendors without UBO/principal capture | Dedicated enrichment step writes principals, UBOs, and parent/global ultimate links |
| Hidden ownership or looping structures | Ownership insights flag UBO count, majority holder, and looping relations |
| Onboarding without a documented risk gate | Policy evaluation against D&B ratings, SER/SSI, scores, PAYDEX, and filings before approval |
| Compliance needs evidence for “approved third party” | Low-risk rationale, UBO documentation references, and system status proposals |
| Same entity onboarded under different internal names | High-confidence match on name, address, and registration number up front |

Enterprise access can add full family-tree detail, historical ownership changes, and signing-authority data.

## Workflow steps

### 1. Identify & verify

**Prompt:**

> Provide me with D-U-N-S number and registered company names for Meridian Precision Components, 2800 Industrial Pkwy, Austin, TX 78744, US.

**Tools:** MatchTool, DataTool — Company Entity Resolution, Company Information

**Expected outcome:** Verified D-U-N-S, registry-matched legal name, address, match method, active status, start year, legal form.

### 2. Enrich with associations

**Prompt:**

> Check the family tree and principals for Meridian Precision Components, Inc. (D-U-N-S ••••••035), and update internal records with all identified principals & beneficial owners.

**Tools:** OwnershipTool, ContactTool — Hierarchies & Connections, Ownership Insights, Principals & Contacts

**Expected outcome:** Global/domestic ultimate, ownership percentage, family-tree size, principal roster, UBO list, proposed internal field updates.

### 3. Risk assessment

**Prompt:**

> Evaluate Meridian Precision Components, Inc. (D-U-N-S ••••••035) with internal data based on internal risk policy to form a complete risk assessment, and provide rationale.

**Tools:** RiskTool, PaymentTool, BusinessEventsTool — Financial Strength Insights, Third-Party Risk Insights, Payment Insights, Filings & Events

**Expected outcome:** D&B indicator set, clean filings summary, policy pass/fail, overall low/medium/high rating, and recommended onboarding terms.

### 4. Trigger workflows

**Status prompt:**

> Update status for Meridian Precision Components, Inc. (D-U-N-S ••••••035) to approved in internal systems.

**Notifications prompt:**

> Notify compliance and procurement teams of status change for Meridian Precision Components, Inc. (D-U-N-S ••••••035).

**Tool:** DataTool (for orchestration context in the guide)

**Expected outcome:** Proposed approved status across vendor master, compliance register, and portal; notification drafts; checklist and annual review scheduling—confirmation required before execution.

---

*The Meridian Precision example on [dnb.ai](https://dnb.ai/use-cases/supply/third-party-onboarding.html) is illustrative and uses synthetic company data.*

## Related supply guides

- [Onboard a supplier](./supplier-onboarding.md)
- [Assess supplier risk](./supplier-risk.md)
