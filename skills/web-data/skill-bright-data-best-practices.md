---
id: skill-bright-data-best-practices
type: skill
name: bright-data-best-practices
description: Build production-ready Bright Data integrations with best practices baked
  in. Reference documentation for developers using coding assistants (Claude Code,
  Cursor, etc.) to implement web scraping, search, browser automation, and structured
  data extraction. Covers Web Unlocker API, SERP API, Web Scraper API, and Browser
  API (Scraping Browser).
category: web-data
complexity: medium
keywords:
- api
- javascript
- optimization
- python
- rest
- test
capabilities: []
token_estimate: 1927
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,927 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# bright-data-best-practices

> Build production-ready Bright Data integrations with best practices baked in. Reference documentation for developers using coding assistants (Claude Code, Cursor, etc.) to implement web scraping, search, browser automation, and structured data extraction. Covers Web Unlocker API, SERP API, Web Scraper API, and Browser API (Scraping Browser).

# Bright Data APIs

Bright Data provides infrastructure for web data extraction at scale. Four primary APIs cover different use cases — always pick the most specific tool for the job.

## Choosing the Right API

| Use Case | API | Why |
|----------|-----|-----|
| Scrape any webpage by URL (no interaction) | Web Unlocker | HTTP-based, auto-bypasses bot detection, cheapest |
| Google / Bing / Yandex search results | SERP API | Specialized for SERP extraction, returns structured data |
| Structured data from Amazon, LinkedIn, Instagram, TikTok, etc. | Web Scraper API | Pre-built scrapers, no parsing needed |
| Click, scroll, fill forms, run JS, intercept XHR | Browser API | Full browser automation |
| Puppeteer / Playwright / Selenium automation | Browser API | Connects via CDP/WebDriver |

## Authentication Pattern (All APIs)

All APIs share the same authentication model:

```bash
export BRIGHTDATA_API_KEY="your-api-key"         # From Control Panel > Account Settings
export BRIGHTDATA_UNLOCKER_ZONE="zone-name"       # Web Unlocker zone name
export BRIGHTDATA_SERP_ZONE="serp-zone-name"      # SERP API zone name
export BROWSER_AUTH="brd-customer-ID-zone-NAME:PASSWORD"  # Browser API credentials
```

REST API authentication header for Web Unlocker and SERP API:
```
Authorization: Bearer YOUR_API_KEY
```

---

## Web Unlocker API

HTTP-based scraping proxy. Best for simple page fetches without browser interaction.

**Endpoint:** `POST https://api.brightdata.com/request`

```python
import requests

response = requests.post(
    "https://api.brightdata.com/request",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={
        "zone": "YOUR_ZONE_NAME",
        "url": "https://example.com/product/123",
        "format": "raw"
    }
)
html = response.text
```

### Key Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `zone` | string | Zone name (required) |
| `url` | string | Target URL with `http://` or `https://` (required) |
| `format` | string | `"raw"` (HTML) or `"json"` (structured wrapper) (required) |
| `method` | string | HTTP verb, default `"GET"` |
| `country` | string | 2-letter ISO for geo-targeting (e.g., `"us"`, `"de"`) |
| `data_format` | string | Transform: `"markdown"` or `"screenshot"` |
| `async` | boolean | `true` for async mode |

### Quick Patterns

```python
# Get markdown (best for LLM input)
response = requests.post(
    "https://api.brightdata.com/request",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"zone": ZONE, "url": url, "format": "raw", "data_format": "markdown"}
)

# Geo-targeted request
json={"zone": ZONE, "url": url, "format": "raw", "country": "de"}

# Screenshot for debugging
json={"zone": ZONE, "url": url, "format": "raw", "data_format": "screenshot"}

# Async for bulk processing
json={"zone": ZONE, "url": url, "format": "raw", "async": True}
```

**Critical rule:** Never use Web Unlocker with Puppeteer, Playwright, Selenium, or anti-detect browsers. Use Browser API instead.

See **[references/web-unlocker.md](references/web-unlocker.md)** for complete reference including proxy interface, special headers, async flow, features, and billing.

---

## SERP API

Structured search engine result extraction for Google, Bing, Yandex, DuckDuckGo.

**Endpoint:** `POST https://api.brightdata.com/request` (same as Web Unlocker)

```python
response = requests.post(
    "https://api.brightdata.com/request",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={
        "zone": "YOUR_SERP_ZONE",
        "url": "https://www.google.com/search?q=python+web+scraping&brd_json=1&gl=us&hl=en",
        "format": "raw"
    }
)
data = response.json()
for result in data.get("organic", []):
    print(result["rank"], result["title"], result["link"])
```

### Essential Google URL Parameters

| Parameter | Description | Example |
|-----------|-------------|---------|
| `q` | Search query | `q=python+web+scraping` |
| `brd_json` | Parsed JSON output | `brd_json=1` (always use for data pipelines) |
| `gl` | Country for search | `gl=us` |
| `hl` | Language | `hl=en` |
| `start` | Pagination offset | `start=10` (page 2), `start=20` (page 3) |
| `tbm` | Search type | `tbm=nws` (news), `tbm=isch` (images), `tbm=vid` (videos) |
| `brd_mobile` | Device | `brd_mobile=1` (mobile), `brd_mobile=ios` |
| `brd_browser` | Browser | `brd_browser=chrome` |
| `brd_ai_overview` | Trigger AI Overview | `brd_ai_overview=2` |
| `uule` | Encoded geo location | for precise location targeting |

**Note:** `num` parameter is **deprecated** as of September 2025. Use `start` for pagination.

### Parsed JSON Response Structure

```json
{
  "organic": [{"rank": 1, "global_rank": 1, "title": "...", "link": "...", "description": "..."}],
  "paid": [],
  "people_also_ask": [],
  "knowledge_graph": {},
  "related_searches": [],
  "general": {"results_cnt": 1240000000, "query": "..."}
}
```

### Bing Key Parameters

| Parameter | Description |
|-----------|-------------|
| `q` | Search query |
| `setLang` | Language (prefer 4-letter: `en-US`) |
| `cc` | Country code |
| `first` | Pagination (increment by 10: 1, 11, 21...) |
| `safesearch` | `off`, `moderate`, `strict` |
| `brd_mobile` | Device type |

### Async for Bulk SERP

```python
# Submit
response = requests.post(
    "https://api.brightdata.com/request",
    params={"async": "1"},
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"zone": SERP_ZONE, "url": "https://www.google.com/search?q=test&brd_json=1", "format": "raw"}
)
response_id = response.headers.get("x-response-id")

# Retrieve (retrieve calls are NOT billed)
result = requests.get(
    "https://api.brightdata.com/serp/get_result",
    params={"response_id": response_id},
    headers={"Authorization": f"Bearer {API_KEY}"}
)
```

**Billing:** Pay per 1,000 successful requests only. Async retrieve calls are not billed.

See **[references/serp-api.md](references/serp-api.md)** for complete reference including Maps, Trends, Reviews, Lens, Hotels, Flights parameters.

---

## Web Scraper API

Pre-built scrapers for structured data extraction from 100+ platforms. No parsing logic needed.

**Sync Endpoint:** `POST https://api.brightdata.com/datasets/v3/scrape`
**Async Endpoint:** `POST https://api.brightdata.com/datasets/v3/trigger`

```python
# Sync (up to 20 URLs, returns immediately)
response = requests.post(
    "https://api.brightdata.com/datasets/v3/scrape",
    params={"dataset_id": "YOUR_DATASET_ID", "format": "json"},
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"input": [{"url": "https://www.amazon.com/dp/B09X7M8TBQ"}]}
)

if response.status_code == 200:
    data = response.json()  # Results ready
elif response.status_code == 202:
    snapshot_id = response.json()["snapshot_id"]  # Poll for completion
```

### Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `dataset_id` | string | Scraper identifier from the Scraper Library (required) |
| `format` | string | `json` (default), `ndjson`, `jsonl`, `csv` |
| `custom_output_fields` | string | Pipe-separated fields: `url\|title\|price` |
| `include_errors` | boolean | Include error info in results |

### Request Body

```json
{
  "input": [
    { "url": "https://www.amazon.com/dp/B09X7M8TBQ" },
    { "url": "https://www.amazon.com/dp/B0B7CTCPKN" }
  ]
}
```

### Poll for Async Results

