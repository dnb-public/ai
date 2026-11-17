# Direct Plus MCP — 10 Sample Prompts & Expected Responses

This guide contains 10 ready-to-use prompts that exercise the **Match**, **Data**, **Sales & Marketing**, and **Risk** tools available through the D&B Direct Plus MCP connector.

---

## Before you begin

Connect the D&B Commercial Graph MCP server to your environment before running the prompts below. Platform-specific setup instructions live in the [Commercial Graph MCP Setup Guides](./Commercial_Graph_MCP_Setup_Guides/) folder. Each subfolder contains a walkthrough for a supported integration path — for example, Cursor, Databricks, Snowflake, AWS Quick, IBM WatsonX Orchestrate, and LiteLLM. Guides are provided in both **Word (.docx)** and **Markdown (.md)** format; use whichever suits your workflow. The list of supported platforms may change over time, so browse the folder for the guide that matches your setup.

For a hands-on CSV match-and-enrich walkthrough, see [Hello, Match & Enrich](./hello-match-and-enrich.md).

---

## Prompt 1 — Basic Company Match

**Tool(s) used:** Match

> **Prompt:** "Find the DUNS number for Nike in the United States."

**Expected Response:**

Claude calls the **MatchTool** with `name: "Nike"` and `countryISOAlpha2Code: "US"`. The response returns a list of candidate matches ranked by confidence. The top result should be:

- **Company:** NIKE, INC.
- **DUNS:** 060704780 *(example)*
- **Address:** One Bowerman Drive, Beaverton, OR 97005, US
- **Confidence:** High
- **Operating Status:** Active

Claude presents the best match and asks the user to confirm before proceeding with any follow-up tools.

---

## Prompt 2 — Match + Firmographic Overview

**Tool(s) used:** Match → Data

> **Prompt:** "Look up Samsung Electronics in South Korea and give me a company overview — industry, employee count, revenue, and founding date."

**Expected Response:**

Claude first calls the **MatchTool** (`name: "Samsung Electronics"`, `countryISOAlpha2Code: "KR"`) to resolve the DUNS number. It then calls the **DataTool** (`block_extended_mode: false`, `show_historical_data: false`, `include_partial_financial_year: false`) with the retrieved DUNS.

The response should include details such as:

- **Official Name:** Samsung Electronics Co., Ltd.
- **Industry:** Semiconductor and Other Electronic Component Manufacturing (SIC/NAICS)
- **Employees:** ~267,000 (consolidated)
- **Latest Annual Revenue:** ~₩258 trillion (or USD equivalent)
- **Founded:** 1969
- **Operating Status:** Active
- **Website:** samsung.com

---

## Prompt 3 — Risk Assessment for a Known Company

**Tool(s) used:** Match → Risk

> **Prompt:** "What is the current financial risk profile for Tesla in the US? Include the failure score and delinquency score."

**Expected Response:**

Claude calls the **MatchTool** for Tesla (US), then passes the DUNS to the **RiskTool** (`show_historical_data: false`). The expected output contains:

- **Standard Risk Rating:** A numerical rating (1–4) with a description (e.g., "Minimal", "Low", "Moderate", "Significant")
- **Failure Score:** A score (typically 1–100) indicating the likelihood of business failure within the next 12 months, plus the national percentile and risk class
- **Delinquency Score:** A score predicting the likelihood of severely delinquent payments, with class and percentile
- **Supplier Evaluation Risk (SER):** Rating and commentary
- **Supplier Stability Index (SSI):** Rating and commentary
- **Key Risk Factors:** Narrative reasons driving the scores (e.g., industry sector, financial size, years in business)

---

## Prompt 4 — Sales & Marketing Scores

**Tool(s) used:** Match → Sales & Marketing

> **Prompt:** "Pull the sales and marketing insights for Starbucks in the United States. I want to know their marketing responsiveness scores and whether they are a decision headquarters."

**Expected Response:**

Claude matches Starbucks (US), then calls the **SalesAndMarketingTool**. The response includes:

- **Decision Headquarters (DHQ):** Yes/No — indicates whether this entity is the decision-making point for purchasing within its corporate family
- **Prospector Model Ratings:** Segment scores for Total Balance, Card Responsiveness, Loan Propensity, and Line of Credit Propensity (each rated on a scale such as 1–9)
- **Marketing Risk Score:** A score indicating how risky it is to market to this company
- **Triple Play Score:** A composite score used for sales targeting
- **Buydex Scores:** Classification of the company based on spending patterns
- **Material Change Predictions:** Likelihood indicators for growth or decay across dimensions like Risk, Employee, Sales, Borrowing, and Spend

---

## Prompt 5 — Company Match by Address Only

**Tool(s) used:** Match

> **Prompt:** "I have a potential vendor at 1600 Amphitheatre Parkway, Mountain View, California. Can you identify this company?"

**Expected Response:**

Claude calls the **MatchTool** with `countryISOAlpha2Code: "US"`, `streetAddressLine: "1600 Amphitheatre Parkway"`, `addressLocality: "Mountain View"`, `addressRegion: "CA"` — no company name needed.

The result should identify:

- **Company:** GOOGLE LLC (or Alphabet Inc., depending on entity registered at that address)
- **DUNS:** Retrieved number
- **Match Confidence:** High
- **Address Confirmation:** Full address and postal code returned

---

## Prompt 6 — Historical Risk Trends

**Tool(s) used:** Match → Risk (historical)

