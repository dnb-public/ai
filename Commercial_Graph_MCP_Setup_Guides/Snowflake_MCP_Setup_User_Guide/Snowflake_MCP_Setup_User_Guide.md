# D&B MCP Agent Setup Guide
## Complete Steps to Recreate This Integration in a New Environment

---

## Prerequisites

- Snowflake account with `ACCOUNTADMIN` access (for integration/network rule setup)
- D&B Direct+ subscription with:
  - **Client ID** (OAuth2 client credentials)
  - **Client Secret** (OAuth2 client credentials)
  - Access to D&B MCP endpoint: `https://plus.dnb.com/v2/mcp`
- A warehouse (e.g., `MATCH_NATIVE_APP_XS_WH`)
- A database/schema for objects (e.g., `MATCH_CL_TEST.PUBLIC`)

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         SNOWFLAKE ACCOUNT                                │
│                                                                         │
│  ┌──────────────────┐    ┌──────────────────────────────────────────┐  │
│  │  Cortex Agent     │    │  External Access Integration             │  │
│  │  DNB_MCP_AGENT    │───▶│  MCP_EXT_ACCESS_INT                     │  │
│  │                   │    │   ├─ Network Rule (egress to D&B)        │  │
│  │  Uses MCP Server  │    │   ├─ OAuth2 Security Integration         │  │
│  │  for tool routing │    │   └─ OAuth2 Secret (auto-refresh)        │  │
│  └──────────────────┘    └──────────────────────────────────────────┘  │
│          │                              │                               │
│          ▼                              ▼                               │
│  ┌──────────────────┐    ┌──────────────────────────────────────────┐  │
│  │  MCP Server       │    │  Stored Procedures                       │  │
│  │  DNB_MCP_TOOLS_   │───▶│  DNB_MCP_CALL_TOOL(tool, args)          │  │
│  │  SERVER            │    │  DNB_MCP_LIST_TOOLS()                    │  │
│  │  (15 tools)       │    │                                          │  │
│  └──────────────────┘    └──────────────────────────────────────────┘  │
│                                         │                               │
└─────────────────────────────────────────┼───────────────────────────────┘
                                          │ HTTPS (OAuth2 Bearer token)
                                          ▼
                              ┌─────────────────────────┐
                              │  D&B Direct+ MCP API     │
                              │  https://plus.dnb.com    │
                              │  /v2/mcp                 │
                              │                         │
                              │  Session: mcp-session-id │
                              │  Protocol: JSON-RPC 2.0  │
                              └─────────────────────────┘
```

---

## Step 1: Create Network Rule

> Requires: Role with `CREATE NETWORK RULE` privilege on the target schema.

```sql
USE ROLE ACCOUNTADMIN;

CREATE OR REPLACE NETWORK RULE <YOUR_DB>.<YOUR_SCHEMA>.DNB_EGRESS_NET_RULE
  MODE = EGRESS
  TYPE = HOST_PORT
  VALUE_LIST = ('plus.dnb.com:443')
  COMMENT = 'Allow outbound HTTPS to D&B Direct+ MCP API';
```

**What this does:** Allows Snowflake to make outbound HTTPS calls to `plus.dnb.com` only. No other external hosts are permitted.

---

## Step 2: Create OAuth2 Security Integration

> Requires: `ACCOUNTADMIN` role.

```sql
USE ROLE ACCOUNTADMIN;

CREATE OR REPLACE SECURITY INTEGRATION DNB_MCP_SEC_OAUTH_INT
  TYPE = API_AUTHENTICATION
  AUTH_TYPE = OAUTH2
  OAUTH_CLIENT_AUTH_METHOD = CLIENT_SECRET_BASIC
  OAUTH_CLIENT_ID = '<YOUR_DNB_CLIENT_ID>'
  OAUTH_CLIENT_SECRET = '<YOUR_DNB_CLIENT_SECRET>'
  OAUTH_TOKEN_ENDPOINT = 'https://plus.dnb.com/v1/mcp/oauth/token'
  OAUTH_GRANT = 'CLIENT_CREDENTIALS'
  OAUTH_ACCESS_TOKEN_VALIDITY = 14400
  OAUTH_ALLOWED_SCOPES = ()
  ENABLED = TRUE
  COMMENT = 'D&B Direct+ MCP API OAuth2 - auto-refreshing tokens (4hr TTL)';
