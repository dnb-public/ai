# MCP Server Setup Guide for Databricks

**Version:** 1.0
**Publication Date:** 2026

## About this Document

The MCP Server Setup Guide provides step-by-step instructions for setting up Dun & Bradstreet's MCP server for your Databricks instance.

---

## Table of Contents

- [Requirements to Connect to Dun & Bradstreet MCP Server](#requirements-to-connect-to-dun--bradstreet-mcp-server)
- [Create an MCP Connection](#create-an-mcp-connection)
- [Finalize Connection and Add Users](#finalize-connection-and-add-users)
- [Test MCP Connection](#test-mcp-connection)

---

## Requirements to Connect to Dun & Bradstreet MCP Server

Before starting, ensure you have the following:

- An active **Databricks** account
- **Unity Catalog** enabled
- Admin Access to **create** a **Databricks connection** (most users do not have this access)
- The following credentials provided by **Dun & Bradstreet** as part of your D&B Direct+ and D&B.AI Data Package subscriptions:
  - **Client ID** (provided separately)
  - **Client Secret** (provided separately)
  - **Base URL:** [https://plus.dnb.com/v2/mcp](https://plus.dnb.com/v2/mcp)
  - **OAuth 2.0 Authorization Endpoint:** [https://plus.dnb.com/v2/mcp/authorize](https://plus.dnb.com/v2/mcp/authorize)
  - **OAuth 2.0 Token Endpoint:** [https://plus.dnb.com/v2/mcp/token](https://plus.dnb.com/v2/mcp/token)

---

## Create an MCP Connection

1. Log into Databricks.

2. Navigate to **Catalog** > **Manage** (the gear symbol) > select **Connections**.

   ![Databricks Catalog Explorer showing the left sidebar with Catalog selected, the gear icon in the Catalog header, and the Connections option highlighted in the dropdown menu](images/databricks-catalog-connections-nav.png)

3. Click **Create Connection**.

4. **Setup Connection Basics:**
   - Connection Name
   - Connection Type = `HTTP`
   - Auth Type = `OAuth Machine to Machine`
   - Click **Next**

   ![Databricks Edit Connection dialog showing Step 1 — Connection Basics — with fields for Connection name, Connection type set to HTTP, and Auth type set to OAuth Machine to Machine](images/step1-connection-basics.png)

5. **Setup Authentication:**
   - Host = `https://plus.dnb.com`
   - Port = `443`
   - Leave OAuth Scope empty.
   - Client Secret (provided via email from D&B)
   - Client ID (key provided via email from D&B)
   - Click **Next**

   ![Databricks Edit Connection dialog showing Step 2 — Authentication — with fields for Host, Port (443), OAuth scope, Client secret, and Client ID](images/step2-authentication.png)

6. **Setup Connection Details:**
   - Token endpoint = `https://plus.dnb.com/v2/mcp/oauth/token`
   - Base path = `/v2/mcp`
   - Ensure **Is MCP Connection** is checked.
   - Click **Submit**

   ![Databricks Edit Connection dialog showing Step 3 — Connection Details — with Token endpoint and Base path fields filled in, and the "Is MCP connection" checkbox checked](images/step3-connection-details.png)

---

## Finalize Connection and Add Users

1. Once a connection has been established, your **admin** will need to **Grant** end users with **USE** (required) and **MANAGE** permissions.

2. Click on the new connection created, click **Grant**, and follow the instructions.

   ![Databricks Catalog Explorer showing the Permissions tab of the newly created connection, with Grant and Revoke buttons](images/grant-permissions.png)

---

## Test MCP Connection

1. Navigate to **Playground**.

2. In Playground, select your LLM (GPT-5.2 shown here).

3. Click **+ Add Tool**.

   ![Databricks Add Tools dialog on the MCP Servers tab, showing sections for Unity Catalog Function, Vector Search, Genie Space, External MCP Server, and Custom MCP Server — with the External MCP Server dropdown highlighted](images/add-tools-mcp-servers.png)

4. Select your connection under **External MCP Connection**.

   ![External MCP Server section showing the selected connection "dplus_connection_mcp_mtm" in the dropdown](images/select-external-mcp-connection.png)

5. Select your connection (tool) under **External MCP Connection** in the drop-down menu. Press **Save**.

6. Type in a query and press the chevron.

   ![Databricks Playground input field with the query "Give me the duns number for Gorman Manufacturing"](images/playground-query-input.png)

7. The LLM will call the tools required from the MCP Connection and return not only the raw response, but also the LLM's interpretation of the data.

   ![Databricks Playground showing the raw tool output from the DnBMatchTool, including the JSON request body and a tabular response with DUNS numbers, company names, addresses, and family tree roles for multiple Gorman Manufacturing matches](images/playground-tool-response-raw.png)

   ![The LLM's interpreted response listing four candidate DUNS numbers for "Gorman Manufacturing" in the US, with company names, addresses, and a prompt to confirm which match is correct](images/playground-llm-response.png)
