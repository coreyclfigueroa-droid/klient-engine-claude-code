---
name: lead-scraper
version: 1.0.0
description: "Use this skill when the user wants to scrape business leads, find local businesses, generate lead lists, or prospect from Google Maps. Also use when the user mentions 'scrape leads,' 'lead gen,' 'find businesses,' 'Google Maps scraper,' 'business emails,' 'prospect list,' 'local leads,' or 'scrape Google Maps.' This skill scrapes Google Maps for business data + emails + socials and logs everything to Airtable."
---

# Lead Scraper — Google Maps to Airtable

You are an automated lead generation pipeline. Your job is to scrape business leads from Google Maps — including contact emails scraped directly from business websites — and deliver a clean, structured lead list into Airtable ready for outreach.

---

## ⚠️ ONBOARDING — DELETE THIS SECTION AFTER COMPLETION

**This section runs ONCE. After both API keys are saved and confirmed, delete everything between the `⚠️ ONBOARDING` markers.**

### Step 1: Check for Apify API Key

```bash
# Check if Apify key exists in settings.json
cat .claude/settings.json 2>/dev/null | grep -q "apify_api_key"
```

**If key NOT found**, say:

---

**Let's get you set up with Apify — this is what powers the scraping.**

1. **Create a free Apify account here:** https://console.apify.com/sign-up
   - You get $5 in free credits which is enough to scrape hundreds of leads for free.

**Did you create your account?**

---

Wait for them to confirm. Then say:

---

2. **Now grab your API token:**
   - Go to: https://console.apify.com/account/integrations
   - Copy the **Personal API Token**
   - Paste it here:

---

When they paste the key, save it:

```bash
mkdir -p .claude
# Read existing settings.json or create new one
# Add "apify_api_key": "<THEIR_KEY>" to the JSON
```

Confirm: **"Apify key saved."**

**If key IS found**, say: **"Apify key already set up. Moving on."**

### Step 2: Check for Airtable API Key

```bash
# Check if Airtable key exists in settings.json
cat .claude/settings.json 2>/dev/null | grep -q "airtable_api_key"
```

**If key NOT found**, say:

---

**Now let's connect Airtable — this is where your leads land.**

1. **Go to your Airtable API token page:** https://airtable.com/create/tokens
2. **Create a new personal access token** with these scopes:
   - `data.records:read`
   - `data.records:write`
   - `schema.bases:read`
   - `schema.bases:write`
3. Under **Access**, give it access to all current and future bases (or whichever workspace you want).
4. **Copy the token and paste it here:**

---

When they paste the key, save it:

```bash
# Add "airtable_api_key": "<THEIR_KEY>" to .claude/settings.json
```

Confirm: **"Airtable key saved. You're fully set up."**

**If key IS found**, say: **"Airtable key already set up. Moving on."**

### Step 3: Self-Destruct Onboarding

After BOTH keys are confirmed saved and working:

1. Delete everything between the `⚠️ ONBOARDING` markers from this SKILL.md file
2. Confirm: **"Onboarding complete. This won't run again."**

**HARD GATE: Do NOT proceed to any pipeline stage until both keys are saved in `.claude/settings.json`.**

## ⚠️ END ONBOARDING — DELETE TO HERE

---

## Pipeline Overview

```
[1] CONFIGURE → [2] SCRAPE → [3] ENRICH → [4] CLEAN → [5] OUTPUT
```

| Stage | What Happens |
|-------|-------------|
| 1. Configure | Define search terms, location, filters, and lead count |
| 2. Scrape | Apify Google Maps scraper pulls business data + visits websites for emails |
| 3. Enrich | Parse and validate emails, social links, and contact info |
| 4. Clean | Deduplicate, remove junk entries, format for CRM |
| 5. Output | Leads saved to Airtable base |

---

## Stage 1: Lead Search Configuration

### Check for Saved Parameters

```bash
cat .claude/lead-config.json 2>/dev/null
```

**If config exists**, ask:

> Last time you ran a lead scrape, you used these settings:
> - **Search terms:** [list them]
> - **Location:** [location]
> - **Max leads per search:** [number]
> - **Email scraping depth:** [shallow/deep]
> - **Filters:** [any filters]
>
> Run with the same settings, or new ones?

**If config does NOT exist** (or they want new settings), ask these questions one at a time:

1. **What type of businesses are you looking for?** Give me 1-5 search terms. Be specific — "dentists" works, "dental offices accepting new patients" works better. More specific = more targeted leads.

   Examples:
   - "plumbers"
   - "real estate agents"
   - "CrossFit gyms"
   - "wedding photographers"
   - "ecommerce fulfillment warehouse"
   - "digital marketing agency"

2. **What location?** City + state is the minimum. You can also do:
   - A specific city: "Austin, TX"
   - A metro area: "Los Angeles area"
   - Multiple cities (I'll run separate searches): "Miami, FL" and "Fort Lauderdale, FL"
   - A full state: "California" (this will pull a LOT — I'd recommend going city by city)

3. **How many leads do you want per search term?** Google Maps caps around 500 results per query. Recommended:
   - Testing: 20-50
   - Small campaign: 100-200
   - Full scrape: 300-500

4. **Do you want emails?** (Recommended: YES) This makes the scraper visit each business website to find email addresses. Two options:
   - **Shallow** — scans homepage only. Faster, cheaper.
   - **Deep** — scans homepage + /contact, /about, /impressum pages. Finds 30-50% more emails but takes longer and costs more.

5. **Do you want WhatsApp availability checked?** The scraper can check if each business phone number is registered on WhatsApp. Useful for international leads or DM-based outreach.

6. **Any filters?** Optional:
   - Minimum Google rating (e.g., 4.0+)
   - Minimum number of reviews (e.g., 10+)
   - Only businesses with a website
   - Only businesses with an email found

After collecting answers, save config:

```bash
# Save to .claude/lead-config.json
{
  "search_terms": ["dentists", "orthodontists"],
  "locations": ["Austin, TX"],
  "max_leads_per_search": 100,
  "email_depth": "deep",
  "check_whatsapp": false,
  "min_rating": null,
  "min_reviews": null,
  "require_website": false,
  "require_email": false,
  "last_run": null
}
```

---

### Pre-Launch Confirmation

**HARD GATE: Before firing the scrape, explain the cost and timeout reality. Do NOT skip this.**

Say:

> **Before I run this, here's what you need to understand:**
>
> This pipeline uses Apify's sync API endpoint, which has a **300 second (5 minute) timeout.** That means:
>
> - **50 leads or less** → runs clean, no issues
> - **50-200 leads** → usually fine, but email enrichment (visiting each website) adds time
> - **200-500 leads with deep email scraping** → may timeout. If it does, I'll switch to async mode and poll for results.
> - **500+ leads** → will definitely need async mode
>
> **Important about email scraping:** When email depth is set to "deep," the scraper visits multiple pages on EACH business website. For 100 businesses, that's 300-500 web pages being crawled. This takes significantly longer than a basic scrape.
>
> You set this to scrape **[X] leads per search** across **[Y] search terms** = **[X × Y] total leads** with **[shallow/deep]** email enrichment.
>
> **Estimated cost:** Google Maps scraping runs roughly $2-5 per 1,000 results on the base scraper, plus additional cost for email enrichment. Your run of [total] leads will cost approximately **$[calculated estimate]**.
>
> **Do you understand the timeout limits and want to launch the scrape?**

**Wait for explicit "yes" before proceeding. Do NOT fire the API call without confirmation.**

If they want to adjust, update `lead-config.json` and re-confirm.

---

## Stage 2: Execute the Scrape

Read the Apify API key from `.claude/settings.json`.
Read the config from `.claude/lead-config.json`.

**Primary actor: `compass/crawler-google-places`** (most battle-tested, maintained by Apify)

For EACH search term + location combo, fire the scraper:

```bash
# Actor: compass/crawler-google-places
# Endpoint: https://api.apify.com/v2/acts/compass~crawler-google-places/run-sync-get-dataset-items

curl -s -X POST "https://api.apify.com/v2/acts/compass~crawler-google-places/run-sync-get-dataset-items?token=<APIFY_KEY>&timeout=300" \
  -H "Content-Type: application/json" \
  -d '{
    "searchStringsArray": ["<search_term> <location>"],
    "maxCrawledPlacesPerSearch": <max_leads>,
    "language": "en",
    "exportPlaceUrls": false,
    "includeWebResults": false,
    "maxImages": 0,
    "maxReviews": 0,
    "scrapeContacts": true,
    "scrapeDirectories": false,
    "deeperCityScrape": false,
    "onePerQuery": "no",
    "proxyConfiguration": {
      "useApifyProxy": true
    }
  }'
```

**If `scrapeContacts` is true** (email scraping enabled), the actor automatically:
- Visits each business website found on Google Maps
- Scans for email addresses, phone numbers, social media links
- Returns them as part of the dataset

**If email depth is "deep"**, add:
```json
"maxCrawledPagesPerPlace": 3
```

**If the sync call times out (300s exceeded):**

Switch to async mode:

```bash
# Start the run (async)
RUN_RESPONSE=$(curl -s -X POST "https://api.apify.com/v2/acts/compass~crawler-google-places/runs?token=<APIFY_KEY>" \
  -H "Content-Type: application/json" \
  -d '<same_input_json>')

RUN_ID=$(echo $RUN_RESPONSE | jq -r '.data.id')
DATASET_ID=$(echo $RUN_RESPONSE | jq -r '.data.defaultDatasetId')
```

Then poll for completion:

```bash
# Check status every 30 seconds
STATUS=$(curl -s "https://api.apify.com/v2/actor-runs/$RUN_ID?token=<APIFY_KEY>" | jq -r '.data.status')
```

Keep polling until `status` is `SUCCEEDED` or `FAILED`. Tell the user:

> **Scrape is running in the background.** The sync call timed out so I switched to async mode. I'm checking every 30 seconds. This might take a few minutes for large scrapes with email enrichment.
>
> Current status: [RUNNING/SUCCEEDED/FAILED]

When `SUCCEEDED`, fetch the dataset:

```bash
curl -s "https://api.apify.com/v2/datasets/$DATASET_ID/items?token=<APIFY_KEY>&format=json" > .claude/lead-data/raw-leads.json
```

**If scrape succeeds via sync**, save directly to `.claude/lead-data/raw-leads.json`.

**If the API call fails entirely:**
- Tell the user what happened (error message from Apify)
- Common fixes: reduce lead count, try a more specific search term, check API key
- Do NOT proceed to Stage 3

---

## Stage 3: Enrich and Parse

Read `.claude/lead-data/raw-leads.json`.

The Google Maps scraper returns a LOT of fields. Extract and normalize these for each lead:

### Core Business Data
| Field | Source Key | Notes |
|-------|-----------|-------|
| Business Name | `title` | |
| Business Category | `categoryName` | Primary category |
| All Categories | `categories` | Array of all categories |
| Address | `address` | Full street address |
| City | `city` | |
| State | `state` | |
| Zip Code | `postalCode` | |
| Country | `countryCode` | |
| Phone | `phone` | Primary phone number |
| Website | `website` | Business website URL |
| Google Maps URL | `url` | Direct link to their Google Maps listing |

### Ratings & Reviews
| Field | Source Key | Notes |
|-------|-----------|-------|
| Google Rating | `totalScore` | 1.0 - 5.0 |
| Total Reviews | `reviewsCount` | Number of reviews |

### Contact Data (from website crawl)
| Field | Source Key | Notes |
|-------|-----------|-------|
| Email | `email` or `emails` | May be single or array — take the first non-generic one |
| Facebook | `socialProfiles.facebook` or from `contacts` | |
| Instagram | `socialProfiles.instagram` or from `contacts` | |
| LinkedIn | `socialProfiles.linkedin` or from `contacts` | |
| Twitter/X | `socialProfiles.twitter` or from `contacts` | |
| YouTube | `socialProfiles.youtube` or from `contacts` | |

### Location Data
| Field | Source Key | Notes |
|-------|-----------|-------|
| Latitude | `location.lat` | |
| Longitude | `location.lng` | |
| Place ID | `placeId` | Google's unique ID for this business |

### Business Details
| Field | Source Key | Notes |
|-------|-----------|-------|
| Opening Hours | `openingHours` | Format as readable text |
| Temporarily Closed | `temporarilyClosed` | Boolean — flag these |
| Permanently Closed | `permanentlyClosed` | Boolean — remove these |

**Email prioritization logic:**

If multiple emails are found, prioritize in this order:
1. Named emails (john@business.com) — highest value for outreach
2. Role emails (sales@, hello@, contact@) — good for outreach
3. Generic emails (info@, admin@, support@) — lowest value but still usable
4. Discard noreply@, mailer-daemon@, or clearly automated addresses

Save parsed leads to `.claude/lead-data/parsed-leads.json`.

---

## Stage 4: Clean and Deduplicate

Read `.claude/lead-data/parsed-leads.json`.

### Remove Bad Leads
- Remove any business marked as `permanentlyClosed`
- Flag (but keep) any business marked as `temporarilyClosed` — add a note in the record
- Remove entries with no business name
- Remove entries with no phone AND no email AND no website (no way to contact them)

### Deduplicate
- Check for duplicate Place IDs — keep only one
- Check for duplicate phone numbers — keep the one with more complete data
- Check for duplicate business names at the same address — keep one

### Apply User Filters
From `lead-config.json`:
- If `min_rating` is set → remove businesses below that rating
- If `min_reviews` is set → remove businesses with fewer reviews
- If `require_website` is true → remove businesses with no website
- If `require_email` is true → remove businesses where no email was found

### Format for Output
- Phone numbers: normalize to consistent format
- Addresses: ensure city/state/zip are populated (parse from full address if needed)
- Opening hours: convert to readable string
- Social links: ensure full URLs (not just handles)
- Emails: lowercase, trim whitespace

Save cleaned leads to `.claude/lead-data/cleaned-leads.json`.

Output a quick summary:

> **Scrape complete. Here's what I found:**
>
> - **Total scraped:** [X] businesses
> - **After cleaning/dedup:** [Y] leads
> - **With email:** [Z] leads ([percentage]%)
> - **With phone:** [A] leads
> - **With website:** [B] leads
> - **With social profiles:** [C] leads
> - **Average Google rating:** [D] stars
> - **Removed:** [E] (closed, duplicates, or filtered out)

---

## Stage 5: Output to Airtable

Read final leads from `.claude/lead-data/cleaned-leads.json`.
Read Airtable API key from `.claude/settings.json`.

### First Run — Create the Base

**On first run** (no `lead_base_id` in `lead-config.json`):

Create a new Airtable base:

```bash
curl -s -X POST "https://api.airtable.com/v0/meta/bases" \
  -H "Authorization: Bearer <AIRTABLE_KEY>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Lead Scraper",
    "tables": [
      {
        "name": "Leads",
        "fields": [
          {"name": "Business Name", "type": "singleLineText"},
          {"name": "Category", "type": "singleLineText"},
          {"name": "All Categories", "type": "multilineText"},
          {"name": "Phone", "type": "phoneNumber"},
          {"name": "Email", "type": "email"},
          {"name": "Website", "type": "url"},
          {"name": "Address", "type": "singleLineText"},
          {"name": "City", "type": "singleLineText"},
          {"name": "State", "type": "singleLineText"},
          {"name": "Zip Code", "type": "singleLineText"},
          {"name": "Google Rating", "type": "number", "options": {"precision": 1}},
          {"name": "Total Reviews", "type": "number", "options": {"precision": 0}},
          {"name": "Google Maps URL", "type": "url"},
          {"name": "Facebook", "type": "url"},
          {"name": "Instagram", "type": "url"},
          {"name": "LinkedIn", "type": "url"},
          {"name": "Twitter/X", "type": "url"},
          {"name": "YouTube", "type": "url"},
          {"name": "Opening Hours", "type": "multilineText"},
          {"name": "Latitude", "type": "number", "options": {"precision": 6}},
          {"name": "Longitude", "type": "number", "options": {"precision": 6}},
          {"name": "Place ID", "type": "singleLineText"},
          {"name": "Status", "type": "singleSelect", "options": {"choices": [{"name": "New"}, {"name": "Contacted"}, {"name": "Responded"}, {"name": "Meeting Booked"}, {"name": "Closed"}, {"name": "Not Interested"}, {"name": "Temporarily Closed"}]}},
          {"name": "Notes", "type": "multilineText"},
          {"name": "Search Term", "type": "singleLineText"},
          {"name": "Location Searched", "type": "singleLineText"},
          {"name": "Scrape Date", "type": "singleLineText"}
        ]
      },
      {
        "name": "Scrape Log",
        "fields": [
          {"name": "Run Date", "type": "singleLineText"},
          {"name": "Search Terms", "type": "multilineText"},
          {"name": "Location", "type": "singleLineText"},
          {"name": "Total Scraped", "type": "number", "options": {"precision": 0}},
          {"name": "After Cleaning", "type": "number", "options": {"precision": 0}},
          {"name": "With Email", "type": "number", "options": {"precision": 0}},
          {"name": "With Phone", "type": "number", "options": {"precision": 0}},
          {"name": "Email Rate", "type": "singleLineText"},
          {"name": "Avg Rating", "type": "number", "options": {"precision": 1}},
          {"name": "Settings Used", "type": "multilineText"}
        ]
      }
    ]
  }'
```

Save the returned `base_id`, `leads_table_id`, and `scrape_log_table_id` to `.claude/lead-config.json`.

### Every Run — Insert Leads

Read `lead_base_id` and `leads_table_id` from config.

Insert each lead as a record:

```bash
curl -s -X POST "https://api.airtable.com/v0/<BASE_ID>/<LEADS_TABLE_ID>" \
  -H "Authorization: Bearer <AIRTABLE_KEY>" \
  -H "Content-Type: application/json" \
  -d '{
    "records": [
      {
        "fields": {
          "Business Name": "<name>",
          "Category": "<category>",
          "All Categories": "<all_categories_joined>",
          "Phone": "<phone>",
          "Email": "<email>",
          "Website": "<website>",
          "Address": "<address>",
          "City": "<city>",
          "State": "<state>",
          "Zip Code": "<zip>",
          "Google Rating": <rating>,
          "Total Reviews": <reviews>,
          "Google Maps URL": "<maps_url>",
          "Facebook": "<facebook_url>",
          "Instagram": "<instagram_url>",
          "LinkedIn": "<linkedin_url>",
          "Twitter/X": "<twitter_url>",
          "YouTube": "<youtube_url>",
          "Opening Hours": "<hours>",
          "Latitude": <lat>,
          "Longitude": <lng>,
          "Place ID": "<place_id>",
          "Status": "New",
          "Notes": "",
          "Search Term": "<search_term_used>",
          "Location Searched": "<location_used>",
          "Scrape Date": "<today>"
        }
      }
    ]
  }'
```

**Airtable API limit: 10 records per POST.** Batch in groups of 10.

### Log the Run

After all leads are inserted, log the run stats to the "Scrape Log" tab:

```bash
curl -s -X POST "https://api.airtable.com/v0/<BASE_ID>/<SCRAPE_LOG_TABLE_ID>" \
  -H "Authorization: Bearer <AIRTABLE_KEY>" \
  -H "Content-Type: application/json" \
  -d '{
    "records": [
      {
        "fields": {
          "Run Date": "<today>",
          "Search Terms": "<all_search_terms>",
          "Location": "<location>",
          "Total Scraped": <raw_count>,
          "After Cleaning": <clean_count>,
          "With Email": <email_count>,
          "With Phone": <phone_count>,
          "Email Rate": "<percentage>%",
          "Avg Rating": <avg_rating>,
          "Settings Used": "<JSON_dump_of_config>"
        }
      }
    ]
  }'
```

### Deduplicate Against Existing Leads

**On runs after the first**, before inserting, check for existing leads with the same Place ID:

```bash
# Fetch existing Place IDs from Airtable
curl -s "https://api.airtable.com/v0/<BASE_ID>/<LEADS_TABLE_ID>?fields%5B%5D=Place%20ID&pageSize=100" \
  -H "Authorization: Bearer <AIRTABLE_KEY>"
```

**Paginate through all records** using the `offset` parameter to get ALL existing Place IDs.

Compare new leads against existing Place IDs. For duplicates:
- Skip the insert (don't create duplicate rows)
- Count how many were skipped

Tell the user:

> **[X] leads were already in your Airtable from previous scrapes — skipped those. [Y] new leads added.**

---

### Final Output

After all leads are saved and the run is logged, output:

```
╔══════════════════════════════════════════════════════════╗
║                                                          ║
║  ✅ LEAD SCRAPE COMPLETE                                 ║
║                                                          ║
║  🔍 Search: [search terms] in [location]                 ║
║                                                          ║
║  📊 Results:                                             ║
║     Total scraped:     [X]                               ║
║     After cleaning:    [Y]                               ║
║     New leads added:   [Z]                               ║
║     Duplicates skipped:[D]                               ║
║                                                          ║
║  📧 Contact Data:                                        ║
║     With email:        [A] ([%]%)                        ║
║     With phone:        [B] ([%]%)                        ║
║     With website:      [C] ([%]%)                        ║
║     With socials:      [E] ([%]%)                        ║
║                                                          ║
║  ⭐ Quality:                                             ║
║     Avg Google rating: [F]                               ║
║     Avg reviews:       [G]                               ║
║                                                          ║
║  📍 Airtable: Lead Scraper base                          ║
║     ├── Leads tab: all leads with contact data           ║
║     └── Scrape Log tab: run stats logged                 ║
║                                                          ║
╚══════════════════════════════════════════════════════════╝
```

Then show the top 5 leads with the most complete data (email + phone + website + socials + high rating) so the user can see the quality without opening Airtable.

---

## Error Handling

- **Apify scrape fails:** Tell user, show error message, suggest more specific search terms or smaller lead count
- **Sync timeout:** Automatically switch to async mode and poll — tell the user what's happening
- **No results returned:** Search term might be too specific or location might not have that business type. Suggest broadening the search.
- **No emails found:** Normal — not all businesses list emails publicly. Suggest switching to "deep" email depth if using "shallow." Note that email rates of 30-60% are typical.
- **Airtable base creation fails:** Check API key permissions, tell user which scopes are needed
- **Airtable insert fails:** Batch size issue — retry in smaller batches. Check if they hit Airtable rate limits (5 requests per second).
- **Airtable dedup check fails:** Skip dedup, insert all leads, warn user to check for duplicates manually

---

## Tips for Better Results

Share these with the user if their scrape comes back thin:

1. **Be specific with search terms.** "plumber" gets generic results. "emergency plumber 24 hour" gets businesses that actually advertise services.

2. **Split big areas into smaller searches.** "dentists California" will cap out fast. Run "dentists San Diego," "dentists Los Angeles," "dentists San Francisco" separately.

3. **Use business category language.** Google Maps categorizes businesses specifically. "CrossFit gym" works better than "gym" if that's what you want.

4. **Deep email scraping is worth it.** The homepage-only scan misses a lot. The /contact and /about pages are where most businesses put their email.

5. **Filter by reviews.** Businesses with 10+ reviews are usually established and more likely to respond to outreach. Businesses with 0 reviews might be dead listings.

6. **Run the same search in different cities.** Same search term, different location each run. Your Airtable base collects everything in one place across all runs.

---

## File Structure

```
.claude/
├── settings.json              ← API keys (Apify + Airtable) — shared with other skills
├── lead-config.json           ← Saved search parameters + Airtable base/table IDs
└── lead-data/
    ├── raw-leads.json         ← Full Apify output
    ├── parsed-leads.json      ← Normalized and enriched
    └── cleaned-leads.json     ← Final deduped and filtered leads
```

---

## Multiple Searches in One Run

If the user provides multiple search terms AND/OR multiple locations, run them as separate Apify calls and combine the results before cleaning:

**Example:** User wants "dentists" and "orthodontists" in "Austin, TX" and "San Antonio, TX"

That's 4 separate scrapes:
1. "dentists Austin, TX"
2. "dentists San Antonio, TX"
3. "orthodontists Austin, TX"
4. "orthodontists San Antonio, TX"

Run each one, combine all results into one `raw-leads.json`, then deduplicate across all of them in Stage 4 (a business might show up in both "dentist" and "orthodontist" searches).

Tag each lead with which search term and location produced it so the user can filter in Airtable.

---

## Airtable Status Workflow

The Leads table includes a "Status" single-select field pre-configured with these options:

| Status | Meaning |
|--------|---------|
| **New** | Just scraped, not yet contacted (default) |
| **Contacted** | Outreach sent (email, call, DM) |
| **Responded** | They replied |
| **Meeting Booked** | Call or meeting scheduled |
| **Closed** | Deal closed |
| **Not Interested** | They declined |
| **Temporarily Closed** | Business flagged as temporarily closed by Google |

This gives the user a basic CRM pipeline right inside Airtable without needing a separate tool. They can filter by status, sort by rating, and track their outreach progress.
