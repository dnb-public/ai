# MCP Server Setup Guide for Amazon Quick

**Version:** 1.0
**Publication Date:** 2026

## About this Document

The MCP Server Setup Guide provides step-by-step instructions for setting up Dun & Bradstreet's MCP server for your Amazon Quick instance.

---

## Table of Contents

- [Requirements to Connect to Dun & Bradstreet MCP Server](#requirements-to-connect-to-dun--bradstreet-mcp-server)
- [Navigate to MCP Settings](#navigate-to-mcp-settings)
- [Add a New MCP Connection](#add-a-new-mcp-connection)
- [Finalize Connection and Add Users](#finalize-connection-and-add-users)

---

## Requirements to Connect to Dun & Bradstreet MCP Server

Before starting, ensure you have the following:

- An active **Amazon Quick** account
- Access to the **Amazon Quick MCP configuration screen**
- The following credentials provided by **Dun & Bradstreet** as part of your D&B Direct+ and D&B.AI Data Package subscriptions:
  - **Client ID** (provided separately)
  - **Client Secret** (provided separately)
  - **Base URL:** [https://plus.dnb.com/v2/mcp](https://plus.dnb.com/v2/mcp)
  - **OAuth 2.0 Authorization Endpoint:** [https://plus.dnb.com/v2/mcp/authorize](https://plus.dnb.com/v2/mcp/authorize)
  - **OAuth 2.0 Token Endpoint:** [https://plus.dnb.com/v2/mcp/token](https://plus.dnb.com/v2/mcp/token)
- Network access that allows outbound calls to D&B endpoints

---

## Navigate to MCP Settings

1. Log into [Amazon Quick](https://quicksight.aws.amazon.com/).

2. Navigate to **Connectors** > **Available**.

3. In the search bar within the Connectors tab, enter "Dun & Bradstreet" and select the appropriate tile.

   ![Amazon Quick Connectors page showing the "Available" tab with a search bar and a grid of connector tiles including Airtable, Box Agent, Dropbox, Gmail, Google Analytics, Google Calendar, Google Docs, Google Drive, Google Meet, Google Sheets, Google Slides, and Microsoft Outlook — each with a "Connect" button](images/connectors-available.png)

---

## Add a New MCP Connection

1. Click **Connect** on the Dun & Bradstreet tile to begin the setup process.

2. If prompted, fill out the following required fields:
   - **Base URL** — `https://plus.dnb.com/v2/mcp`
   - **Client ID** (provided via email from D&B)
   - **Client Secret** (provided via email from D&B)
   - **Token URL** — `https://plus.dnb.com/v2/mcp/token`
   - **Authorization URL** — `https://plus.dnb.com/v2/mcp/authorize`

   ![Model Context Protocol "Authenticate" step showing the Create integration dialog with Authentication method (User authentication), OAuth Configuration (Custom user based OAuth), and fields for Client ID, Client secret, Token URL, and Authorization URL — with a "Create and continue" button](images/authenticate-credentials.png)

---

## Finalize Connection and Add Users

1. Once a connection has been established, you will be prompted to continue to the tools page.

2. Review the list of available tools within the MCP server. Click **Next** to proceed.

   > **Note:** All tools will be listed within this page, including tools for Dun & Bradstreet Data Blocks that you may not have an active subscription for. To add additional Data Blocks to your subscription, contact your Dun & Bradstreet representative.

   ![Model Context Protocol summary page showing Connection Details on the left (status: Signed in, connector: Model Context Protocol, Base URL, Authorization URL, Token URL, etc.) and Enabled Actions on the right listing 13 tools including BusinessEventsTool, ContactTool, DataTool, DiversityTool, ESGTool, InquiryTool, MatchTool, PaymentTool, RiskTool, RumSpendTool, SalesAndMarketingTool, SBRITool, and ShippingTool](images/review-tools.png)

3. You will now be prompted to select which team members will have access to the Dun & Bradstreet MCP Connector. You can choose to entitle all users within your organization, or select from specific teams and groups.

   ![Model Context Protocol "Publish" step showing options to choose who can access the connection — a toggle for "Everyone in your organization" and a search field for specific groups — with Back, Cancel, and Publish buttons](images/publish-connection.png)

4. Click **Publish** to finalize.

5. You have now completed your connection to Dun & Bradstreet's MCP server. For additional support, visit [service.dnb.com](https://service.dnb.com) or contact your Dun & Bradstreet representative.