> **Prompt:** "Show me the historical risk data for Ford Motor Company in the US. I want to see how their failure score and delinquency score have trended over time."

**Expected Response:**

Claude matches Ford Motor Company (US), then calls the **RiskTool** with `show_historical_data: true`. The response includes:

- **Current Scores:** Latest failure score, delinquency score, SER, SSI, and viability rating
- **Historical Failure Scores:** A time series of failure scores (e.g., quarterly or as available), showing the trajectory
- **Historical Delinquency Scores:** Same time-series format for delinquency
- **Commentary:** Narrative reasons for any notable shifts (e.g., industry downturns, restructuring events)
- **Trend Summary:** Claude synthesises whether risk has been improving, stable, or deteriorating

---

## Prompt 7 — Firmographic Deep Dive with Financials

**Tool(s) used:** Match → Data (with historical financials)

> **Prompt:** "Get me the full financial profile for Siemens AG in Germany, including historical financial statements."

**Expected Response:**

Claude matches Siemens AG (`countryISOAlpha2Code: "DE"`), then calls the **DataTool** with `show_historical_data: true`, `block_extended_mode: false`, `include_partial_financial_year: false`.

The response should cover:

- **Company Overview:** Official name, registration numbers, LEI, address, industry codes
- **Employees:** Total and trend
- **Current Financials:** Latest balance sheet (total assets, total liabilities, net worth), profit and loss (revenue, net income), and the accounting period dates
- **Historical Financials:** Prior-year financial statements with start and end period dates for each, enabling year-over-year comparison
- **Note:** Claude flags if any historical period is unavailable

---

## Prompt 8 — Combined Risk + Sales & Marketing Assessment

**Tool(s) used:** Match → Risk + Sales & Marketing

> **Prompt:** "I'm evaluating a potential partnership with Unilever in the UK. Give me both their risk profile and their sales and marketing insights so I can assess viability."

**Expected Response:**

Claude matches Unilever (GB), then makes two parallel calls — one to the **RiskTool** and one to the **SalesAndMarketingTool**.

**Risk Profile:**
- Standard risk rating, failure score, delinquency score
- SER and SSI ratings
- Viability rating and key risk factors

**Sales & Marketing Insights:**
- DHQ status (is this entity the purchasing decision-maker?)
- Prospector model scores
- Triple play score
- Material change predictions for growth/decay

**Synthesis:** Claude combines both to provide a summary, e.g., "Unilever UK shows a low-risk profile with strong financial stability. They are flagged as a decision headquarters, meaning this entity has purchasing authority within the Unilever family. Material change models suggest stable or growing sales trajectory, making them a viable partnership candidate from both a risk and commercial perspective."

---

## Prompt 9 — Match by Registration Number

**Tool(s) used:** Match → Data

> **Prompt:** "I have a VAT registration number: FR35433379801. Can you identify this company and provide their firmographic data?"

**Expected Response:**

Claude calls the **MatchTool** with `countryISOAlpha2Code: "FR"` and `registrationNumber: "FR35433379801"`. The match result identifies the entity (e.g., a known French corporation). Claude then calls the **DataTool** on the retrieved DUNS.

The response includes:

- **Company Identified:** Official registered name
- **DUNS:** Retrieved number
- **Registration Confirmation:** VAT number confirmed in the registry
- **Firmographic Data:** Industry, address, employee count, website, operating status, and current financial statements with period dates

---

## Prompt 10 — Full Due Diligence Package

**Tool(s) used:** Match → Data + Risk + Sales & Marketing

> **Prompt:** "Run a full due diligence check on Toyota Motor Corporation in Japan. I need firmographic data, financial risk indicators, and sales and marketing insights all in one view."

**Expected Response:**

Claude matches Toyota (`countryISOAlpha2Code: "JP"`), then calls all three tools — **DataTool**, **RiskTool**, and **SalesAndMarketingTool** — using the retrieved DUNS.

**Firmographic Summary (Data):**
- Official name, address, industry (Motor Vehicle Manufacturing), employee count, website
- Latest revenue, total assets, net worth, and accounting period

**Risk Summary (Risk):**
- Standard risk rating and viability rating
- Failure score with national percentile
- Delinquency score
- SER and SSI ratings
- Key risk factors and narrative commentary

**Sales & Marketing Summary (S&M):**
- DHQ status
- Prospector model ratings across all segments
- Marketing risk score and triple play score
- Material change predictions

**Executive Summary:** Claude produces a combined narrative, e.g., "Toyota Motor Corporation is a well-established, low-risk entity with strong financial fundamentals. The failure and delinquency scores place the company in the lowest-risk percentiles nationally. As a decision headquarters, Toyota holds direct purchasing authority. Material change models predict stable or growing trends across all measured dimensions, supporting confidence in both credit extension and commercial engagement."

---

## Quick Reference — Tool Cheat Sheet

| Tool | Key Data Points | Required Parameters |
|---|---|---|
| **Match** | DUNS resolution, address confirmation, match confidence | `countryISOAlpha2Code` (mandatory); name, address, registration number (optional) |
| **Data** | Firmographics, financials, employees, industry, registration | `duns`, `block_extended_mode`, `show_historical_data`, `include_partial_financial_year` |
| **Risk** | Failure score, delinquency score, SER, SSI, viability rating | `duns`, `show_historical_data` |
| **Sales & Marketing** | DHQ, prospector scores, triple play, Buydex, material change | `duns` |