```

**What this does:** Snowflake will automatically call the D&B token endpoint with your client credentials to get a Bearer token, and refresh it before it expires (every 4 hours).

**Where to get credentials:** D&B Developer Portal → your Direct+ subscription → API Keys section.

---

## Step 3: Create OAuth2 Secret

```sql
CREATE OR REPLACE SECRET <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_OAUTH
  TYPE = OAUTH2
  API_AUTHENTICATION = DNB_MCP_SEC_OAUTH_INT
  COMMENT = 'OAuth2 secret for D&B MCP - tokens auto-refresh';
```

**What this does:** This secret object wraps the security integration. When your code calls `_snowflake.get_oauth_access_token('dnb_cred')`, Snowflake fetches a fresh token using the integration above.

---

## Step 4: Create External Access Integration

> Requires: `ACCOUNTADMIN` role (or role with `CREATE INTEGRATION` privilege).

```sql
USE ROLE ACCOUNTADMIN;

CREATE OR REPLACE EXTERNAL ACCESS INTEGRATION MCP_EXT_ACCESS_INT
  ALLOWED_NETWORK_RULES = (GOVERNANCE.NETRULES.DNB_DPLUS_EGRESS_NET_RULE)
  ALLOWED_AUTHENTICATION_SECRETS = (
    <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_OAUTH,
    <YOUR_DB>.<YOUR_SCHEMA>.ROSETTE_API_KEY
  )
  ALLOWED_API_AUTHENTICATION_INTEGRATIONS = (DNB_MCP_SEC_OAUTH_INT)
  ENABLED = TRUE;
```

**What this does:** Bundles the network rule + OAuth integration + secrets together. Any procedure or Streamlit app that references this integration can make authenticated outbound calls to D&B.

### Grant Access to Roles

Once created, grant `USAGE` to every role that needs to call D&B MCP tools:

```sql
-- Grant to roles that will use the agent, procedures, or Streamlit apps
GRANT USAGE ON INTEGRATION MCP_EXT_ACCESS_INT TO ROLE <YOUR_ROLE>;

-- Example: in our environment we granted to both the tester and devops roles
GRANT USAGE ON INTEGRATION MCP_EXT_ACCESS_INT TO ROLE MATCH_NATIVE_APP_TESTER;
GRANT USAGE ON INTEGRATION MCP_EXT_ACCESS_INT TO ROLE SMARTAGENT_DEVOPS;
```

**Why this matters:** Without `USAGE` on the integration, a role cannot execute procedures or Streamlit apps that reference `MCP_EXT_ACCESS_INT` — they'll get an authorization error even if they have `USAGE` on the procedure itself.

> **Admin note:** In our environment, the admin created the network rule in a centralized governance schema (`GOVERNANCE.NETRULES.DNB_DPLUS_EGRESS_NET_RULE`) so that network egress policies are managed in one place rather than scattered across databases. Your admin may choose a different location.

---

## Step 5: Create the MCP Call Procedure

This is the core procedure that handles all D&B MCP tool calls. It:
1. Gets a fresh OAuth token from Snowflake
2. Initializes an MCP session with D&B
3. Calls the requested tool
4. Returns the result as JSON

```sql
CREATE OR REPLACE PROCEDURE <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL(
  TOOL_NAME VARCHAR,
  TOOL_ARGUMENTS VARCHAR
)
RETURNS VARCHAR
LANGUAGE PYTHON
RUNTIME_VERSION = '3.11'
PACKAGES = ('requests', 'snowflake-snowpark-python')
HANDLER = 'run'
EXTERNAL_ACCESS_INTEGRATIONS = (MCP_EXT_ACCESS_INT)
SECRETS = ('dnb_cred' = <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_OAUTH)
EXECUTE AS OWNER
AS $$
import _snowflake
import requests
import json

