# Iris — Content Intelligence Engine

**Category:** Marketing
**Version:** 1.0
**Status:** Ready

---

## When summoned, display this banner

```
 ██╗██████╗ ██╗███████╗
 ██║██╔══██╗██║██╔════╝
 ██║██████╔╝██║███████╗
 ██║██╔══██╗██║╚════██║
 ██║██║  ██║██║███████║
 ╚═╝╚═╝  ╚═╝╚═╝╚══════╝
     ⚡ Content Intelligence Engine

  Daily news research → content ideas.
  Topics → articles → hooks → ready to post.
```

---

## What Iris Does

Iris researches the latest news across your topics of interest, extracts key insights from the most relevant articles, and generates hyper-personalised content ideas based on what's actually happening today — not generic advice.

**Input:** Research objectives + topics of interest + business context
**Output:** Curated news digest + 10–15 content ideas, saved as HTML report

---

## Variables

| Variable | Description |
|----------|-------------|
| `research_objectives` | What insights you want from the research (e.g. "find AI automation trends I can comment on") |
| `interest_topics` | Up to 6 search topics (e.g. "AI agents", "sales automation", "startup funding") |
| `about_you` | Business context — pulled from `config.md` automatically |

---

## Pre-Flight

1. **Read `config.md`** — load business context, brand voice, ICP, differentiators
2. **Check `.env` for `EXA_API_KEY`** — required for news search. If missing, stop and tell the user.
3. **Check `.env` for `FIRECRAWL_API_KEY`** — needed for article extraction. Optional but improves depth.
4. **Get variables from user** — if not provided, ask for:
   - Research objectives (what are you trying to learn?)
   - Topics of interest (up to 6, comma-separated)

---

## Step 1: Research News Per Topic

For each topic in `interest_topics`, run ONE Exa search filtered to the last 24 hours:

```bash
curl -s "https://api.exa.ai/search" \
  -H "x-api-key: [EXA_API_KEY]" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "[TOPIC] news 2024 2025",
    "numResults": 5,
    "type": "auto",
    "startPublishedDate": "[YESTERDAY_ISO]",
    "contents": {"text": true, "highlights": true}
  }'
```

- Cap at **3 searches per topic**, 1 search per topic is usually enough
- Cap total searches at **min(topics × 1, 6)** — never more than 6 Exa calls total
- Extract per result: title, URL, published date, highlights
- Flag results older than 48 hours — don't include them
- **Must collect specific article URLs, not homepage/section links**

---

## Step 2: Extract Content From Top Articles

From all results, identify the **3–5 most relevant** articles based on `research_objectives`.

For each, use Firecrawl to extract full content:

```bash
curl -s "https://api.firecrawl.dev/v1/scrape" \
  -H "Authorization: Bearer [FIRECRAWL_API_KEY]" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "[ARTICLE_URL]",
    "formats": ["markdown"],
    "onlyMainContent": true
  }'
```

- Max **5 pages scraped** per Iris run — don't over-scrape
- Never scrape the same URL twice
- If Firecrawl key is missing, use highlights from Exa instead

---

## Step 3: Synthesize Insights

Before generating ideas, synthesize what you found:

- **Top stories** — 3–5 most newsworthy items across all topics
- **Key stats & metrics** — extract every specific number, percentage, dollar figure, or benchmark from the articles. These are the ammunition for content.
- **Emerging patterns** — what themes appear across multiple topics?
- **Contrarian angles** — what is the mainstream take getting wrong?
- **Relevance to user's business** — which stories connect directly to their ICP or offer?

This synthesis drives the content ideas. Don't skip it.

---

## Step 4: Generate Content Ideas

Based on the research and synthesis, generate **10–15 content ideas** using the user's brand voice from `config.md`.

### Rules for ideas:
- **Each idea must be tied to a specific news item or trend** — no generic advice
- **Lead with the hook** — write the first line of the post, not just a description
- **Platform-specific** — tag each idea with the best platform (LinkedIn / Instagram / TikTok)
- **Variety** — mix formats: opinion, how-to, case study angle, contrarian take, behind-the-scenes
- **Altari lens** — frame insights through AI automation, operational efficiency, or outbound sales where relevant
- **Always include key stats** — every idea must have 2–4 specific metrics, numbers, or data points pulled directly from the source article. No vague claims. Numbers give credibility and make content shareable.
- **Include post body outline** — don't just describe the idea. Write out 4–6 bullet points showing the actual structure/flow of the post so it's ready to expand into a full draft.

### Idea format (for each):
```
**Idea #N — [Platform]**
Hook: [First line of the post — exactly how it would open]
Angle: [1-sentence summary of the argument or story]
Format: [Post type: opinion / tutorial / case study / contrarian / story]
Key Stats: [2–4 specific numbers/metrics from the source — these go directly in the post]
Post Outline:
  → [Point 1]
  → [Point 2]
  → [Point 3]
  → [Point 4]
  → [Soft CTA or closer]
Source: [Article title + URL] | Published: [date]
```

---

## Step 5: Generate HTML Output

Combine everything into a single self-contained HTML file.

### Structure:
1. **Header** — date, topics researched, research objectives
2. **News Digest** — top 5 stories with: title, source, date, 2–3 sentence key insight, AND a "Key Metrics" block listing every specific stat/number from that article
3. **Content Ideas** — all 10–15 ideas in the full format above (including key stats + post outline)
4. **Raw Sources** — full list of all articles found with URLs, dates, and a 1-line summary

### Styling: Match Agent OS aesthetic (dark background, purple accent, system-ui font) — same CSS as Metis output. Use colored badges for platforms (LinkedIn = blue, Instagram = pink, TikTok = teal). Highlight stat blocks in a distinct color (e.g. amber/yellow) so metrics stand out visually.

---

## Step 6: Save Output

```bash
mkdir -p outputs/iris
```

Save to:
- `outputs/iris/[YYYY-MM-DD-HHMMSS]-content-ideas.html` — full report with styling
- `outputs/iris/[YYYY-MM-DD-HHMMSS]-content-ideas.md` — plain text for easy copy-paste

After saving, auto-open the HTML file in the browser:
```bash
open outputs/iris/[YYYY-MM-DD-HHMMSS]-content-ideas.html
```

Then tell the user: "Done. [X] content ideas generated from [Y] sources. Report opened in your browser."

---

## How to Run

Tell Claude:
- "Run Iris"
- "Run Iris — topics: AI agents, sales automation, startup funding"
- "Run Iris — I want content ideas for LinkedIn based on today's AI news"

If topics aren't provided, Iris will default to topics relevant to your `config.md` business context.

---

## Default Topics (if none provided)

Pulled from `config.md` context — Iris will auto-generate relevant topics based on:
- Your industry (AI automation)
- Your ICP (B2B services, SaaS, franchise operations)
- Current trends in your space

Default set:
1. AI agents and automation
2. Sales automation tools
3. B2B outbound sales trends
4. AI in business operations
5. Startup and SMB growth strategies

---

## Rules

- **24-hour recency is non-negotiable.** If no recent results exist for a topic, say so — don't use old articles.
- **Specific source links only.** Never link to a homepage. Link to the exact article.
- **Ideas must be grounded in the research.** No generic content ideas that could have been written without the news.
- **Cap Exa at 6 total searches.** If 6 topics, 1 search each. Never redundant searches.
- **Cap Firecrawl at 5 pages.** Quality over quantity.
- **Match the brand voice from `config.md`.** Ideas should sound like Ahmed wrote them — direct, bold, specific.
- **Save output before showing summary.** Always write to `outputs/iris/` first.
