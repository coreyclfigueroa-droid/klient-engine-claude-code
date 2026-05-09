# Offer Engine — First Run Setup

Welcome. This file walks you through one-time setup, then deletes itself so it never runs again.

## What you'll need before we start

1. **Apify API key** — https://console.apify.com/account/integrations (free tier includes $5/mo credits, enough for several scrapes)
2. **Notion integration token** — https://www.notion.so/profile/integrations → "New integration" → copy Internal Integration Token
3. **A Notion page** where your offer workspace will live — you must share this page with the integration you just created (click "..." on the page → Connect to → pick your integration)
4. **(Optional) Airtable API key** — only if you want usage logging. Skip if not.

## Step-by-step setup flow

Claude, when running this onboarding, execute these steps in order. Ask the user one question at a time — do not dump all questions at once.

### Step 1: Collect Apify key
Ask: "Paste your Apify API key. You can get it at https://console.apify.com/account/integrations — it looks like `apify_api_...`"

Store to `~/.claude/offer-engine/config.json` under key `apify_api_token`.

### Step 2: Collect Notion token
Ask: "Paste your Notion integration token. Get it at https://www.notion.so/profile/integrations — create a new integration, copy the 'Internal Integration Token' (starts with `ntn_` or `secret_`)."

Store under `notion_token`.

### Step 3: Collect Notion parent page ID
Ask: "Paste the URL of the Notion page where I should create your Offer Engine workspace. Make sure you've shared that page with your integration — click '...' on the page, Connect to, pick your integration."

Extract the 32-character page ID from the URL (the last chunk of hex after the final dash in the slug). Store under `notion_parent_page_id`.

### Step 4: Verify Notion access
Use `curl` with the Notion token to fetch the parent page. If it returns 404, the user didn't share the page with the integration — explain the fix and retry.

<details>
<summary>bash</summary>

```bash
curl -s -H "Authorization: Bearer $NOTION_TOKEN" \
     -H "Notion-Version: 2022-06-28" \
     "https://api.notion.com/v1/pages/$PARENT_PAGE_ID"
```

</details>

### Step 5: Auto-create the three databases
Under the parent page, create three databases via Notion API:

**Framework Library** — properties:
- `Name` (title) — short name of the pattern
- `Category` (select: Offer Structure, Value Stack, Guarantee, Bonus, Hook, Headline, Pricing, Objection Handling, Sales Psychology, Other)
- `Source URL` (url)
- `Source Creator` (rich_text) — handle or name as it appears on the source
- `Pattern Description` (rich_text) — Claude's structural description in its own words
- `When To Use` (rich_text) — conditions under which this pattern applies
- `Example Application` (rich_text) — generic example of the pattern applied to a fictional business
- `Scraped Date` (date)

**Market Intel** — properties (created even if competitor-intel skill isn't installed; companion skill will populate):
- `Name` (title) — competitor or source name
- `Source Type` (select: Reviews, Website, Facebook Ads, Reddit, Other)
- `Source URL` (url)
- `Pain Points` (rich_text)
- `Customer Language` (rich_text)
- `Positioning Notes` (rich_text)
- `Scraped Date` (date)

**Offer Drafts** — properties:
- `Name` (title) — offer name
- `Business` (rich_text) — user's business/product name
- `Status` (select: Draft, Testing, Live, Retired)
- `Built Date` (date)
- `Frameworks Used` (rich_text) — which Framework Library entries informed this draft
- The offer body itself lives as the page content, not a property (long-form writing)

Store the three database IDs under `framework_library_db_id`, `market_intel_db_id`, `offer_drafts_db_id`.

### Step 6: Airtable (optional)
Ask: "Want usage logging in Airtable? (yes/no) — skip if you're not sure."

If yes → collect API key and base ID, store under `airtable_api_key` and `airtable_base_id`. Create a table called `offer_engine_logs` with columns: `Run Date`, `Command`, `Source URL`, `Duration Seconds`, `Apify Cost Units`, `Status`.

If no → skip.

### Step 7: Cost expectations
Tell the user plainly:

> **Here's what things cost:**
> - YouTube channel scrape (50 videos): ~$0.30–$0.80 in Apify credits, runs 3–8 minutes
> - YouTube single video scrape: ~$0.01–$0.03, runs under 30 seconds
> - Podcast RSS + 10 episode transcripts: ~$0.15–$0.40, runs 2–5 minutes
> - Notion API calls: free within normal use limits
>
> Every scrape will show you the estimated cost and runtime before it runs, and will not execute until you confirm. You can cancel any time.

### Step 8: Confirm setup worked
Do a dry-run check:
- Notion API: create a test page in Framework Library with `Name = "Setup Test"`, then delete it
- Apify API: call `/v2/acts` with the token to verify it authenticates (no scrape run)

If both succeed → proceed to step 9.
If either fails → surface the exact error and walk the user through fixing it.

### Step 9: Self-destruct
Delete `onboarding.md` from the skill folder:

<details>
<summary>bash</summary>

```bash
rm /path/to/offer-engine/onboarding.md
```

</details>

Confirm to the user:

> Setup is complete. Your Notion workspace is live with three databases ready. You can now run:
> - `/scrape-creator [url]` to extract frameworks from any creator
> - `/build-offer` to build an offer once you have frameworks in your library
>
> This onboarding file has deleted itself. It won't run again.

## Config file schema

Final `~/.claude/offer-engine/config.json` should look like:

<details>
<summary>json</summary>

```json
{
  "apify_api_token": "apify_api_...",
  "notion_token": "ntn_...",
  "notion_parent_page_id": "...",
  "framework_library_db_id": "...",
  "market_intel_db_id": "...",
  "offer_drafts_db_id": "...",
  "airtable_api_key": "pat_... or null",
  "airtable_base_id": "app... or null",
  "setup_completed_at": "ISO timestamp"
}
```

</details>

Set `0600` permissions on the config file so keys aren't world-readable.