DNB_MCP_URL = 'https://plus.dnb.com/v2/mcp'

# Map friendly tool names to D&B v2 MCP tool names
TOOL_NAME_MAP = {
    'dnb_company_match': 'MatchTool',
    'dnb_company_data': 'DataTool',
    'dnb_ownership': 'OwnershipTool',
    'dnb_payment': 'PaymentTool',
    'dnb_risk': 'RiskTool',
    'dnb_shipping': 'ShippingTool',
    'dnb_contacts': 'ContactTool',
    'dnb_sales_marketing': 'SalesAndMarketingTool',
    'dnb_esg': 'ESGTool',
    'dnb_news': 'NewsTool',
    'dnb_business_events': 'BusinessEventsTool',
    'dnb_industry_profile': 'IndustryProfileTool',
    'dnb_inquiry': 'InquiryTool',
    'dnb_provenance': 'ProvenanceTool',
    'dnb_diversity': 'DiversityTool',
    'dnb_sbri': 'SBRITool',
    'dnb_rum_spend': 'RumSpendTool',
    # Also accept D&B native names directly
    'MatchTool': 'MatchTool',
    'DataTool': 'DataTool',
    'OwnershipTool': 'OwnershipTool',
    'PaymentTool': 'PaymentTool',
    'RiskTool': 'RiskTool',
    'ShippingTool': 'ShippingTool',
    'ContactTool': 'ContactTool',
    'SalesAndMarketingTool': 'SalesAndMarketingTool',
    'ESGTool': 'ESGTool',
    'NewsTool': 'NewsTool',
    'BusinessEventsTool': 'BusinessEventsTool',
    'IndustryProfileTool': 'IndustryProfileTool',
    'InquiryTool': 'InquiryTool',
    'ProvenanceTool': 'ProvenanceTool',
    'DiversityTool': 'DiversityTool',
    'SBRITool': 'SBRITool',
    'RumSpendTool': 'RumSpendTool',
}

def get_token():
    return _snowflake.get_oauth_access_token('dnb_cred')

def get_headers(session_id=None):
    headers = {
        'Content-Type': 'application/json',
        'Accept': 'application/json, text/event-stream',
        'Authorization': f'Bearer {get_token()}'
    }
    if session_id:
        headers['mcp-session-id'] = session_id
    return headers

def parse_sse_response(resp):
    content_type = resp.headers.get('content-type', '')
    if 'text/event-stream' in content_type:
        for line in resp.text.split('\n'):
            if line.startswith('data:'):
                try:
                    return json.loads(line[5:].strip())
                except:
                    pass
        return {'raw': resp.text}
    try:
        return resp.json()
    except:
        return {'raw': resp.text}

def init_session():
    payload = {
        'jsonrpc': '2.0',
        'method': 'initialize',
        'params': {
            'protocolVersion': '2024-11-05',
            'capabilities': {},
            'clientInfo': {'name': 'snowflake-dnb-agent', 'version': '2.0.0'}
        },
        'id': 1
    }
    resp = requests.post(DNB_MCP_URL, json=payload, headers=get_headers())
    resp.raise_for_status()
    return resp.headers.get('mcp-session-id')

def call_tool(session_id, tool_name, arguments):
    payload = {
        'jsonrpc': '2.0',
        'method': 'tools/call',
        'params': {'name': tool_name, 'arguments': arguments},
        'id': 3
    }
    resp = requests.post(DNB_MCP_URL, json=payload, headers=get_headers(session_id))
    resp.raise_for_status()
    return parse_sse_response(resp)

