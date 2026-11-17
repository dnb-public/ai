# MCP Server Setup Guide for IBM WatsonX Orchestrate

**Version:** 1.0
**Publication Date:** 2026

## About this Document

The MCP Server Setup Guide provides step-by-step instructions for setting up Dun & Bradstreet's MCP server for your IBM WatsonX Orchestrate instance.

---

## Table of Contents

- [Requirements to Connect to Dun & Bradstreet MCP Server](#requirements-to-connect-to-dun--bradstreet-mcp-server)
- [Create an MCP Connection](#create-an-mcp-connection)
- [Add MCP Tool to Agent and Connect](#add-mcp-tool-to-agent-and-connect)
- [Test MCP Connection](#test-mcp-connection)

---

## Requirements to Connect to Dun & Bradstreet MCP Server

Before starting, ensure you have the following:

- An active **IBM WatsonX Orchestrate** account
- The following credentials provided by **Dun & Bradstreet** as part of your D&B Direct+ and D&B.AI Data Package subscriptions:
  - **Client ID** (provided separately)
  - **Client Secret** (provided separately)
  - **OAuth Token Endpoint:** [https://plus.dnb.com/v1/mcp/token](https://plus.dnb.com/v1/mcp/token)
- **Token** obtained via Postman or another application (token expires after four hours)
- The ability to create an agent within WatsonX Orchestrate

---

## Create an MCP Connection

1. Log into WatsonX Orchestrate.

2. Create an agent "from scratch".

3. Next, create a connection. Navigate to the hamburger menu: **Hamburger** > **Manage** > select **Security**.

   ![IBM WatsonX Orchestrate sidebar navigation with the hamburger menu expanded, showing Manage expanded with a red arrow pointing to the Security option underneath](images/nav-manage-security.png)

4. Click **Add Connection**.

   ![Security control center page showing the Connections tab selected with red arrows pointing to the Connections tab and the "Add connection" button on the right](images/security-add-connection.png)

5. **Setup Connection Basics:**
   - Connection Name
   - Connection Display Name
   - Connection description
   - Click **Save and continue**

   ![Add connection dialog on the "Define connection details" step, with fields for Connection ID ("DNB_MCP_Connection"), Display name ("My D&B MCP Connection"), and Connection description, with a red arrow pointing to the "Save and continue" button](images/define-connection-details.png)

6. **Setup Draft Environment:**
   - Authentication Type = `Bearer Token`
   - Credential Type = `Team Credentials`
   - Bearer Token = your token
   - Click **Next**

   ![Add connection dialog on the "Configure draft environment" step, with red arrows pointing to Authentication type set to "Bearer Token", Credential type set to "Team credentials", and the Bearer token input field](images/configure-draft-environment.png)

7. **Setup Live Environment:**
   - Authentication Type = `Bearer Token`
   - Credential Type = `Team Credentials`
   - Bearer Token = your token
   - Click **Finish**

   ![Add connection dialog on the "Configure live environment" step, with the same Bearer Token and Team credentials configuration as the draft environment, and a "Finish" button](images/configure-live-environment.png)

---

## Add MCP Tool to Agent and Connect

1. Once a connection has been established, return to **Edit** your agent.

2. Click **Add Tool**.

   ![Agent editor for "Company ABC demo agent" showing the Toolset section with a red arrow pointing to the "Add tool" button](images/agent-add-tool.png)

3. Select **Catalog**.

   ![Add a tool dialog showing options to create new (Agentic workflow) or add from Catalog, Local instance, MCP server, or OpenAPI — with a red arrow pointing to Catalog](images/add-tool-catalog.png)

4. Select the **D&B Direct+ MCP Server** tool.

   ![Add tools catalog showing MCP servers including Athenium Weather Intelligence, Box MCP Server, and D&B Direct+ MCP Server — with a red arrow pointing to the D&B tile](images/catalog-dnb-mcp-server.png)

5. Click **Add to Agent**.

   ![D&B Direct+ MCP Server detail page showing the overview, description, categories (Sales, Productivity, Research), and a red arrow pointing to the "Add to agent" button](images/dnb-mcp-server-add-to-agent.png)

6. Select the connection you just created and click **Connect**.

   ![Add an MCP server dialog showing server name, description, MCP server URL, transport type (Streamable HTTP), and a red arrow pointing to the "Select Connection" dropdown set to "My D&B MCP Connection" and the "Connect" button](images/add-mcp-server-select-connection.png)

7. **Select the specific tools that the MCP uses.** Depending on the use case and the Data Blocks you are entitled to, this could vary. For this demonstration, all tools are selected.

8. Click **Add to Agent**.

   ![Add tools and manage MCP servers dialog showing 16 tools selected from the D&B MCP server, including DnBBusinessEventsTool, DnBContactTool, DnBDataTool, DnBDiversityTool, DnBESGTool, and DnBIndustryProfileTool — with red arrows pointing to the checkboxes and the "Add to agent" button](images/select-tools-add-to-agent.png)

---

## Test MCP Connection

1. You should now see all the tools added under **Tools**. Ask WatsonX a question — for example: *"Can you give me the DUNS number for Gorman Manufacturing and the address?"*

   ![Agent editor showing the full Toolset with D&B MCP tools listed (DnBBusinessEventsTool, DnBContactTool, DnBDataTool, etc.) on the left, and the Preview chat panel on the right with the query entered in the input field](images/agent-tools-and-query.png)

2. The LLM will call the tools required from the MCP and return the LLM's interpretation of the data.

   ![WatsonX Orchestrate chat response showing a formatted table of D-U-N-S numbers and primary addresses matching "Gorman Manufacturing" in the United States, with four results and a follow-up prompt asking the user to specify a location](images/llm-response.png)

3. If you would like to see what happened in the background, toggle **Show Reasoning** and you will see the tool(s) called, as well as the raw JSON response.

   ![Show Reasoning panel expanded, displaying Step 1 with the DnBMatchTool call, the JSON input parameters (name: "Gorman Manufacturing", countryISOAlpha2Code: "US"), and the raw text output with DUNS numbers and company data](images/show-reasoning-raw-json.png)