```python
import time

# Trigger
snapshot_id = requests.post(
    "https://api.brightdata.com/datasets/v3/trigger",
    params={"dataset_id": DATASET_ID, "format": "json"},
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"input": [{"url": u} for u in urls]}
).json()["snapshot_id"]

# Poll
while True:
    status = requests.get(
        f"https://api.brightdata.com/datasets/v3/progress/{snapshot_id}",
        headers={"Authorization": f"Bearer {API_KEY}"}
    ).json()["status"]

    if status == "ready": break
    if status == "failed": raise Exception("Job failed")
    time.sleep(10)

# Download
data = requests.get(
    f"https://api.brightdata.com/datasets/v3/snapshot/{snapshot_id}",
    params={"format": "json"},
    headers={"Authorization": f"Bearer {API_KEY}"}
).json()
```

**Progress status values:** `starting` → `running` → `ready` | `failed`
**Data retention:** 30 days.
**Billing:** Per delivered record. Invalid input URLs that fail are still billable.

See **[references/web-scraper-api.md](references/web-scraper-api.md)** for complete reference including scraper types, output formats, delivery options, and billing details.

---

## Browser API (Scraping Browser)

Full browser automation via CDP/WebDriver. Handles CAPTCHA, fingerprinting, and anti-bot detection automatically.

**Connection:**
- Playwright/Puppeteer: `wss://${AUTH}@brd.superproxy.io:9222`
- Selenium: `https://${AUTH}@brd.superproxy.io:9515`

```javascript
const { chromium } = require("playwright-core");

const AUTH = process.env.BROWSER_AUTH;
const browser = await chromium.connectOverCDP(`wss://${AUTH}@brd.superproxy.io:9222`);
const page = await browser.newPage();
page.setDefaultNavigationTimeout(120000); // Always set to 2 minutes

await page.goto("https://example.com", { waitUntil: "domcontentloaded" });
const html = await page.content();
await browser.close();
```

```python
from playwright.async_api import async_playwright

async with async_playwright() as p:
    browser = await p.chromium.connect_over_cdp(f"wss://{AUTH}@brd.superproxy.io:9222")
    page = await browser.new_page()
    page.set_default_navigation_timeout(120000)
    await page.goto("https://example.com", wait_until="domcontentloaded")
    html = await page.content()
    await browser.close()
```

### Custom CDP Functions

| Function | Purpose |
|----------|---------|
| `Captcha.solve` | Manually trigger CAPTCHA solving |
| `Captcha.setAutoSolve` | Enable/disable auto CAPTCHA solving |
| `Proxy.setLocation` | Set precise geo location (call BEFORE goto) |
| `Proxy.useSession` | Maintain same IP across sessions |
| `Emulation.setDevice` | Apply device profile (iPhone 14, etc.) |
| `Emulation.getSupportedDevices` | List available device profiles |
| `Unblocker.enableAdBlock` | Block ads to save bandwidth |
| `Unblocker.disableAdBlock` | Re-enable ads |
| `Input.type` | Fast text input for bulk form filling |
| `Browser.addCertificate` | Install client SSL cert for session |
| `Page.inspect` | Get DevTools debug URL for live session |

```javascript
// CDP session pattern for custom functions
const client = await page.target().createCDPSession();

// CAPTCHA solve with timeout
const result = await client.send("Captcha.solve", { timeout: 30000 });

// Precise geo location (must be before goto)
await client.send("Proxy.setLocation", {
  latitude: 37.7749,
  longitude: -122.4194,
  distance: 10,
  strict: true
});

// Block unnecessary resources
await client.send("Network.setBlockedURLs", { urls: ["*google-analytics*", "*.ads.*"] });

// Device emulation
await client.send("Emulation.setDevice", { deviceName: "iPhone 14" });
```

### Session Rules
- **One initial navigation per session** — new URL = new session
- **Idle timeout:** 5 minutes
- **Max duration:** 30 minutes

### Geolocation
- Country-level: append `-country-us` to credentials username
- EU-wide: append `-country-eu` (routes through 29+ European countries)
- Precise: use `Proxy.setLocation` CDP command (before navigation)

### Error Codes

| Code | Issue | Fix |
|------|-------|-----|
| `407` | Wrong port | Playwright/Puppeteer → `9222`, Selenium → `9515` |
| `403` | Bad auth | Check credentials format and zone type |
| `503` | Service scaling | Wait 1 minute, reconnect |

**Billing:** Traffic-based only. Block images/CSS/fonts to reduce costs.

See **[references/browser-api.md](references/browser-api.md)** for complete reference including all CDP functions, bandwidth optimization, CAPTCHA patterns, and debugging.

---

## Detailed References

- **[references/web-unlocker.md](references/web-unlocker.md)** — Web Unlocker: full parameter list, proxy interface, special headers, async flow, features, billing, anti-patterns
- **[references/serp-api.md](references/serp-api.md)** — SERP API: all Google params (Maps, Trends, Reviews, Lens, Hotels, Flights), Bing params, parsed JSON structure, async, billing
- **[references/web-scraper-api.md](references/web-scraper-api.md)** — Web Scraper API: sync vs async, all parameters, polling, scraper types, output formats, billing
- **[references/browser-api.md](references/browser-api.md)** — Browser API: connection strings, session rules, all CDP functions, geo-targeting, bandwidth optimization, CAPTCHA, debugging, error codes


---


## 📚 Reference Materials


### Web Scraper Api

# Web Scraper API Reference

## Table of Contents

- [Overview](#overview)
- [Authentication](#authentication)
- [Choosing Sync vs Async](#choosing-sync-vs-async)
- [Synchronous Requests](#synchronous-requests)
- [Asynchronous Requests](#asynchronous-requests)
- [Monitor Progress](#monitor-progress)
- [Download Results](#download-results)
- [Scraper Types](#scraper-types)
- [Output Formats](#output-formats)
- [Billing Model](#billing-model)
- [Best Practices](#best-practices)

---

## Overview

Bright Data Web Scraper API provides pre-built scrapers ("datasets") for 100+ popular websites including Amazon, LinkedIn, Instagram, TikTok, YouTube, Facebook, and more. You provide input (URLs or keywords), and receive clean structured JSON/CSV data without writing any scraping logic.

**Supported domains include:** Amazon, eBay, Walmart, LinkedIn, Instagram, TikTok, YouTube, Facebook, Reddit, Twitter/X, Crunchbase, ZoomInfo, and many more.

---

## Authentication

```bash
export BRIGHTDATA_API_KEY="your-api-key"
```

Get your API key from: `https://brightdata.com/cp/setting/users`

All requests use Bearer token authentication:
```
Authorization: Bearer YOUR_API_KEY
```

---

## Choosing Sync vs Async

| Factor | Synchronous (`/scrape`) | Asynchronous (`/trigger`) |
|--------|------------------------|---------------------------|
| Input size | Up to **20 URLs** | Any size — built for bulk |
| Response time | Immediate (within 1 min) | Background job — poll for completion |
| Timeout behavior | Returns 202 + `snapshot_id` if >1 min | N/A — always async |
| Best for | Real-time single lookups | Large batches, scheduled jobs |

---

## Synchronous Requests

**Endpoint:** `POST https://api.brightdata.com/datasets/v3/scrape`

Results are returned immediately in the response body.

### Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `dataset_id` | string | Yes | Identifies which scraper to use (from the Scraper Library) |
| `format` | string | No | Output format: `json` (default), `ndjson`, `jsonl`, or `csv` |
| `custom_output_fields` | string | No | Pipe-separated field names to filter output (e.g., `url\|title\|price`) |
| `include_errors` | boolean | No | Include error reporting in results |

### Request Body

```json
{
  "input": [
    { "url": "https://www.amazon.com/dp/B09X7M8TBQ" },
    { "url": "https://www.amazon.com/dp/B0B7CTCPKN" }
  ]
}
```

### Python Example

```python
import requests

response = requests.post(
    "https://api.brightdata.com/datasets/v3/scrape",
    params={
        "dataset_id": "gd_l7q7dkf244hwjntr0",  # Amazon product dataset_id
        "format": "json"
    },
    headers={
        "Authorization": f"Bearer {API_KEY}",
        "Content-Type": "application/json"
    },
    json={
        "input": [
            {"url": "https://www.amazon.com/dp/B09X7M8TBQ"},
            {"url": "https://www.amazon.com/dp/B0B7CTCPKN"}
        ]
    }
)

if response.status_code == 200:
    data = response.json()
    for item in data:
        print(item["title"], item["price"])
elif response.status_code == 202:
    # Processing exceeded 1-minute timeout — use snapshot_id for async retrieval
    snapshot_id = response.json().get("snapshot_id")
    print(f"Processing... poll with snapshot_id: {snapshot_id}")
```