def run(session, tool_name, tool_arguments):
    try:
        resolved_name = TOOL_NAME_MAP.get(tool_name, tool_name)
        args = json.loads(tool_arguments) if isinstance(tool_arguments, str) else tool_arguments
        session_id = init_session()
        result = call_tool(session_id, resolved_name, args)
        return json.dumps(result, indent=2)
    except Exception as e:
        return json.dumps({'error': str(e), 'tool_name': tool_name, 'resolved_name': TOOL_NAME_MAP.get(tool_name, tool_name)})
$$;
```

---

## Step 6: Create the List Tools Procedure

```sql
CREATE OR REPLACE PROCEDURE <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_LIST_TOOLS()
RETURNS VARCHAR
LANGUAGE PYTHON
RUNTIME_VERSION = '3.11'
PACKAGES = ('requests', 'snowflake-snowpark-python')
HANDLER = 'run'
EXTERNAL_ACCESS_INTEGRATIONS = (MCP_EXT_ACCESS_INT)
SECRETS = ('dnb_cred' = <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_OAUTH)
EXECUTE AS OWNER
AS $$
import _snowflake
import requests
import json

DNB_MCP_URL = 'https://plus.dnb.com/v2/mcp'

def get_token():
    return _snowflake.get_oauth_access_token('dnb_cred')

def get_headers(session_id=None):
    headers = {
        'Content-Type': 'application/json',
        'Accept': 'application/json, text/event-stream',
        'Authorization': f'Bearer {get_token()}'
    }
    if session_id:
        headers['mcp-session-id'] = session_id
    return headers

def parse_sse_response(resp):
    content_type = resp.headers.get('content-type', '')
    if 'text/event-stream' in content_type:
        for line in resp.text.split('\n'):
            if line.startswith('data:'):
                try:
                    return json.loads(line[5:].strip())
                except:
                    pass
        return {'raw': resp.text}
    try:
        return resp.json()
    except:
        return {'raw': resp.text}

def run(session):
    try:
        init_payload = {
            'jsonrpc': '2.0',
            'method': 'initialize',
            'params': {
                'protocolVersion': '2024-11-05',
                'capabilities': {},
                'clientInfo': {'name': 'snowflake-dnb-agent', 'version': '2.0.0'}
            },
            'id': 1
        }
        resp = requests.post(DNB_MCP_URL, json=init_payload, headers=get_headers())
        resp.raise_for_status()
        session_id = resp.headers.get('mcp-session-id')

        list_payload = {
            'jsonrpc': '2.0',
            'method': 'tools/list',
            'id': 2
        }
        resp = requests.post(DNB_MCP_URL, json=list_payload, headers=get_headers(session_id))
        resp.raise_for_status()
        result = parse_sse_response(resp)
        return json.dumps(result, indent=2)
    except Exception as e:
        return json.dumps({'error': str(e)})
