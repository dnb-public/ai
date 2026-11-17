# Hello, Match & Enrich!

> Your first D&B Client Match & Enrich run — from a tiny CSV to enriched output in minutes.

---

## What you'll learn

By the end of this walkthrough you'll understand the full loop:

1. Build a minimal input CSV.
2. Call **MatchTool** to resolve a company name to a DUNS number.
3. Call **DataTool** to pull firmographic details for that DUNS.
4. Parse the response into a clean set of appended columns.
5. Write the enriched output file.

Think of it as the "Hello, World!" of entity resolution — one row, two MCP calls, zero ambiguity.

---

## Prerequisites

Before you start, make sure you have:

- A connected **D&B Commercial Graph MCP server** (MatchTool and DataTool should appear in your tools list).
- A credential tier of at least **companyinfo_L1** (identity, address, SIC, tickers, registration IDs — revenue and employee counts are *not* included at this tier).

If you're not connected yet, start with the **Commercial Graph MCP Setup Guides** in [Commercial_Graph_MCP_Setup_Guides](./Commercial_Graph_MCP_Setup_Guides/). That folder contains platform-specific walkthroughs for connecting the D&B Commercial Graph MCP server to your environment — for example, Cursor, Databricks, Snowflake, AWS Quick, IBM WatsonX Orchestrate, and LiteLLM. Each guide is available in both **Word (.docx)** and **Markdown (.md)** format; open whichever you prefer. The set of supported platforms may change over time, so browse the folder for the guide that matches your setup.

Everything below assumes the MCP server is live.

---

## Step 0 — Create your "Hello, World!" CSV

Start with a single-row CSV. Save it as `hello_world.csv`:

```csv
source_id,duns,name,streetaddress,city,state,postalCode,country,telephoneNumber,url,email
1,,Apple Inc,1 Apple Park Way,Cupertino,CA,95014,US,,,
```

That's it. The only columns the matcher *requires* are `name` and `country`. Everything else is optional but improves match quality — so we've included address fields to give it a head start.

### Column quick-reference

| Column            | Required? | What it does                                         |
|-------------------|-----------|------------------------------------------------------|
| `source_id`       | No        | Your internal row ID — passed through untouched      |
| `duns`            | No        | Often blank on input; you're here to *get* one       |
| `name`            | **Yes**   | Company name sent to MatchTool                       |
| `country`         | **Yes**   | ISO alpha-2 country code (e.g. `US`, `GB`, `DE`)    |
| `streetaddress`   | No        | Improves match precision                             |
| `city`            | No        | Improves match precision                             |
| `state`           | No        | 2-letter code for US/CA                              |
| `postalCode`      | No        | Used for disambiguation, not sent to MatchTool       |
| `telephoneNumber` | No        | Sent to MatchTool when present                       |
| `url`             | No        | Passthrough only                                     |
| `email`           | No        | Passthrough only                                     |

---

## Step 1 — Match: Resolve the company to a DUNS

Call **MatchTool** with the fields mapped from your CSV row:

```
MatchTool(
  name              = "Apple Inc"
  countryISOAlpha2Code = "US"
  streetAddressLine = "1 Apple Park Way"
  addressLocality   = "Cupertino"
  addressRegion     = "CA"
)
```

### What comes back

MatchTool returns one or more candidate entities, each with a DUNS number, name, and address. For a clear-cut case like Apple's headquarters you'll typically get a single strong match. When there are multiple candidates, apply these disambiguation rules in order:

1. **Geography wins first.** Prefer the candidate whose city/state aligns with your input.
2. **Headquarters over branches.** Prefer a Global Ultimate or Parent/HQ entity over subsidiaries and branch locations.
3. **Common sense for household names.** For well-known public companies, pick the obvious HQ entity even if the input address is a legacy or branch location.
4. **Still can't decide?** Mark the row `ambiguous` and move on.

Record the result:

| Outcome | `match_status` | Next step |
|---------|---------------|-----------|
| One confident match | `matched` | Proceed to DataTool |
| Multiple candidates, none clear | `ambiguous` | Skip DataTool; leave enrichment columns blank |
| No candidates at all | `no_match` | Skip DataTool |
| MCP call failed | `error` | Skip DataTool; continue with remaining rows |

**Pro tip:** If a match fails for a well-known company when you include the full address, retry with just `name` and `country`. Sometimes a stale street address hurts more than it helps.

---

## Step 2 — Enrich: Pull firmographics with DataTool

For every row where `match_status` is `matched`, call **DataTool**:

```
DataTool(
  duns                         = "<matched_duns>"
  show_historical_data         = false
  include_partial_financial_year = false
  block_extended_mode          = false
)
```

DataTool returns a rich block of company data — name, address, industry codes, stock exchange listings, registration numbers, and more.

---

## Step 3 — Parse the response

Now you extract a specific set of fields from the DataTool response and map them to your output columns. Here's what you're looking for and where to find it.

### Business name and address

- **`matched_business_name`** — the Primary Name from the response.
- **`matched_address`** — the street portion of the Primary Address.
- **`matched_city`**, **`matched_state`**, **`matched_zip`**, **`matched_country`** — parsed from the Primary Address string.

Primary Address typically looks like:

```
1 Apple Park Way, Cupertino, California, 95014-0642, United States
```

Split on commas: street is the first segment, city is the second, state is the third (normalize to 2-letter code when possible), zip is the fourth.

### Industry (SIC)

The Primary Industry line comes formatted as `{code}: {description}`, for example:

```
3663: Mfg radio/tv communication equipment
```

- **`matched_sic_code`** — everything before the colon.
- **`matched_sic_description`** — everything after it.

### Stock ticker

Look in the Stock Exchanges list:

1. If a listing has `Is Primary Exchange = True` and the exchange is NASDAQ or NYSE, use that ticker.
2. Otherwise, take the first NASDAQ or NYSE listing you find.
3. If neither exchange appears, leave it blank.

### Registration numbers

Scan the Registration Numbers block:

- **`matched_cage`** — the number where the type is `Commercial And Government Entity Code`.
- **`matched_ein`** — the number where the type is `Federal Taxpayer Identification Number (US)`.

Either or both may be absent — that's fine, just leave them blank.

### Handling missing data

Some fields will come back blank or marked `<<NOTENTITLED|REQUIRE ...>>`. This is expected at the L1 credential tier. Don't retry, don't infer — just leave those output cells empty.

---

## Step 4 — Write the output

Your output CSV keeps every original column unchanged and appends 13 new ones:

```
matched_duns, matched_business_name, matched_address, matched_city,
matched_state, matched_zip, matched_country, matched_sic_code,
matched_sic_description, matched_ticker, matched_cage, matched_ein,
match_status
```

Name the file by appending `-matched-appended` to the input basename:

```
hello_world.csv → hello_world-matched-appended.csv
```

For our single-row example, the output might look like:

```csv
source_id,duns,name,streetaddress,city,state,postalCode,country,telephoneNumber,url,email,matched_duns,matched_business_name,matched_address,matched_city,matched_state,matched_zip,matched_country,matched_sic_code,matched_sic_description,matched_ticker,matched_cage,matched_ein,match_status
1,,Apple Inc,1 Apple Park Way,Cupertino,CA,95014,US,,,,003586395,Apple Inc.,1 Apple Park Way,Cupertino,CA,95014-0642,US,3663,Mfg radio/tv communication equipment,AAPL,,,matched
```

*(DUNS, SIC, and ticker shown above are illustrative — your actual results come from the live MCP response.)*

---

## Step 5 — Check your work

Print a quick status summary:

```
Results for hello_world.csv
─────────────────────────────
Total rows:  1
  matched:   1
  no_match:  0
  ambiguous: 0
  error:     0

Output: hello_world-matched-appended.csv
```

If any rows came back `ambiguous` or `error`, note them with enough context (company name, what went wrong) so you can refine the input and re-run.

---

## Scaling up

That was one row. The real workflow is identical — just loop it:

```
for each row in input CSV:
    1. MatchTool  → get DUNS + match_status
    2. if matched → DataTool → parse appended fields
    3. append columns to row
write full output CSV
report status counts
```

A few things to keep in mind as your file grows:

- **Row order is sacred.** The output must preserve the original row sequence.
- **Original data is read-only.** Never modify the values in the input columns.
- **One bad row shouldn't stop the batch.** If a single MCP call fails, mark that row `error` and keep going.
- **Auth failures are different.** If the MCP server rejects your credentials, stop the whole run — there's no point burning through rows that will all fail.

---

## Quick-reference cheat sheet

### MCP call sequence per row

```
┌─────────┐       ┌──────────┐       ┌──────────┐
│ CSV Row │──────▶│ MatchTool│──────▶│ DataTool │
│         │       │          │       │          │
│ name    │       │ → DUNS   │       │ → Name   │
│ country │       │ → status │       │ → Address│
│ address │       │          │       │ → SIC    │
│ city    │       └──────────┘       │ → Ticker │
│ state   │                          │ → CAGE   │
│ phone   │                          │ → EIN    │
└─────────┘                          └──────────┘
```

### Match status values

| Status      | Meaning                          | DataTool called? |
|-------------|----------------------------------|------------------|
| `matched`   | Single confident DUNS identified | Yes              |
| `no_match`  | No candidates returned           | No               |
| `ambiguous` | Multiple candidates, can't pick  | No               |
| `error`     | MCP call failed                  | No               |

### Output columns at a glance

| Column                     | Source                            |
|----------------------------|-----------------------------------|
| `matched_duns`             | MatchTool best candidate          |
| `matched_business_name`    | DataTool Primary Name             |
| `matched_address`          | DataTool Primary Address (street) |
| `matched_city`             | Parsed from Primary Address       |
| `matched_state`            | Parsed from Primary Address       |
| `matched_zip`              | Parsed from Primary Address       |
| `matched_country`          | DataTool Country Code             |
| `matched_sic_code`         | Industry code before `:`          |
| `matched_sic_description`  | Industry text after `:`           |
| `matched_ticker`           | NASDAQ/NYSE primary listing       |
| `matched_cage`             | Registration: CAGE type           |
| `matched_ein`              | Registration: EIN type            |
| `match_status`             | Match outcome flag                |

---

## Next steps

- **Add more rows.** Throw a real client list at it and see how the match rates look.
- **Tune your input.** Better input data (especially `streetaddress` and `state`) means fewer `ambiguous` results.
- **Handle edge cases.** International companies, subsidiaries, DBAs — the disambiguation rules above cover the basics, but your data will teach you the rest.
- **Try conversational prompts.** Once matching and enrichment feel comfortable, move on to the [Commercial Graph Sample Prompts](./Commercial_Graph_Sample_Prompts.md). That guide includes 10 ready-to-use prompts with expected responses, covering Match, Data, Risk, and Sales & Marketing tools through natural-language queries.

Happy matching!