```javascript
const response = await fetch(
  "https://api.brightdata.com/datasets/v3/scrape?dataset_id=gd_l7q7dkf244hwjntr0&format=json",
  {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${API_KEY}`,
      "Content-Type": "application/json"
    },
    body: JSON.stringify({
      input: [
        { url: "https://www.amazon.com/dp/B09X7M8TBQ" }
      ]
    })
  }
);

if (response.status === 200) {
  const data = await response.json();
  console.log(data);
} else if (response.status === 202) {
  const { snapshot_id } = await response.json();
  // Poll for completion
}
```

### Response Codes (Sync)

| Code | Meaning |
|------|---------|
| `200 OK` | Data returned directly in response body |
| `202 Accepted` | Processing exceeded 1-minute timeout — response includes `snapshot_id` for async retrieval |

---

## Asynchronous Requests

Use `/trigger` for large batches or when you don't need an immediate response.

**Endpoint:** `POST https://api.brightdata.com/datasets/v3/trigger`

### Request Parameters (same as sync plus)

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `dataset_id` | string | Yes | Scraper identifier |
| `format` | string | No | `json`, `ndjson`, `jsonl`, `csv` |
| `custom_output_fields` | string | No | Pipe-separated field names |
| `include_errors` | boolean | No | Include errors in output |
| `notify` | string | No | Webhook URL to receive completion notification |
| `output` | object | No | External storage delivery config (S3, GCS, etc.) |

### Python Example (Trigger + Poll)

```python
import requests
import time

# Step 1: Trigger the job
trigger_response = requests.post(
    "https://api.brightdata.com/datasets/v3/trigger",
    params={
        "dataset_id": "gd_l7q7dkf244hwjntr0",
        "format": "json"
    },
    headers={
        "Authorization": f"Bearer {API_KEY}",
        "Content-Type": "application/json"
    },
    json={
        "input": [
            {"url": "https://www.amazon.com/dp/B09X7M8TBQ"},
            # ... hundreds more URLs
        ]
    }
)
snapshot_id = trigger_response.json()["snapshot_id"]

# Step 2: Poll until ready
while True:
    progress = requests.get(
        f"https://api.brightdata.com/datasets/v3/progress/{snapshot_id}",
        headers={"Authorization": f"Bearer {API_KEY}"}
    )
    status = progress.json()["status"]
    print(f"Status: {status}")

    if status == "ready":
        break
    elif status == "failed":
        raise Exception("Scraping job failed")

    time.sleep(10)

# Step 3: Download results
results = requests.get(
    f"https://api.brightdata.com/datasets/v3/snapshot/{snapshot_id}",
    params={"format": "json"},
    headers={"Authorization": f"Bearer {API_KEY}"}
)
data = results.json()
```

---

## Monitor Progress

**Endpoint:** `GET https://api.brightdata.com/datasets/v3/progress/{snapshot_id}`

```python
response = requests.get(
    f"https://api.brightdata.com/datasets/v3/progress/{snapshot_id}",
    headers={"Authorization": f"Bearer {API_KEY}"}
)
status = response.json()["status"]
```

### Status Values

| Status | Description |
|--------|-------------|
| `starting` | Job initialization |
| `running` | Data collection in progress |
| `ready` | Results available for download |
| `failed` | Job failed |

### Error Responses

| Code | Meaning |
|------|---------|
| `401` | Missing or invalid API key |
| `404` | Snapshot ID not found |

---

## Download Results

**Endpoint:** `GET https://api.brightdata.com/datasets/v3/snapshot/{snapshot_id}`

```python
response = requests.get(
    f"https://api.brightdata.com/datasets/v3/snapshot/{snapshot_id}",
    params={"format": "json"},
    headers={"Authorization": f"Bearer {API_KEY}"}
)
data = response.json()
```

### Snapshot Lifecycle
- Snapshots are available for **30 days** after collection
- Download in JSON, NDJSON, JSONL, or CSV format

---

## Scraper Types

The Scraper Library contains pre-built scrapers organized by type:

### PDP Scrapers (Product/Profile Detail)
- Accept one or more URLs
- Return detailed data for each URL
- Example: Amazon product page → price, title, reviews, specs

### Discovery Scrapers
- Accept search terms, keywords, or category URLs
- Return lists of results to explore
- Example: Amazon search → list of matching products

### Finding Dataset IDs
1. Go to `https://brightdata.com/cp/datasets` (Scraper Library)
2. Select the platform and data type you need
3. Each scraper has a unique `dataset_id` shown in the API reference

---

## Output Formats

| Format | Description |
|--------|-------------|
| `json` | Standard JSON array (default) |
| `ndjson` | Newline-delimited JSON (one object per line) — good for streaming large results |
| `jsonl` | Same as ndjson |
| `csv` | CSV format |

### Custom Output Fields

Filter returned fields to reduce payload size:

```python
params = {
    "dataset_id": "gd_l7q7dkf244hwjntr0",
    "format": "json",
    "custom_output_fields": "url|title|price|rating"  # pipe-separated
}
```

Nested fields use dot notation: `about.updated_on`

---

## Billing Model

| Scenario | Billing |
|----------|---------|
| Standard | Per **delivered record** — starting from $0.70/1,000 records |
| Failed due to user input error | **Billable** — resources were consumed processing the invalid input |
| Sync timeout (202) → async retrieval | Single charge for the records, not double |
| Real-time mode | Up to 20 URL inputs per call |

**Data retention:** Collected snapshots available for **30 days**.

---

## Best Practices

### 1. Use sync for ≤20 URLs, async for larger batches
Sync is simpler for small jobs. For anything larger, use `/trigger` with polling.

```python
if len(urls) <= 20:
    # Use /scrape for immediate results
    endpoint = "https://api.brightdata.com/datasets/v3/scrape"
else:
    # Use /trigger for bulk
    endpoint = "https://api.brightdata.com/datasets/v3/trigger"
```

### 2. Handle 202 responses in sync mode
If your sync request takes >1 minute, you'll get a 202 with `snapshot_id`. Always handle this case:

```python
if response.status_code == 202:
    snapshot_id = response.json()["snapshot_id"]
    # Fall through to polling logic
```

### 3. Use webhooks for production async workflows
Polling is fine for development. In production, configure `notify` URL to receive push notifications:

```python
json={
    "input": [...],
    "notify": "https://your-server.com/webhook/brightdata"
}
```

### 4. Use `custom_output_fields` to reduce payload
Only request fields you need. This reduces bandwidth and response size:

```python
params={"custom_output_fields": "url|title|price|availability"}
```

### 5. Use `ndjson` format for large result sets
NDJSON is more memory-efficient for large datasets since you can stream-process line by line:

```python
for line in response.iter_lines():
    record = json.loads(line)
    process(record)
```

### 6. Check data retention (30 days)
Download your snapshots within 30 days. After that, the data is gone.

### 7. Validate inputs before submitting
Submitting invalid URLs/inputs that fail due to user error is still billable. Validate URLs before sending:

```python
from urllib.parse import urlparse

def is_valid_url(url: str) -> bool:
    parsed = urlparse(url)
    return parsed.scheme in ("http", "https") and bool(parsed.netloc)

urls = [u for u in raw_urls if is_valid_url(u)]
```

### 8. Use delivery to external storage for large jobs
Instead of downloading via the API, configure delivery to S3/GCS in the trigger request for large datasets:

```python
json={
    "input": [...],
    "output": {
        "type": "s3",
        "bucket": "your-bucket",
        "prefix": "brightdata/results/"
    }
}
```




### Web Unlocker

# Web Unlocker API Reference

## Table of Contents