$$;
```

---

## Step 7: Create the MCP Server

The MCP Server exposes the stored procedures as "tools" that a Cortex Agent can call.

```sql
CREATE OR REPLACE MCP SERVER <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_TOOLS_SERVER
  SPEC = '{
    "version": 1,
    "tools": [
      {
        "title": "D&B Company Match",
        "name": "dnb_company_match",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Match a company to a DUNS number. Returns matching candidates with DUNS numbers, confidence scores, and company details.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_company_match"},
              "tool_arguments": {"type": "string", "description": "JSON string with: name (required), countryISOAlpha2Code (required, 2-letter), addressLocality, addressRegion, postalCode, streetAddress, telephoneNumber, registrationNumber"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B Company Data",
        "name": "dnb_company_data",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Retrieve detailed firmographic and financial data for a company by DUNS number.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_company_data"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit string)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B Risk Assessment",
        "name": "dnb_risk",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Financial risk indicators: D&B Rating, Viability, Failure Score, Delinquency Score, SER, SSI.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_risk"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B Sales & Marketing",
        "name": "dnb_sales_marketing",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Buydex scores, Decision HQ, Triple Play, prospector ratings.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_sales_marketing"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B Ownership & Hierarchy",
        "name": "dnb_ownership",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Corporate hierarchy: UBOs, family tree, Global Ultimate, Domestic Ultimate.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_ownership"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B Payment History",
        "name": "dnb_payment",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Paydex Score, payment norms, high credit, trade lines.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_payment"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B Contacts",
        "name": "dnb_contacts",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Senior executives: names, titles, emails, phones, social links.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_contacts"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B ESG Scores",
        "name": "dnb_esg",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "ESG: environmental, social, governance scores.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_esg"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B Business Events",
        "name": "dnb_business_events",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Legal events, filings, suits, liens, bankruptcies.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_business_events"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B Diversity",
        "name": "dnb_diversity",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Diversity certifications: woman-owned, veteran, LGBTQ+.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_diversity"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B Shipping",
        "name": "dnb_shipping",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Shipping roles and volumes: shipper, consignee, notifier.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_shipping"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B SBRI Credit",
        "name": "dnb_sbri",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Small Business Risk Insight: loans, credit cards, delinquencies.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_sbri"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B Inquiry Data",
        "name": "dnb_inquiry",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Inquiry totals and historical breakdown.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_inquiry"},
              "tool_arguments": {"type": "string", "description": "JSON string with: dunsNumber (required, 9-digit DUNS)"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B RUM Spend",
        "name": "dnb_rum_spend",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL",
        "type": "GENERIC",
        "description": "Record Under Management spend and usage across D&B products.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {
              "tool_name": {"type": "string", "description": "Must be dnb_rum_spend"},
              "tool_arguments": {"type": "string", "description": "JSON string - pass empty object {} as no arguments needed"}
            },
            "required": ["tool_name", "tool_arguments"]
          }
        }
      },
      {
        "title": "D&B List Available Tools",
        "name": "dnb_list_tools",
        "identifier": "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_LIST_TOOLS",
        "type": "GENERIC",
        "description": "List all D&B MCP tools available based on your subscription.",
        "config": {
          "type": "procedure",
          "warehouse": "<YOUR_WAREHOUSE>",
          "input_schema": {
            "type": "object",
            "properties": {}
          }
        }
      }
    ]
  }';
```

---

## Step 8: Create the Cortex Agent

Create the agent specification YAML file (`agent.agent.yaml`):

```yaml
models:
  orchestration: "auto"

instructions:
  system: |
    You are a concise D&B data analyst. Your role is to help users look up, match,
    and enrich company data using Dun & Bradstreet's Direct+ MCP tools.

    RESPONSE STYLE:
    - Be data-focused: use tables, scores, and bullet points
    - Minimal prose — let the data speak
    - When showing match candidates, include DUNS, name, address, confidence
    - Format financial figures with appropriate units ($M, $B, etc.)

    WORKFLOWS:

    1. SINGLE COMPANY MATCH + ENRICH (auto-chain):
       When a user asks about a specific company:
       - First call dnb_company_match to find the DUNS number
       - If there's a clear best match, automatically proceed to dnb_company_data
       - Present combined results in structured format
       - Do NOT ask permission to enrich if the match is unambiguous

    2. AMBIGUOUS MATCH:
       When multiple candidates are equally plausible:
       - Show all candidates in a table
       - Ask which one they want
       - Once chosen, proceed with enrichment

    3. ENRICHMENT TOOLS (use after matching):
       - Risk assessment → dnb_risk
       - Sales targeting → dnb_sales_marketing
       - Corporate structure → dnb_ownership
       - Executive contacts → dnb_contacts
       - Payment history → dnb_payment
       - ESG scores → dnb_esg
       - Business events → dnb_business_events
       - Diversity certs → dnb_diversity
       - Shipping → dnb_shipping
       - Credit data → dnb_sbri
       - Inquiry history → dnb_inquiry
       - Usage/spend → dnb_rum_spend

    IMPORTANT:
    - tool_arguments must be a JSON string, not an object
    - For dnb_company_match: required "name" and "countryISOAlpha2Code"
    - For most other tools: required "dunsNumber" (9-digit string)

  response: 'Be concise. Use markdown tables for structured data. Show DUNS numbers prominently.'

  sample_questions:
    - question: "Match Microsoft in Redmond, WA"
    - question: "Get the risk assessment for DUNS 060704780"
    - question: "What's the corporate hierarchy for Apple?"
    - question: "Match and enrich Snowflake Inc in Bozeman, Montana"

