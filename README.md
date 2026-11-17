# D&B AI & Commercial Graph

Resources for connecting **D&B Commercial Graph MCP**, running hands-on match-and-enrich exercises, and exploring **AI workflow guides** aligned with [dnb.ai use cases](https://dnb.ai/).

This repository combines getting-started material (originally from Getting Started) with a curated **prompt library**—one folder per use-case category on dnb.ai, each with markdown summaries, sample prompts, and links back to the official workflow pages.

## Start here

**New to Commercial Graph MCP?** Begin with **[Hello, Match & Enrich](./hello-match-and-enrich.md)**.

That walkthrough is the recommended first step. It walks you through the full match-and-enrich loop—from a single-row CSV to a DUNS-resolved, firmographic-enriched output file—using **MatchTool** and **DataTool**. Once you understand that flow, the rest of the resources in this repo will make more sense.

## Repository layout

| Area | Description |
|------|-------------|
| [hello-match-and-enrich.md](./hello-match-and-enrich.md) | Step-by-step CSV match-and-enrich walkthrough |
| [Commercial_Graph_MCP_Setup_Guides/](./Commercial_Graph_MCP_Setup_Guides/) | Platform-specific MCP connection guides (Word and Markdown) |
| [Commercial_Graph_Sample_Prompts.md](./Commercial_Graph_Sample_Prompts.md) | 10 conversational sample prompts with expected responses |
| [prompt-library/](./prompt-library/) | Workflow guides by domain (see below) |

## Prompt library

Each category mirrors a section on [What Can Dun & Bradstreet Data Do in Your AI Workflows?](https://dnb.ai/). Open the category README for an index, then individual guides for purpose, problems solved, tools, and sample prompts.

| Category | Folder | dnb.ai hub |
|----------|--------|------------|
| Compliance | [prompt-library/compliance/](./prompt-library/compliance/) | [Compliance use cases](https://dnb.ai/use-cases/compliance.html) |
| Supply | [prompt-library/supply/](./prompt-library/supply/) | [Supply use cases](https://dnb.ai/use-cases/supply.html) |
| Finance | [prompt-library/finance/](./prompt-library/finance/) | [Finance use cases](https://dnb.ai/use-cases/finance.html) |
| Sales & marketing | [prompt-library/sales-marketing/](./prompt-library/sales-marketing/) | [Sales & marketing use cases](https://dnb.ai/use-cases/sales-marketing.html) |
| Master data | [prompt-library/master-data/](./prompt-library/master-data/) | [Enterprise master data](https://dnb.ai/use-cases/data-management.html) |

Some workflows on dnb.ai assume **agent integration** with CRM, ERP, credit, or marketing systems (read/write or policy access). Those prerequisites are called out in the relevant guide files.

## Suggested path

1. **Connect your environment** — If the MCP server is not connected yet, open [Commercial_Graph_MCP_Setup_Guides/](./Commercial_Graph_MCP_Setup_Guides/) and follow the guide for your platform (Cursor, Databricks, Snowflake, AWS Quick, IBM WatsonX Orchestrate, LiteLLM, Perplexity, and others). Each guide is available in **Word (.docx)** and **Markdown (.md)** format where provided.
2. **Run the hello walkthrough** — Work through [hello-match-and-enrich.md](./hello-match-and-enrich.md) to match a company and append firmographic fields to a CSV.
3. **Try sample prompts** — Use [Commercial_Graph_Sample_Prompts.md](./Commercial_Graph_Sample_Prompts.md) to explore Match, Data, Risk, and Sales & Marketing through natural-language queries.
4. **Explore a use-case domain** — Pick a category in [prompt-library/](./prompt-library/) that matches your role (for example, compliance risk assessment, supplier onboarding, credit memo, ABM, or D-U-N-S lookup) and run the prompts in your connected agent.

## Prerequisites

- A connected **D&B Commercial Graph MCP server**
- A credential tier of at least **companyinfo_L1** for the hello walkthrough (identity, address, SIC, tickers, registration IDs)

Some sample prompts and prompt-library workflows use Risk, Sales & Marketing, Ownership, Payment, or financial fields that may require additional entitlements. Enterprise-only data elements are noted in the library guides where dnb.ai references them.

## Remote

GitHub: [HuntressF_dnbgit/ai](https://github.com/HuntressF_dnbgit/ai) (`origin` on branch `main`). Local working copies may include unpushed changes to the prompt library and this README.
