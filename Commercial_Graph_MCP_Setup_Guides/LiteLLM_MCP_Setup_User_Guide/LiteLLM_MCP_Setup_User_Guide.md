# MCP Server Setup Guide for LiteLLM

**Version:** 1.0
**Publication Date:** 2026

## About this Document

The MCP Server Setup Guide provides step-by-step instructions for setting up Dun & Bradstreet's MCP server for a LiteLLM integration.

---

## Table of Contents

- [Requirements to Connect to Dun & Bradstreet MCP Server](#requirements-to-connect-to-dun--bradstreet-mcp-server)
- [Create an MCP Connection](#create-an-mcp-connection)
- [Test MCP Connection](#test-mcp-connection)

---

## Requirements to Connect to Dun & Bradstreet MCP Server

Before starting, ensure you have the following:

- An active **LiteLLM** account and the standalone application
- The following credentials provided by **Dun & Bradstreet** as part of your D&B Direct+ and D&B.AI Data Package subscriptions:
  - **Client ID** (provided separately)
  - **Client Secret** (provided separately)

---

## Create an MCP Connection

1. Click **MCP Servers** and click the **Add New MCP Server** button.

2. Fill in the appropriate information:

   - **Transport Type** — Streamable HTTP
   - **MCP Server URL** — `https://plus.dnb.com/v2/mcp`
   - **Authentication** — OAuth
   - **OAuth Flow Type** — Interactive (PKCE)

   ![LiteLLM "Add New MCP Server" form showing fields for MCP Server Name ("DirectPlus"), Alias, Description, Logo picker, GitHub/Source URL, Transport Type set to "Streamable HTTP (Recommended)", and MCP Server URL set to "https://plus.dnb.com/v2/mcp"](images/add-new-mcp-server-top.png)

3. Enter your D&B credentials.

   ![LiteLLM Authentication section showing Authentication set to "OAuth", OAuth Flow Type set to "Interactive (PKCE)", masked Client ID and Client Secret fields, empty Scopes field, Authorization URL set to "https://plus.dnb.com/v2/mcp/authorize", and Token URL set to "https://plus.dnb.com/v2/mcp/token"](images/add-new-mcp-server-auth.png)

   ![LiteLLM form continued showing Token Endpoint Auth Method, Registration URL, Token Validation Rules, Token Storage TTL fields, an "Authorize & Fetch Token" button, a Variables section, and a Permission Management / Access Control section](images/add-new-mcp-server-bottom.png)

---

## Test MCP Connection

1. **Check Tool Configuration.** Verify that the tools are enabled for user access under the Tool Configuration section. You should see all tools listed with a green check and the status showing tools enabled for user access.

   ![LiteLLM MCP Servers page showing Tool Configuration with 17 of 17 tools enabled, organized by risk group. The "Read" group (marked Safe) shows 14 of 14 tools allowed, including ContactTool, DataTool, DiversityTool, ESGTool, and IndustryProfileTool — each with descriptions and "on" toggles](images/tool-configuration.png)

2. **Use the Tool Testing Playground to test the server.** Navigate to the DirectPlus server's MCP Tools tab. Select a tool such as MatchTool, enter test parameters (e.g. country code "US" and name "ibm"), and verify a successful result.

   ![LiteLLM Tool Testing Playground showing the DirectPlus MCP server with 17 available tools listed on the left. The MatchTool is selected, with input parameters for countryISOAlpha2Code ("US") and name ("ibm"). The Tool Result panel shows "Tool executed successfully" in 2.03 seconds with a JSON text response containing DUNS numbers and company data for IBM](images/tool-testing-playground.png)