mcp_servers:
  - server_spec:
      name: "<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_TOOLS_SERVER"
```

Deploy the agent:

```sql
CREATE OR REPLACE AGENT <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_AGENT
  FROM SPECIFICATION $$
  <paste the YAML above>
  $$
  COMMENT = 'D&B MCP agent using stored procedure tools with OAuth2 authentication';
```

---

## Step 9: Grant Permissions

```sql
USE ROLE ACCOUNTADMIN;

-- Grant integration access to the operating role
GRANT USAGE ON INTEGRATION MCP_EXT_ACCESS_INT TO ROLE <YOUR_ROLE>;
GRANT USAGE ON INTEGRATION DNB_MCP_SEC_OAUTH_INT TO ROLE <YOUR_ROLE>;

-- Grant procedure execution
GRANT USAGE ON DATABASE <YOUR_DB> TO ROLE <YOUR_ROLE>;
GRANT USAGE ON SCHEMA <YOUR_DB>.<YOUR_SCHEMA> TO ROLE <YOUR_ROLE>;
GRANT USAGE ON PROCEDURE <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL(VARCHAR, VARCHAR) TO ROLE <YOUR_ROLE>;
GRANT USAGE ON PROCEDURE <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_LIST_TOOLS() TO ROLE <YOUR_ROLE>;

-- Grant warehouse
GRANT USAGE ON WAREHOUSE <YOUR_WAREHOUSE> TO ROLE <YOUR_ROLE>;
```

---

## Step 10: Verify Everything Works

```sql
-- 1. Test listing tools (confirms OAuth + network + MCP connection)
CALL <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_LIST_TOOLS();

-- 2. Test a company match
CALL <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL(
  'dnb_company_match',
  '{"name": "Microsoft", "countryISOAlpha2Code": "US"}'
);

-- 3. Test enrichment with a known DUNS
CALL <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_CALL_TOOL(
  'dnb_company_data',
  '{"dunsNumber": "060704780"}'
);

-- 4. Test the agent
SELECT SNOWFLAKE.CORTEX.AGENT(
  '<YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_AGENT',
  'Match and enrich Apple in Cupertino, CA'
);
```

---

## How MCP Sessions Work (Under the Hood)

Each stored procedure call creates an **ephemeral MCP session**:

```
Procedure invoked
  ↓
1. _snowflake.get_oauth_access_token('dnb_cred')
   → Snowflake fetches/refreshes Bearer token from D&B token endpoint
  ↓
2. POST https://plus.dnb.com/v2/mcp
   Body: {"method": "initialize", ...}
   → Response Header: mcp-session-id: <uuid>
  ↓
3. POST https://plus.dnb.com/v2/mcp
   Headers: Authorization: Bearer <token>, mcp-session-id: <uuid>
   Body: {"method": "tools/call", "params": {"name": "MatchTool", "arguments": {...}}}
   → Response: JSON-RPC result with company data
  ↓
