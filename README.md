# Gemini Scraper API — ScrapeLLM

[![ScrapeLLM Gemini Scraper](https://github.com/user-attachments/assets/2875ee09-132d-4eb5-94d5-de03fee03a5d)](https://scrapellm.com/scrapers/gemini)

[![ScrapeLLM](https://img.shields.io/badge/ScrapeLLM-Gemini%20Scraper-7c3aed?style=for-the-badge)](https://scrapellm.com/scrapers/gemini)
[![API Docs](https://img.shields.io/badge/API-Documentation-7c3aed?style=for-the-badge)](https://scrapellm.com/api)
[![500 Free Credits](https://img.shields.io/badge/Free-500%20Credits-16a34a?style=for-the-badge)](https://scrapellm.com/signup)

The [ScrapeLLM Gemini Scraper](https://scrapellm.com/scrapers/gemini) lets you send any prompt to Google Gemini and get back the **real user interface response** — including rich citation metadata with source snippets, highlights, and favicons — as clean, structured JSON. Unlike the direct Gemini API, ScrapeLLM captures exactly what a user sees in the Gemini web interface.

**3 credits per request · 500 free credits · No credit card required**

---

## How it works

Send a `GET` request to `https://api.scrapellm.com/scrapers/gemini` with your prompt. ScrapeLLM routes it through a pool of real browsers, captures the full Gemini UI response, and returns it as structured JSON — including every cited source with its snippet, highlighted excerpts, and site metadata.

### Request sample (Python)

```python
import requests

response = requests.get(
    "https://api.scrapellm.com/scrapers/gemini",
    headers={"X-API-Key": "your_api_key"},
    params={
        "prompt": "What brands do marketers recommend for email automation?",
        "country": "US",
    }
)

print(response.json())
```

### Request sample (JavaScript)

```javascript
const params = new URLSearchParams({
  prompt: "What brands do marketers recommend for email automation?",
  country: "US",
});

const response = await fetch(
  `https://api.scrapellm.com/scrapers/gemini?${params}`,
  { headers: { "X-API-Key": "your_api_key" } }
);

const data = await response.json();
console.log(data);
```

### Request sample (cURL)

```bash
curl "https://api.scrapellm.com/scrapers/gemini" \
  -H "X-API-Key: your_api_key" \
  -G \
  --data-urlencode "prompt=What brands do marketers recommend for email automation?" \
  --data-urlencode "country=US"
```

---

## Authentication

Pass your API key in one of two ways:

| Method | Example |
|--------|---------|
| `X-API-Key` header | `-H "X-API-Key: your_api_key"` |
| `?api_key=` query param | `?api_key=your_api_key` |

Get your API key at [scrapellm.com/signup](https://scrapellm.com/signup) — 500 free credits, no credit card required.

---

## Request parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `prompt` | string | ✅ | — | The prompt to send to Gemini. Max 4,000 characters. |
| `country` | string | — | `US` | ISO 3166-1 alpha-2 country code. Routes the request through infrastructure in that region for localised responses. JP and TW are not supported. |
| `bypass_cache` | boolean | — | `false` | Skip the response cache and always fetch a fresh result. |
| `timeout` | float | — | `300` | Max seconds to wait for Gemini to respond. Between 10 and 600. |

---

## Response schema

```json
{
  "scraper": "gemini",
  "status": "done",
  "job_id": "job_abc123",
  "prompt": "What brands do marketers recommend for email automation?",
  "country": "US",
  "result": "Marketers commonly recommend Mailchimp, HubSpot, and Klaviyo...",
  "result_markdown": "Marketers commonly recommend **Mailchimp**, **HubSpot**, and **Klaviyo**...",
  "citations": [
    {
      "title": "Best Email Marketing Platforms 2025",
      "url": "https://example.com/email-marketing",
      "snippet": "We tested 12 tools across deliverability, automation, and pricing...",
      "highlights": [
        "Klaviyo leads for e-commerce with advanced segmentation",
        "Mailchimp remains the most accessible entry point"
      ],
      "website_name": "Marketing Weekly",
      "favicon": "https://example.com/favicon.ico"
    }
  ],
  "llm_model": "gemini-2.0-flash",
  "credits_used": 3,
  "elapsed_ms": 6521.8,
  "cached": false,
  "markdown_url": "https://scrapellm.com/md/job_abc123.md"
}
```

### Response fields

| Field | Type | Description |
|-------|------|-------------|
| `scraper` | string | Always `"gemini"`. |
| `status` | string | `"done"` on success. |
| `job_id` | string | Unique ID for this request. |
| `prompt` | string | The prompt that was submitted. |
| `country` | string | The country the request was routed through. |
| `result` | string | Plain text response from Gemini. |
| `result_markdown` | string | Markdown-formatted response with inline citation markers. |
| `citations` | array | Sources cited by Gemini. See [Citation fields](#citation-fields). |
| `llm_model` | string | Gemini model variant used (e.g. `gemini-2.0-flash`). |
| `credits_used` | integer | Credits consumed by this request (always `3`). |
| `elapsed_ms` | float | Total request duration in milliseconds. |
| `cached` | boolean | `true` if this response was served from cache. |
| `markdown_url` | string | Hosted `.md` URL for this response (expires after 24 h). |

### Citation fields

Each object in `citations` contains:

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | Title of the cited page. |
| `url` | string | Absolute URL of the source. |
| `snippet` | string | Excerpt from the cited page. |
| `highlights` | array | Specific passages Gemini pulled from the source. |
| `website_name` | string | Human-readable site name (e.g. `"Wikipedia"`). |
| `favicon` | string \| null | Favicon URL for the cited domain. |

---

## Error codes

| Status | Meaning |
|--------|---------|
| `401` | Missing or invalid API key. |
| `429` | Credit limit reached, or scraper queue full — retry shortly. |
| `500` | Scraper returned an unexpected error — safe to retry. |
| `503` | Scraper pool warming up — retry in a few seconds. |
| `504` | Network timeout reaching the scraper. |

---

## Multi-region support

Pass any ISO 3166-1 alpha-2 country code to the `country` parameter to route your request through infrastructure in that region. Gemini responses vary by geography — essential for localised brand and SEO research. JP and TW are not supported.

```python
# Get Gemini's response as seen by users in Germany
response = requests.get(
    "https://api.scrapellm.com/scrapers/gemini",
    headers={"X-API-Key": "your_api_key"},
    params={"prompt": "Best CRM software?", "country": "DE"}
)
```

Supported country codes include `US`, `GB`, `DE`, `FR`, `AU`, `CA`, and many more.

---

## Practical use cases

### 1. Brand mention monitoring
Track how often and in what context Gemini mentions your brand when users ask industry questions. Run the same prompt repeatedly to calculate a reliable **visibility %** — the only meaningful AI brand metric.

```python
import requests, time

prompts = [
    "What CRM do sales teams recommend?",
    "Best CRM for small business?",
    "Top CRM tools in 2025?",
]

for prompt in prompts:
    r = requests.get(
        "https://api.scrapellm.com/scrapers/gemini",
        headers={"X-API-Key": "your_api_key"},
        params={"prompt": prompt, "country": "US"}
    )
    data = r.json()
    print(f"Prompt: {prompt}")
    print(f"Answer: {data['result'][:200]}")
    print(f"Citations: {[c['url'] for c in data['citations']]}\n")
    time.sleep(2)
```

### 2. AI SEO research
Gemini's `citations` field is richer than any other AI scraper — each source comes with a `snippet`, `highlights`, and `website_name`. Use this to understand exactly which pages and passages influence Gemini's answers in your niche.

```python
response = requests.get(
    "https://api.scrapellm.com/scrapers/gemini",
    headers={"X-API-Key": "your_api_key"},
    params={"prompt": "How do I improve email deliverability?", "country": "US"}
)
data = response.json()

for citation in data["citations"]:
    print(f"{citation['website_name']} — {citation['url']}")
    print(f"  Snippet: {citation['snippet'][:120]}")
    for highlight in citation["highlights"]:
        print(f"  ↳ {highlight}")
```

### 3. Competitor tracking
Monitor which competitors Gemini recommends for key buying-intent queries in your market, and drill into the specific source passages that drive those recommendations via `highlights`.

```python
buying_intent_prompts = [
    "What project management tool should I use?",
    "Best Asana alternative?",
    "Project management software comparison",
]

for prompt in buying_intent_prompts:
    r = requests.get(
        "https://api.scrapellm.com/scrapers/gemini",
        headers={"X-API-Key": "your_api_key"},
        params={"prompt": prompt, "country": "US"}
    )
    print(r.json()["result"][:300])
```

### 4. Content strategy
Use Gemini's cited sources and highlighted passages as precise signals for what content to create. Unlike other AI scrapers, Gemini's `highlights` tell you exactly which sentences from a page Google's AI finds most relevant — a goldmine for on-page optimisation.

---

## Why ScrapeLLM over the direct Gemini API?

| | Direct Gemini API | ScrapeLLM |
|--|--|--|
| Real UI responses | ❌ | ✅ |
| Rich citations with snippets | ❌ | ✅ |
| Source highlights | ❌ | ✅ |
| Multi-region | ❌ | ✅ |
| Predictable pricing | ❌ Token-based | ✅ Flat credits |
| Single API for all LLMs | ❌ | ✅ |

---

## All scrapers

ScrapeLLM supports all major AI platforms through a single consistent API:

| Scraper | Credits / req | Docs |
|---------|--------------|------|
| ChatGPT | 3 | [scrapellm.com/scrapers/chatgpt](https://scrapellm.com/scrapers/chatgpt) |
| Perplexity AI | 3 | [scrapellm.com/scrapers/perplexity](https://scrapellm.com/scrapers/perplexity) |
| Grok | 3 | [scrapellm.com/scrapers/grok](https://scrapellm.com/scrapers/grok) |
| Gemini | 3 | [scrapellm.com/scrapers/gemini](https://scrapellm.com/scrapers/gemini) |
| Microsoft Copilot | 3 | [scrapellm.com/scrapers/copilot](https://scrapellm.com/scrapers/copilot) |
| Google Search | 1 | [scrapellm.com/scrapers/google-search](https://scrapellm.com/scrapers/google-search) |

---

## FAQ

### Is scraping Gemini legal?
ScrapeLLM accesses only the publicly available, unauthenticated Gemini interface — the same view any anonymous visitor sees. We do not use login credentials, stored sessions, or cookies for any target platform. Users are responsible for ensuring their use of the data complies with applicable laws and Google's Terms of Service.

### What is the prompt size limit?
The maximum prompt length is 4,000 characters. For longer content, split into multiple requests.

### How long do requests take?
Most requests complete in 5–20 seconds.  Set `timeout` up to 600 seconds for complex prompts.

### What makes Gemini's citations different?
Gemini returns richer citation metadata than other AI platforms — each source includes a `snippet`, a list of `highlights` (the exact passages Gemini pulled), and `website_name`. This level of detail is unique to Gemini and makes it especially valuable for AI SEO research.

### Can I request from a specific country?
Yes. Pass any ISO 3166-1 alpha-2 code via the `country` parameter. Responses will reflect what a user in that region sees. JP and TW are not supported.

### Do credits roll over?
No. Credits reset at the start of each billing cycle.

---

## Get started

1. [Sign up](https://scrapellm.com/signup) — 500 free credits, no credit card required
2. Copy your API key from the dashboard
3. Make your first request:

```bash
curl "https://api.scrapellm.com/scrapers/gemini" \
  -H "X-API-Key: your_api_key" \
  -G \
  --data-urlencode "prompt=What brands do marketers recommend?" \
  --data-urlencode "country=US"
```

## Contact

Questions or need help? Reach out at **hello@scrapellm.com** or visit [scrapellm.com](https://scrapellm.com).