- [Overview](#overview)
- [Authentication](#authentication)
- [REST API (Recommended)](#rest-api-recommended)
- [Proxy Interface](#proxy-interface)
- [All Request Parameters](#all-request-parameters)
- [Special Headers](#special-headers)
- [Response Structure](#response-structure)
- [Async Requests](#async-requests)
- [Output Formats](#output-formats)
- [Features](#features)
- [Billing Model](#billing-model)
- [Best Practices](#best-practices)
- [Anti-Patterns](#anti-patterns)

---

## Overview

Web Unlocker is Bright Data's HTTP-based unlocking proxy. It automatically selects the best proxy network, handles anti-bot protections, solves CAPTCHAs, and retries failed attempts. Use it for non-interactive data extraction where you need HTML, JSON, markdown, or screenshots of web pages via a simple HTTP request.

**When to use Web Unlocker vs other APIs:**

| Need | Use |
|------|-----|
| Scrape any webpage by URL | Web Unlocker |
| Search engine results (Google, Bing) | SERP API |
| Structured data from known platforms | Web Scraper API |
| Click, scroll, fill forms, run JS | Browser API |
| Browser automation libraries (Playwright/Puppeteer) | Browser API, NOT Web Unlocker |

---

## Authentication

Get your API key from the Bright Data Control Panel → Account Settings, or from your zone's Overview tab.

```bash
# Environment variable (recommended)
export BRIGHTDATA_API_KEY="your-api-key"
export BRIGHTDATA_UNLOCKER_ZONE="your-zone-name"
```

---

## REST API (Recommended)

**Endpoint:** `POST https://api.brightdata.com/request`

**Header:** `Authorization: Bearer YOUR_API_KEY`

### Minimal Request

```python
import requests

response = requests.post(
    "https://api.brightdata.com/request",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={
        "zone": "YOUR_ZONE_NAME",
        "url": "https://example.com",
        "format": "raw"
    }
)
print(response.text)
```

```javascript
const response = await fetch("https://api.brightdata.com/request", {
  method: "POST",
  headers: {
    "Authorization": `Bearer ${API_KEY}`,
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    zone: "YOUR_ZONE_NAME",
    url: "https://example.com",
    format: "raw"
  })
});
const html = await response.text();
```

```bash
curl -H "Authorization: Bearer $BRIGHTDATA_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{"zone":"'"$BRIGHTDATA_UNLOCKER_ZONE"'","url":"https://example.com","format":"raw"}' \
     https://api.brightdata.com/request
```

---

## Proxy Interface

Alternative to the REST API — route requests through a proxy endpoint.

- **Host:** `brd.superproxy.io`
- **Port:** `33335`
- **Credentials:** `brd-customer-{CUSTOMER_ID}-zone-{ZONE_NAME}:{ZONE_PASSWORD}`

```python
import requests

proxies = {
    "http": "http://brd-customer-CUSTOMER_ID-zone-ZONE_NAME:PASSWORD@brd.superproxy.io:33335",
    "https": "http://brd-customer-CUSTOMER_ID-zone-ZONE_NAME:PASSWORD@brd.superproxy.io:33335"
}
# Install Bright Data's SSL certificate to avoid SSL errors
response = requests.get("https://example.com", proxies=proxies, verify="/path/to/brightdata-cert.crt")
```

Special proxy username flags (append to username string):
- `-country-XX` → Geo-target to country (e.g., `-country-us`)
- `-ua-mobile` → Use mobile user agent
- `-debug-full` → Enable debug header in response

---

## All Request Parameters

### REST API Body Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `zone` | string | Yes | Zone name from your Control Panel |
| `url` | string | Yes | Target URL (must include `http://` or `https://`) |
| `format` | string | Yes | `"raw"` (HTML string) or `"json"` (structured JSON) |
| `method` | string | No | HTTP verb, default `"GET"`. Use `"POST"` with `body` for POST requests |
| `body` | string | No | POST body payload (use with `"method": "POST"`) |
| `country` | string | No | 2-letter ISO country code for geo-targeting (e.g., `"us"`, `"gb"`). Auto-selected if omitted |
| `data_format` | string | No | Transform output: `"markdown"` or `"screenshot"` |
| `async` | boolean | No | Set `true` to use asynchronous mode. Returns `response_id` immediately |

---

## Special Headers

Used with the **proxy interface**. Pass in proxy username or as custom request headers.

| Header | Value | Description |
|--------|-------|-------------|
| `x-unblock-data-format` | `"markdown"` or `"screenshot"` | Convert response to markdown or PNG screenshot |
| `x-unblock-expect` | CSS selector, text, or body content | Wait for this element/text before returning response. Prevents partial page loads |
| `x-unblock-url-fragment` | The `#fragment` part of URL | Handle hash-fragmented URLs in a single request |
| `x-unblock-city` | City name string | Amazon-specific: simulate city selection |
| `x-unblock-zipcode` | ZIP code string | Amazon-specific: simulate ZIP code for region-specific content |

**Proxy username flags** (appended to username):
- `-ua-mobile` → Uses mobile user agents instead of desktop
- `-debug-full` → Adds `x-brd-debug` response header with: request ID, traffic metrics, billing status, destination IP, headers used, peer info, render status

---

## Response Structure

### Synchronous (format: "json")

```json
{
  "status": 200,
  "headers": {
    "content-type": "text/html",
    "...": "..."
  },
  "body": "<html>...</html>"
}
```

### Synchronous (format: "raw")

Returns the raw HTML/content as a string directly.

### Error Codes

| Code | Meaning | Action |
|------|---------|--------|
| `200` | Success | Process response |
| `400` | Bad Request | Check required fields: `zone`, `url`, `format` |
| `401` | Unauthorized | Verify API key |

---

## Async Requests

### Setup

Enable in Control Panel: Your Zone → Advanced Options → Toggle "Asynchronous requests" ON.

### Flow

**Step 1: Submit request**

```python
response = requests.post(
    "https://api.brightdata.com/unblocker/req",
    params={"zone": "YOUR_ZONE_NAME"},
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"url": "https://example.com"}
)
response_id = response.json()["response_id"]
```

**Step 2: Poll for result**

```python
import time

while True:
    result = requests.get(
        "https://api.brightdata.com/unblocker/get_result",
        params={"response_id": response_id},
        headers={"Authorization": f"Bearer {API_KEY}"}
    )
    if result.status_code == 200:
        print(result.json()["body"])
        break
    time.sleep(5)
```

### Async Key Facts
- Responses typically complete within **5 minutes**, up to **8 hours** during peak
- Results stored for **48 hours**
- Better for slow-responding sites or large-scale URL processing
- Use webhooks for production: set webhook URL in zone settings to receive push notifications

---

## Output Formats

### HTML (default)
Raw HTML string. Use `format: "raw"`.

### Markdown
Clean markdown extracted from the page. Best for LLM consumption.

**REST API:**
```json
{ "zone": "...", "url": "...", "format": "raw", "data_format": "markdown" }
```

**Proxy:**
Add header: `x-unblock-data-format: markdown`

### Screenshot
PNG screenshot of the rendered page. Useful for debugging and visual verification.

**REST API:**
```json
{ "zone": "...", "url": "...", "format": "raw", "data_format": "screenshot" }
```

---

## Features

### CAPTCHA Solving
Enabled by default. Automatically solves CAPTCHAs encountered during requests. Can be disabled in Advanced Settings for a lightweight solution when CAPTCHA solving isn't needed.

### Premium Domains
Certain high-difficulty websites require additional resources. Enable "Premium Domains" in zone settings during creation. Only requests targeting premium-classified domains are billed at the higher rate.

### Geolocation Targeting
Auto-selects optimal IP location. Override with `country` parameter (REST API) or `-country-XX` (proxy). Country-level targeting only (not city-level—use Browser API for that).

### Mobile User Agent
Append `-ua-mobile` to proxy username, or use via proxy username flag, to use mobile-specific user agents.

### Auto-Throttling
System automatically adjusts based on success rates:
- Default threshold: 70% success rate
- Automatically applies better-performing configurations
- When custom headers/cookies are enabled: customizable threshold

### Debug Header
Add `-debug-full` to proxy username to receive `x-brd-debug` response header containing: request ID, traffic metrics, billing status, destination IP, headers used, peer info, render status.

### Success Rate API
Query domain-specific success rates for the past 7 days:
```bash
GET https://api.brightdata.com/unblocker/success_rate/?zone=YOUR_ZONE&domain=example.com
Authorization: Bearer YOUR_API_KEY
```

---

## Custom Headers & Cookies (Advanced)

Override automatically-generated headers and cookies.

**When to use:** When you need to pass specific session cookies, authentication headers, or custom values to reach a particular site version.

**How to enable:** Control Panel → Your Zone → Advanced Options → Toggle "Custom Headers & Cookies" ON.

**Critical billing note:** Enabling custom headers/cookies means you are **billed for 100% of requests** (both successful and failed), because you are taking control of request parameters. Standard mode only bills for successes.

**Restrictions:**
- Custom values must be from a compliance-pre-approved list
- Unlisted values require approval from the compliance team
- Cannot pass authentication/login credentials

---

## Billing Model

| Mode | Billing |
|------|---------|
| Standard | CPM — billed per 1,000 **successful** requests only |
| Custom Headers/Cookies enabled | Billed for **all** requests (successful + failed) |
| Async collect/retrieve calls | **Not billed** — only the initial submission is billed |

Monitor usage via the "Traffic" column in My Proxies (CPM = cost per 1,000 successful requests).

---

## Best Practices

### 1. Start with direct API endpoint targeting
Many sites expose clean API endpoints. Try hitting the API directly first — it often succeeds without extra config and is cheaper.

```python
# Try the API endpoint first
response = requests.post(
    "https://api.brightdata.com/request",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"zone": ZONE, "url": "https://site.com/api/products.json", "format": "raw"}
)
```

### 2. Fall back to main webpage if API fails
If the direct API endpoint fails, scrape the primary webpage instead.

### 3. Use Browser API for complex JS-heavy scenarios
When you need to execute JavaScript, click elements, or interact with the page — don't try to force Web Unlocker. Use Browser API.

### 4. Use `x-unblock-expect` to prevent partial loads
For pages that load content progressively, specify an expected element to ensure the content you need is present.

```json
{
  "zone": "...",
  "url": "https://example.com/products",
  "format": "raw",
  "headers": { "x-unblock-expect": ".product-list" }
}
```

### 5. Use markdown format for LLM pipelines
When feeding scraped content to an LLM, use `data_format: "markdown"` to get clean, structured text without HTML noise.

### 6. Use async for bulk/large-scale processing
Async improves stability for slow sites and enables processing large batches of URLs without blocking.

### 7. Use geolocation for region-restricted content
Pass `country` parameter when you need region-specific content (prices, availability, localized pages).

```json
{ "zone": "...", "url": "https://example.com", "format": "raw", "country": "de" }
```

### 8. Monitor success rates before custom header mode
Check your domain success rates via the API before enabling custom headers (which changes billing to 100%).

---

## Anti-Patterns

**DO NOT use Web Unlocker with browser automation libraries.**
- Puppeteer, Playwright, Selenium → use **Browser API** instead
- Chrome, Firefox, Edge → use **Bright Data proxy networks** (Residential, ISP, etc.)
- Anti-detect browsers (Adspower, Multilogin) → use proxy networks

Web Unlocker is optimized for singular HTTP requests, not browser sessions.

**DO NOT enable custom headers unless necessary.**
Enabling custom headers changes billing from success-only to 100% of all requests.

**DO NOT ignore the `x-unblock-expect` header for paginated/dynamic content.**
Without it, you may receive partial pages before JavaScript has populated content.




### Serp Api

# SERP API Reference

## Table of Contents

- [Overview](#overview)
- [Authentication](#authentication)
- [REST API (Recommended)](#rest-api-recommended)
- [Proxy Interface](#proxy-interface)
- [Request Parameters](#request-parameters)
- [Google Search Parameters](#google-search-parameters)
- [Bing Search Parameters](#bing-search-parameters)
- [Parsed JSON Output](#parsed-json-output)
- [Async Requests](#async-requests)
- [Billing Model](#billing-model)
- [Best Practices](#best-practices)

---

## Overview

Bright Data SERP API extracts structured search engine results from Google, Bing, Yandex, and DuckDuckGo. It automatically handles proxy management, CAPTCHA solving, and delivers results in under 5 seconds.

**What it returns:**
- Organic results (title, description, link, rank)
- Paid advertisements (top, bottom, product listing, premium)
- Local business listings (snack pack)
- Shopping results
- Related searches and "People Also Ask"
- Knowledge panels
- Specialized SERP features (maps, trends, reviews, lens, hotels, flights)

---

## Authentication

```bash
export BRIGHTDATA_API_KEY="your-api-key"
export BRIGHTDATA_SERP_ZONE="your-serp-zone-name"
```

API keys are auto-generated when you create a SERP API zone. Additional keys can be generated in Account Settings. Setting expiration dates on keys is recommended for security.

---

## REST API (Recommended)

**Endpoint:** `POST https://api.brightdata.com/request`

**Header:** `Authorization: Bearer YOUR_API_KEY`

### Google Search

```python
import requests

response = requests.post(
    "https://api.brightdata.com/request",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={
        "zone": "YOUR_SERP_ZONE",
        "url": "https://www.google.com/search?q=python+web+scraping",
        "format": "raw"
    }
)
html = response.text
```

### Google Search with Parsed JSON

```python
response = requests.post(
    "https://api.brightdata.com/request",
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={
        "zone": "YOUR_SERP_ZONE",
        "url": "https://www.google.com/search?q=python+web+scraping&brd_json=1",
        "format": "raw"
    }
)
data = response.json()
for result in data.get("organic", []):
    print(result["title"], result["link"])
```

```javascript
const response = await fetch("https://api.brightdata.com/request", {
  method: "POST",
  headers: {
    "Authorization": `Bearer ${API_KEY}`,
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    zone: "YOUR_SERP_ZONE",
    url: "https://www.google.com/search?q=python+web+scraping&brd_json=1",
    format: "raw"
  })
});
const data = await response.json();
```

```bash
curl -H "Authorization: Bearer $BRIGHTDATA_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{"zone":"'"$BRIGHTDATA_SERP_ZONE"'","url":"https://www.google.com/search?q=python+web+scraping&brd_json=1","format":"raw"}' \
     https://api.brightdata.com/request
```

---

## Proxy Interface

Route search requests through Bright Data's proxy endpoint.

- **Host:** `brd.superproxy.io`
- **Port:** `33335`
- **Credentials:** `brd-customer-{CUSTOMER_ID}-zone-{ZONE_NAME}:{ZONE_PASSWORD}`

```python
proxies = {
    "http": "http://brd-customer-CUSTOMER_ID-zone-ZONE_NAME:PASSWORD@brd.superproxy.io:33335",
    "https": "http://brd-customer-CUSTOMER_ID-zone-ZONE_NAME:PASSWORD@brd.superproxy.io:33335"
}
response = requests.get(
    "https://www.google.com/search?q=python+web+scraping&brd_json=1",
    proxies=proxies,
    verify="/path/to/brightdata-cert.crt"  # Install Bright Data SSL cert
)
```

---

## Request Parameters

Core parameters shared across all SERP endpoints:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `zone` | string | Yes | SERP zone name |
| `url` | string | Yes | Full search URL with query params included |
| `format` | string | Yes | `"raw"` (HTML/JSON) or `"json"` (structured response wrapper) |
| `country` | string | No | 2-letter ISO country code for proxy geo-targeting |
| `async` | boolean | No | `true` for async mode |

---

## Google Search Parameters

All parameters are appended to the Google URL as query string parameters.

### Localization

| Parameter | Description | Example |
|-----------|-------------|---------|
| `gl` | 2-letter country code for search location | `gl=us` |
| `hl` | 2-letter language code for page language | `hl=en` |

### Search Type

| Parameter | Description | Values |
|-----------|-------------|--------|
| `tbm` | Search type | `isch` (images), `nws` (news), `vid` (videos) |
| `udm` | Alternative search types | `28` (shopping), `39` (short videos) |
| `ibp` | Jobs search | `htl;jobs` |

### Pagination

| Parameter | Description | Example |
|-----------|-------------|---------|
| `start` | Result offset | `0`, `10`, `20` (each page = 10) |
| `num` | Number of results | **Deprecated** as of September 2025 |

### Geo-Location

| Parameter | Description |
|-----------|-------------|
| `uule` | Encoded location string for precise geo-targeting |

### Device & Browser

| Parameter | Description | Values |
|-----------|-------------|--------|
| `brd_mobile` | Device type | `0` (desktop), `1` (random mobile), `ios`, `ipad`, `android`, `android_tablet` |
| `brd_browser` | Browser type | `chrome`, `safari`, `firefox` |

### Output Format

| Parameter | Description | Values |
|-----------|-------------|--------|
| `brd_json` | Enable parsed JSON output | `1` (JSON), `html` (JSON + raw HTML) |

### Special Features

| Parameter | Description | Values |
|-----------|-------------|--------|
| `brd_ai_overview` | Increase likelihood of AI Overview results | `2` |

### Hotel Search (via Google Search URL)

| Parameter | Description | Example |
|-----------|-------------|---------|
| `hotel_occupancy` | Number of guests | `1`–`4` |
| `hotel_dates` | Check-in/check-out | `2025-06-01,2025-06-07` |

---

## Google Maps Parameters

Append to `https://www.google.com/maps/search/...`:

| Parameter | Description |
|-----------|-------------|
| `gl`, `hl` | Localization |
| `@latitude,longitude,zoom` | GPS coordinates for location search |
| `fid` | Feature ID for place overview |
| `brd_accomodation_type` | Filter: `hotels` or `vacation_rentals` |

---

## Google Trends Parameters

Append to `https://trends.google.com/trends/explore`:

| Parameter | Description | Values |
|-----------|-------------|--------|
| `brd_json` | Required — returns parsed results | `1` |
| `brd_trends` | Widget type | `timeseries`, `geo_map`, or both |
| `geo` | 2-letter country code | `us`, `gb` |
| `hl` | Language code | `en` |
| `date` | Time range | `now 1-H`, `today 12-m`, custom dates |
| `cat` | Category ID | integer |
| `gprop` | Google property filter | `images`, `news`, `froogle`, `youtube` |

---

## Google Reviews Parameters

| Parameter | Description | Values |
|-----------|-------------|--------|
| `fid` | Feature ID for the place | string |
| `hl` | Language code | `en` |
| `sort` | Sort method | `qualityScore`, `newestFirst`, `ratingHigh`, `ratingLow` |
| `filter` | Keyword filter | string |
| `start` | Pagination offset | integer |
| `num` | Results per page | max `20` |

---

## Google Lens Parameters

| Parameter | Description | Values |
|-----------|-------------|--------|
| `url` | Image URL for reverse search | string |
| `hl` | Language code | `en` |
| `brd_lens` | Specific tab results | `products`, `homework`, `visual_matches`, `exact_matches` |

---

## Google Hotels Parameters

| Parameter | Description | Values |
|-----------|-------------|--------|
| `gl`, `hl` | Localization | ISO codes |
| `brd_dates` | Check-in/check-out dates | `YYYY-MM-DD,YYYY-MM-DD` |
| `brd_occupancy` | Guest count or breakdown | `2` or `2,5,7` (adults,child-ages) |
| `brd_free_cancellation` | Filter for free cancellation | `true`/`false` |
| `brd_accomodation_type` | Accommodation type | string |
| `brd_currency` | 3-letter currency code | `USD`, `EUR` |
| `brd_mobile` | Device type | `0`, `1`, `ios`, etc. |
| `brd_json` | Output format | `1` |

---

## Google Flights Parameters

| Parameter | Description |
|-----------|-------------|
| `gl`, `hl` | Localization |
| `tfs` | Flight search parameter string |
| `curr` | Currency code for prices |

---

## Bing Search Parameters

**Note:** Microsoft retired Bing Search APIs on August 11, 2025. Bright Data continues supporting their SERP API for Bing domain.

| Parameter | Description | Values |
|-----------|-------------|--------|
| `setLang` | Language code | `en-US`, `fr-FR` (4-letter preferred) |
| `location` | Used with latitude/longitude | paired with `mkt` |
| `cc` | 2-character country code | `us`, `gb` |
| `mkt` | Market specification | `en-US` |
| `first` | Result offset (pagination) | `1`, `11`, `21` (increment by 10) |
| `safesearch` | Adult content filter | `off`, `moderate` (default), `strict` |
| `brd_mobile` | Device type | `0`, `1`, `ios`, `android`, etc. |
| `brd_browser` | Browser type | `chrome`, `safari`, `firefox` |

---

## Parsed JSON Output

Add `brd_json=1` to the Google search URL to receive structured JSON instead of HTML.

```python
url = "https://www.google.com/search?q=best+laptops+2025&brd_json=1&gl=us&hl=en"
```

### Response Structure

```json
{
  "general": {
    "search_engine": "google",
    "query": "best laptops 2025",
    "results_cnt": 1240000000,
    "language": "en",
    "device": "desktop"
  },
  "organic": [
    {
      "rank": 1,
      "global_rank": 1,
      "title": "Best Laptops 2025",
      "link": "https://example.com/best-laptops",
      "description": "...",
      "sitelinks": []
    }
  ],
  "paid": [],
  "product_listing_ads": [],
  "knowledge_graph": {},
  "people_also_ask": [],
  "related_searches": [],
  "maps": [],
  "news": [],
  "videos": [],
  "recipes": [],
  "perspectives": []
}
```

### Key Fields

| Field | Description |
|-------|-------------|
| `organic[].rank` | Position within organic results component |
| `organic[].global_rank` | Overall position across entire SERP |
| `organic[].title` | Page title |
| `organic[].link` | URL |
| `organic[].description` | Snippet |
| `general.results_cnt` | Total results count (desktop only — not available on mobile) |

For raw HTML + parsed JSON: use `brd_json=html`.

---

## Async Requests

### Setup
Enable in Control Panel: SERP zone → Advanced Options → Toggle "Asynchronous requests" ON.

**Webhook allowlist IPs** (add these to your server firewall):
- `100.27.150.189`
- `18.214.10.85`

### Async Flow

**Step 1: Submit request**

```python
response = requests.post(
    "https://api.brightdata.com/request",
    params={"async": "1"},
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={
        "zone": "YOUR_SERP_ZONE",
        "url": "https://www.google.com/search?q=python+web+scraping&brd_json=1",
        "format": "raw"
    }
)
response_id = response.headers.get("x-response-id")
```

**Step 2: Retrieve result**

```python
result = requests.get(
    "https://api.brightdata.com/serp/get_result",
    params={"response_id": response_id},
    headers={"Authorization": f"Bearer {API_KEY}"}
)
data = result.json()
```

### Async Key Facts
- Responses typically complete within **5 minutes**, stored for **48 hours**
- `99.99%` success rate in async mode
- Retrieve result using `response_id` from `x-response-id` header
- Collect/retrieve calls are **not billed** — only the initial submission

---

## Billing Model

| Mode | Billing |
|------|---------|
| Standard | Per 1,000 **successful** requests only |
| Async collect/retrieve | **Not billed** — only submission is billed |
| Automatic retries | Charged once for the successful response, not per retry |
| Failed requests | **Not charged** |

What's included at no extra cost: parsing (JSON/Markdown/HTML), proxy management, CAPTCHA handling, geotargeting, desktop and mobile user agent support.

---

## Best Practices

### 1. Always use `brd_json=1` for data pipelines
HTML parsing is brittle. Use `brd_json=1` to get structured data that won't break when Google changes its layout.

```python
url = "https://www.google.com/search?q=your+query&brd_json=1&gl=us&hl=en"
```

### 2. Set locale parameters (`gl` + `hl`) for consistent results
Without locale params, results vary by server location. Always set both for reproducible, region-correct results.

```python
# Good: explicit locale
"url": "https://www.google.com/search?q=coffee+shops&gl=us&hl=en"

# Bad: no locale, results depend on IP
"url": "https://www.google.com/search?q=coffee+shops"
```

### 3. Use `brd_mobile` for mobile SERP data
Mobile and desktop SERPs differ significantly. Match your target audience.

```python
# Mobile results
"url": "https://www.google.com/search?q=restaurants+near+me&brd_mobile=1&brd_json=1"
```

### 4. Paginate with `start` parameter
Each page offset is 10 results. To get page 3: `start=20`.

```python
for page in range(5):
    url = f"https://www.google.com/search?q=python+tutorials&brd_json=1&start={page * 10}"
```

### 5. Use async for high-volume pipelines
For bulk queries (monitoring rankings, scraping many keywords), async mode gives `99.99%` success rate and you're not charged for collect/retrieve calls.

### 6. Use `brd_ai_overview=2` when AI Overview data is needed
This parameter increases the likelihood that Google's AI Overview appears in results.

### 7. Filter by search type with `tbm`
Don't scrape the wrong SERP type. Use `tbm=nws` for news, `tbm=isch` for images, etc.

### 8. Use `Enhanced Ads` zone setting for ad-heavy research
Enable "Enhanced Ads" in your zone settings to fetch a larger, more diverse range of ads, simulating incognito browsing.

### 9. Note: `num` parameter is deprecated
As of September 2025, the `num` parameter no longer controls result count. Use pagination via `start` instead.




### Browser Api

# Browser API (Scraping Browser) Reference

## Table of Contents

- [Overview](#overview)
- [Authentication](#authentication)
- [Connection Strings](#connection-strings)
- [Supported Frameworks](#supported-frameworks)
- [Session Rules](#session-rules)
- [Quick Start Examples](#quick-start-examples)
- [Custom CDP Functions](#custom-cdp-functions)
- [Geolocation Targeting](#geolocation-targeting)
- [Bandwidth Optimization](#bandwidth-optimization)
- [CAPTCHA Handling](#captcha-handling)
- [Debugging](#debugging)
- [Premium Domains](#premium-domains)
- [Error Codes](#error-codes)
- [Billing Model](#billing-model)
- [Best Practices](#best-practices)
- [Anti-Patterns](#anti-patterns)

---

## Overview

Bright Data Browser API (also called Scraping Browser) is a managed cloud browser service. It handles proxy management, fingerprinting, CAPTCHA solving, and bot detection bypass automatically. Use it when you need full browser automation: clicking, scrolling, form filling, JavaScript execution, or working with SPAs.

**When to use Browser API vs other APIs:**

| Need | Use |
|------|-----|
| Simple HTTP scraping, no interaction | Web Unlocker |
| Google/Bing search results | SERP API |
| Structured data from known platforms | Web Scraper API |
| Click, scroll, fill forms, run JS | **Browser API** |
| Intercept XHR/fetch calls from a page | **Browser API** |
| Handle complex anti-bot that requires browser | **Browser API** |
| Puppeteer/Playwright/Selenium automation | **Browser API** |

---

## Authentication

Get credentials from your Browser API zone's **Overview tab** in the Control Panel.

- **Username:** `brd-customer-{CUSTOMER_ID}-zone-{ZONE_NAME}`
- **Password:** Your zone password

```bash
export BROWSER_AUTH="brd-customer-CUSTOMER_ID-zone-ZONE_NAME:PASSWORD"
```

---

## Connection Strings

| Framework | Connection Type | Endpoint |
|-----------|----------------|----------|
| Playwright | WebSocket | `wss://${AUTH}@brd.superproxy.io:9222` |
| Puppeteer | WebSocket | `wss://${AUTH}@brd.superproxy.io:9222` |
| Selenium | HTTPS | `https://${AUTH}@brd.superproxy.io:9515` |

Replace `${AUTH}` with `username:password`.

**Critical:** Wrong port = 407 error. Playwright/Puppeteer = port `9222`. Selenium = port `9515`.

---

## Supported Frameworks

- **Node.js:** Puppeteer, Playwright, Selenium WebDriver
- **Python:** Playwright, Selenium
- **C# (.NET):** PuppeteerSharp, Playwright, Selenium
- **Other languages:** Any language with CDP or WebDriver support (Ruby, Go, Java, etc.)

---

## Session Rules

- **One initial navigation per session.** After `page.goto(url)`, you can interact (click, scroll, evaluate) within the same page, but cannot navigate to a different URL in the same session. Start a new session for a new target.
- **Idle timeout:** Sessions inactive for **5+ minutes** automatically close.
- **Maximum duration:** Sessions cannot exceed **30 minutes**.
- **Password entry:** Disabled by default to protect non-public data. Contact Bright Data to enable.

---

## Quick Start Examples

### Playwright (Node.js)

```javascript
const { chromium } = require("playwright-core");

const AUTH = process.env.BROWSER_AUTH || "brd-customer-CUSTOMER_ID-zone-ZONE_NAME:PASSWORD";
const TARGET_URL = "https://example.com";

(async () => {
  const browser = await chromium.connectOverCDP(
    `wss://${AUTH}@brd.superproxy.io:9222`
  );
  const page = await browser.newPage();

  // Set navigation timeout to 2 minutes (recommended for complex unlocking)
  page.setDefaultNavigationTimeout(120000);

  await page.goto(TARGET_URL, { waitUntil: "domcontentloaded" });
  const html = await page.content();
  console.log(html);

  await browser.close();
})();
```

### Playwright (Python)

```python
import asyncio
from playwright.async_api import async_playwright

AUTH = "brd-customer-CUSTOMER_ID-zone-ZONE_NAME:PASSWORD"
TARGET_URL = "https://example.com"

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.connect_over_cdp(
            f"wss://{AUTH}@brd.superproxy.io:9222"
        )
        page = await browser.new_page()
        page.set_default_navigation_timeout(120000)

        await page.goto(TARGET_URL, wait_until="domcontentloaded")
        html = await page.content()
        print(html)

        await browser.close()

asyncio.run(main())
```

### Puppeteer (Node.js)

```javascript
const puppeteer = require("puppeteer-core");

const AUTH = process.env.BROWSER_AUTH || "brd-customer-CUSTOMER_ID-zone-ZONE_NAME:PASSWORD";

(async () => {
  const browser = await puppeteer.connect({
    browserWSEndpoint: `wss://${AUTH}@brd.superproxy.io:9222`
  });
  const page = await browser.newPage();
  page.setDefaultNavigationTimeout(120000);

  await page.goto("https://example.com", { waitUntil: "domcontentloaded" });
  const html = await page.content();
  console.log(html);

  await browser.close();
})();
```

### Selenium (Python)

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

AUTH = "brd-customer-CUSTOMER_ID-zone-ZONE_NAME:PASSWORD"

options = Options()
options.add_argument("--ignore-certificate-errors")

driver = webdriver.Remote(
    command_executor=f"https://{AUTH}@brd.superproxy.io:9515",
    options=options
)
driver.get("https://example.com")
print(driver.page_source)
driver.quit()
```

---

## Custom CDP Functions

Bright Data extends standard CDP with specialized commands. Use `page.evaluate` or `client.send` to invoke them.

### CAPTCHA Handling

#### `Captcha.setAutoSolve`
Control automatic CAPTCHA solving.

```javascript
// Disable auto-solve (use manual control instead)
const client = await page.target().createCDPSession();
await client.send("Captcha.setAutoSolve", { autoSolve: false });
```

#### `Captcha.solve`
Manually trigger CAPTCHA solving and wait for completion.

```javascript
const client = await page.target().createCDPSession();
const result = await client.send("Captcha.solve", { timeout: 30000 });
console.log(result.status); // "solved" | "not_detected" | "timeout"
```

```python
# Python equivalent
client = await page.context.new_cdp_session(page)
result = await client.send("Captcha.solve", {"timeout": 30000})
print(result["status"])
```

### Geolocation

#### `Proxy.setLocation`
Set precise proxy location by coordinates. **Must be called before navigating to the site.**

```javascript
const client = await page.target().createCDPSession();
await client.send("Proxy.setLocation", {
  latitude: 37.7749,
  longitude: -122.4194,
  distance: 10,   // Search radius in km
  strict: true    // true = only peers within distance; false = expand if none found
});
await page.goto("https://example.com");
```

```python
client = await page.context.new_cdp_session(page)
await client.send("Proxy.setLocation", {
    "latitude": 37.7749,
    "longitude": -122.4194,
    "distance": 10,
    "strict": True
})
await page.goto("https://example.com")
```

### Session Management

#### `Proxy.useSession`
Maintain consistent proxy peer across multiple browsing sessions (same IP continuity).

```javascript
const client = await page.target().createCDPSession();
await client.send("Proxy.useSession", { sessionId: "my-session-123" });
```

### Device Emulation

#### `Emulation.getSupportedDevices`
Get list of available device profiles.

```javascript
const client = await page.target().createCDPSession();
const { devices } = await client.send("Emulation.getSupportedDevices");
console.log(devices); // ["iPhone 14", "Samsung Galaxy S21", ...]
```

#### `Emulation.setDevice`
Apply a specific device profile (user agent, screen size, touch).

```javascript
await client.send("Emulation.setDevice", {
  deviceName: "iPhone 14"
});
// Optional: landscape orientation
await client.send("Emulation.setDevice", {
  deviceName: "iPhone 14",
  landscape: true
});
```

### Ad Blocking

#### `Unblocker.enableAdBlock` / `Unblocker.disableAdBlock`
Block/unblock ads to reduce bandwidth on content-heavy sites.

```javascript
const client = await page.target().createCDPSession();
await client.send("Unblocker.enableAdBlock");
// ... scrape page ...
await client.send("Unblocker.disableAdBlock"); // if needed later
```

### Input Acceleration

#### `Input.type`
Faster text input than standard keyboard simulation. Useful for bulk form-filling.

```javascript
const client = await page.target().createCDPSession();
await client.send("Input.type", {
  text: "search query here",
  selector: "#search-input"
});
```

### File Downloads

#### `Download.*`
Control file downloads with content-type filtering.

```javascript
const client = await page.target().createCDPSession();
await client.send("Download.setDownloadBehavior", {
  behavior: "allow",
  downloadPath: "/tmp/downloads"
});
// Retrieve file as base64
const { data } = await client.send("Download.getDownloadedFile", {
  guid: "download-guid-here"
});
```

### Security / Client Certificates

#### `Browser.addCertificate`
Install a client SSL/TLS certificate for authenticated domain access. Certificate is automatically removed when the session ends.

```javascript
await client.send("Browser.addCertificate", {
  host: "example.com",
  certificate: "base64-encoded-cert",
  privateKey: "base64-encoded-key"
});
```

### Debugging

#### `Page.inspect`
Get a Chrome DevTools debugger URL to connect and inspect the live session.

```javascript
const { url } = await client.send("Page.inspect");
console.log(`Devcapabilities: File operations, code editing, terminal access, search${url}`);
// Open in Chrome or use programmatically
```

---

## Geolocation Targeting

### Country-Level (via credentials)
Append `-country-XX` to your username (2-letter ISO code):

```javascript
const AUTH = "brd-customer-ID-zone-NAME-country-us:PASSWORD";
const browser = await chromium.connectOverCDP(`wss://${AUTH}@brd.superproxy.io:9222`);
```

**EU targeting** — automatically routes through 29+ European countries:
```javascript
const AUTH = "brd-customer-ID-zone-NAME-country-eu:PASSWORD";
```

### Precise Location (via CDP)
Use `Proxy.setLocation` for city/neighborhood-level targeting. **Call before `page.goto()`**:

```javascript
const client = await page.target().createCDPSession();
await client.send("Proxy.setLocation", {
  latitude: 51.5074,   // London
  longitude: -0.1278,
  distance: 5,          // 5km radius
  strict: false         // expand search if no peers found nearby
});
await page.goto("https://example.com");
```

---

## Bandwidth Optimization

Browser sessions are billed by traffic. These techniques reduce costs:

### Block Unnecessary Resources

```javascript
// Puppeteer
await page.setRequestInterception(true);
page.on("request", (req) => {
  const blocked = ["image", "stylesheet", "font", "media"];
  if (blocked.includes(req.resourceType())) {
    req.abort();
  } else {
    req.continue();
  }
});
```

```python
# Playwright
async def block_resources(route, request):
    blocked = ["image", "stylesheet", "font", "media"]
    if request.resource_type in blocked:
        await route.abort()
    else:
        await route.continue_()

await page.route("**/*", block_resources)
```

### Block Specific URLs (CDP)

```javascript
const client = await page.target().createCDPSession();
await client.send("Network.setBlockedURLs", {
  urls: [
    "*google-analytics*",
    "*facebook.com/tr*",
    "*.doubleclick.net*"
  ]
});
```

### Enable Ad Blocker

```javascript
await client.send("Unblocker.enableAdBlock");
```

### Browser Caching
Browser API automatically caches resources across multiple navigations to the same domain within a session. No extra configuration needed.

---

## CAPTCHA Handling

### Automatic (Default)
CAPTCHA solving is enabled by default. No code needed — the browser solves CAPTCHAs transparently.

### Detect and Wait for CAPTCHA

```javascript
// Navigate and wait for CAPTCHA to be solved automatically
const client = await page.target().createCDPSession();
await page.goto("https://example.com");

// If CAPTCHA appears, explicitly wait for it
const captchaResult = await client.send("Captcha.solve", { timeout: 60000 });
if (captchaResult.status === "solved") {
  console.log("CAPTCHA solved");
}
```

### Disable Auto-Solve

```javascript
const client = await page.target().createCDPSession();
await client.send("Captcha.setAutoSolve", { autoSolve: false });
// Now you control when/if to solve CAPTCHAs
```

---

## Debugging

### Via Control Panel
Navigate to your Browser API zone → Overview tab → Click "Chrome Dev Tools Debugger".

### Via CDP Command

```javascript
const { url } = await client.send("Page.inspect");
// Opens Chrome DevTools for the live session
```

### Programmatic Inspection (Playwright)
Playwright provides a `slowMo` and debug URL option. Use `Page.inspect` to get the debug URL and connect with Chrome.

---

## Premium Domains

Certain websites classified as "premium" require more Browser API resources:

- Enable during zone creation: toggle "Premium Domains" ON
- Only traffic to premium-classified domains incurs the higher rate
- The list of premium domains updates regularly based on Bright Data's algorithms
- Check current premium domain list in your zone settings

---

## Error Codes

| Code | Issue | Solution |
|------|-------|----------|
| `407` | Wrong port | Playwright/Puppeteer = port `9222`, Selenium = port `9515` |
| `403` | Authentication failure | Verify username format and password; confirm you're using a Browser API zone (not proxy zone) |
| `503` | Service unavailable (scaling) | Reconnect after 1 minute |

---

## Billing Model

| Factor | Detail |
|--------|--------|
| Pricing unit | Traffic-based only (bandwidth consumed) |
| Time/instance fees | None |
| Session idle timeout | 5 minutes — sessions close automatically |
| Session max duration | 30 minutes |
| Resource blocking | Reduces bandwidth = reduces cost |

---

## Best Practices

### 1. Always set navigation timeout to 2 minutes
Complex anti-bot procedures take time. Default timeouts (30s) are too short.

```javascript
page.setDefaultNavigationTimeout(120000); // 2 minutes
```

### 2. Call `Proxy.setLocation` before navigation
Location selection must happen before `page.goto()` to ensure the correct proxy is selected.

```javascript
// CORRECT: Set location first
await client.send("Proxy.setLocation", { latitude: 37.7749, longitude: -122.4194, distance: 10 });
await page.goto("https://example.com");

// WRONG: Location set after navigation has no effect on proxy selection
await page.goto("https://example.com");
await client.send("Proxy.setLocation", { ... }); // too late
```

### 3. Block unnecessary resources to cut bandwidth costs
Images, stylesheets, and fonts are often not needed for data extraction. Block them.

```javascript
await page.route("**/*.{png,jpg,jpeg,gif,svg,css,woff,woff2}", route => route.abort());
```

### 4. Use `waitUntil: "domcontentloaded"` over `networkidle`
`networkidle` waits for all network activity to stop, which is slow and often unnecessary. `domcontentloaded` is faster and sufficient for most pages.

```javascript
await page.goto(url, { waitUntil: "domcontentloaded" });
```

### 5. Start a fresh session for each new target URL
Since only one initial navigation per session is allowed, create a new browser connection for each independent scraping task.

```javascript
async function scrapeUrl(url) {
  const browser = await chromium.connectOverCDP(`wss://${AUTH}@brd.superproxy.io:9222`);
  try {
    const page = await browser.newPage();
    page.setDefaultNavigationTimeout(120000);
    await page.goto(url, { waitUntil: "domcontentloaded" });
    return await page.content();
  } finally {
    await browser.close();
  }
}
```

### 6. Use `Proxy.useSession` for IP continuity across sessions
When you need the same IP for multiple requests (e.g., login → scrape → logout flow across sessions):

```javascript
await client.send("Proxy.useSession", { sessionId: "user-session-abc123" });
```

### 7. Use `Unblocker.enableAdBlock` for content-heavy sites
Ad networks add significant bandwidth. Blocking ads reduces costs on news sites, blogs, and similar pages.

### 8. Use `Emulation.setDevice` for mobile-specific pages
Some sites serve different content to mobile users. Set the device profile instead of manually setting user agent.

```javascript
await client.send("Emulation.setDevice", { deviceName: "iPhone 14" });
await page.goto("https://example.com/mobile");
```

### 9. Monitor sessions via `Page.inspect` during development
During development, use `Page.inspect` to get a live DevTools URL to debug what the browser actually sees.

### 10. Use `Input.type` for bulk form filling
`Input.type` is faster than simulating individual keystrokes — important when filling many fields at scale.

---

## Anti-Patterns

**DO NOT use proxy networks with Playwright/Puppeteer/Selenium.**
Browser automation libraries should connect to Browser API, not route through a proxy. The Browser API endpoint (`wss://...`) replaces proxy configuration entirely.

**DO NOT reuse sessions for different target URLs.**
One session = one `page.goto()`. After that, interact only with the loaded page. For new URLs, create new sessions.

**DO NOT set navigation timeout below 60 seconds.**
Bright Data's unlocking procedures can take time. Anything below 60 seconds risks premature timeouts on difficult sites. Recommended: 120 seconds.

**DO NOT enable custom headers unless required.**
Custom headers/cookies shift billing from success-only to 100% of all requests.

**DO NOT rely on `networkidle` for SPA pages.**
Single-page applications may never reach true `networkidle` state. Use specific element waiters instead:
```javascript
await page.waitForSelector(".product-data", { timeout: 30000 });
```




---

## 🚀 Usage

**Reference this template:** `@skill-bright-data-best-practices.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