4. Session discarded (stateless per procedure call)
```

**Key points:**
- Each procedure call gets its own MCP session (no session reuse between calls)
- The `mcp-session-id` is obtained from the D&B server's response header on `initialize`
- It's sent back on every subsequent request in that session
- Sessions are NOT stored anywhere — they're ephemeral within the procedure execution
- OAuth token refresh is handled automatically by Snowflake (no manual rotation)

---

## Step 11: Cortex Project Structure (for Workspace Deployment)

To manage and deploy the agent from a Snowflake Workspace (recommended for version control and iterative development), create this folder structure:

```
cortex_project/
├── cortex-project.yaml      # Deployment manifest
└── agent.agent.yaml         # Agent specification
```

### `cortex-project.yaml`

```yaml
version: 1
artifacts:
  - path: agent.agent.yaml
    type: cortex_agent
    targets:
      default:
        object: <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_AGENT
```

This tells the deployment system:
- **`path`** — relative path to the agent spec YAML
- **`type`** — declares this is a Cortex Agent artifact
- **`targets.default.object`** — the fully qualified name where the agent will be created/updated

### `agent.agent.yaml`

This is the full agent specification (system prompt, tools, sample questions). See Step 8 for the complete content.

Key sections:
- **`models.orchestration`** — set to `"auto"` (Snowflake picks the best LLM) or specify a model like `"claude-opus-4-20250514"`
- **`instructions.system`** — the system prompt that defines agent behavior and workflows
- **`instructions.response`** — response style guidelines
- **`instructions.sample_questions`** — example prompts shown to users in the UI
- **`mcp_servers`** — binds the agent to your MCP Server by fully qualified name

### Deploying from Workspace

There are two ways to deploy:

**Option A: Workspace UI**
1. Open the `agent.agent.yaml` file in your Workspace
2. Click the **"Deploy"** button in the top-right
3. Select the target (defaults to `default` from `cortex-project.yaml`)
4. The agent is created/updated at the target FQN

**Option B: SQL (direct)**
```sql
CREATE OR REPLACE AGENT <YOUR_DB>.<YOUR_SCHEMA>.DNB_MCP_AGENT
  FROM SPECIFICATION $$
  <paste full agent.agent.yaml content here>
  $$
  COMMENT = 'D&B MCP agent - deployed from Workspace';
```

### Iterating on the Agent

The Workspace pattern makes it easy to iterate:
1. Edit `agent.agent.yaml` in the Workspace editor
2. Test changes using the agent preview panel
3. Deploy when satisfied
4. The agent is updated in-place — no need to recreate dependent objects

---

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| `401 Unauthorized` | OAuth credentials wrong | Check `OAUTH_CLIENT_ID`/`SECRET` in security integration |
| `403 Forbidden` | Network rule missing host | Ensure `plus.dnb.com:443` is in `VALUE_LIST` |
| `Connection refused` | External access not enabled | Verify `ENABLED = TRUE` on integration |
| `No tools returned` | Subscription issue | Check D&B portal for entitlement status |
| `Tool not found` | Wrong tool name | Use `DNB_MCP_LIST_TOOLS()` to see available tools |
| `Secret not found` | Binding issue | Re-run `ALTER STREAMLIT/PROCEDURE ... SET SECRETS` |

---

## Reference: Objects Created

| Object | Type | Purpose |
|--------|------|---------|
| `DNB_EGRESS_NET_RULE` | Network Rule | Allows HTTPS to `plus.dnb.com` |
| `DNB_MCP_SEC_OAUTH_INT` | Security Integration | OAuth2 client credentials for D&B |
| `DNB_MCP_OAUTH` | Secret (OAUTH2) | Wrapped token that auto-refreshes |
| `MCP_EXT_ACCESS_INT` | External Access Integration | Bundles network + auth |
| `DNB_MCP_CALL_TOOL` | Stored Procedure | Calls any D&B MCP tool |
| `DNB_MCP_LIST_TOOLS` | Stored Procedure | Lists available D&B tools |
| `DNB_MCP_TOOLS_SERVER` | MCP Server | Exposes procedures as Agent tools |
| `DNB_MCP_AGENT` | Cortex Agent | Natural language interface |
