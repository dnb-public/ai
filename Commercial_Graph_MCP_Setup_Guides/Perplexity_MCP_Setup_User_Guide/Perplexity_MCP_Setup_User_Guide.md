# MCP Server Setup Guide for Perplexity

**Version:** 1.0
**Publication Date:** 2026

## About this Document

The MCP Server Setup Guide provides step-by-step instructions for setting up Dun & Bradstreet's MCP server for a Perplexity application.

---

## Table of Contents

- [Requirements to Connect to Dun & Bradstreet MCP Server](#requirements-to-connect-to-dun--bradstreet-mcp-server)
- [Admin Management Within Perplexity](#admin-management-within-perplexity)
- [Finalize an MCP Connection for Users](#finalize-an-mcp-connection-for-users)
- [Test MCP Connection](#test-mcp-connection)

---

## Requirements to Connect to Dun & Bradstreet MCP Server

Before starting, ensure you have the following:

- An active **Perplexity** account and the standalone application
- The following credentials provided by **Dun & Bradstreet** as part of your D&B Direct+ and D&B.AI Data Package subscriptions:
  - **Client ID** (provided separately)
  - **Client Secret** (provided separately)

---

## Admin Management Within Perplexity

You can find the most complete instructions on how to manage permissions and users directly on the Perplexity website at [https://www.perplexity.ai/help-center/en/articles/12067853-introduction-to-organization-admins](https://www.perplexity.ai/help-center/en/articles/12067853-introduction-to-organization-admins).

---

## Finalize an MCP Connection for Users

1. Log into the Perplexity application.

2. Click **Connectors** on the left-hand side.

   ![Perplexity application showing the left sidebar with a red arrow pointing to "Connectors", alongside other navigation items including New, Computer, Artifacts, Memory, Projects, and Sessions](images/perplexity-sidebar-connectors.png)

3. Search for **D&B Commercial Graph**.

4. Click the **+** symbol on the D&B Commercial Graph tile.

   ![Perplexity Connectors page on the Discover tab, with red arrows pointing to the search bar and the D&B Commercial Graph tile under the "New" section, which shows the description "Match companies to D-U-N-S numbers and retrieve firmographic, credit risk"](images/connectors-discover-dnb.png)

5. Click **+ Add Connector**.

   ![D&B Commercial Graph detail panel showing an Overview on the left with capabilities listed, the Tools tab on the right listing read-only MCP tools (Get business and legal events, Get company contacts and executives, Get company firmographic data, Get company diversity insights, Get company ESG data, Get industry profile report), and a red arrow pointing to the "+ Add connector" button](images/dnb-commercial-graph-add-connector.png)

6. You will be asked for your key and secret. Enter those in the form, then press **Submit**.

7. You will be redirected to Dun & Bradstreet for authentication. Once it is completed, you can close that window and proceed to use the connector.

---

## Test MCP Connection

1. Verify your connection within **Customize** > **Connectors** > **Connected**. You should see the D&B Commercial Graph tile with a checkmark.

   ![Perplexity Connectors page on the "Connected" tab, with red arrows pointing to the Connected filter and the D&B Commercial Graph tile (marked "New") showing a green checkmark, alongside an already-connected HubSpot connector](images/connectors-connected-verify.png)

2. If you do not see the D&B Commercial Graph, follow the steps above to reconnect.

3. Within your Agent Window, under **Computer** mode, ask a question.

4. Perplexity returns a response.

   ![Perplexity Computer mode response for the query "find the duns number for Gorman Manufacturing", showing the primary result (DUNS 009175688 — Gorman Manufacturing Company, Inc., Sacramento, CA) and a table of other matches with D-U-N-S numbers, names, locations, and notes](images/test-response-duns-lookup.png)
