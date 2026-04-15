# Agent OS — Agent Catalog

> **Most users should start with `/campaign`** — it chains lead finding, research, business analysis, and outreach into a single pipeline. Use individual agents below when you need just one piece.

---

## The Full Pipeline: `/campaign`

Finds leads → researches each company's operations → writes outreach that pitches AI specific to their business → exports HTML dashboard + CSVs for Instantly/HeyReach. This is the flagship feature.

---

## Individual Agents

### What do you need?

### "I need to research a prospect, company, or competitor"

| Agent | Best For |
|-------|----------|
| **Pluto** | Deep research on a specific person — LinkedIn, web, signals, personalization hooks |
| **Atlas** | Deep research on a specific company — financials, growth signals, fit scoring |
| **Themis** | Competitive research — positioning, strengths/weaknesses, market gaps |

**Typical flow:** Atlas (company research) → Pluto (key decision-maker research) → Emilio/Leonardo (outreach)

---

### "I need leads, prospects, or contact lists"

| Agent | Best For |
|-------|----------|
| **Felix** | Building lead lists — companies + people from Apollo, Google Maps, LinkedIn, Exa (tiered by API keys available) |
| **Artemis** | Scraping LinkedIn post commenters, enriching profiles, ICP scoring, writing DMs |

**Typical flow:** Felix (build list) → Pluto (research top prospects) → Emilio (write outreach)

---

### "I need to reach out, sell, or close deals"

| Agent | Best For |
|-------|----------|
| **Emilio** | Writing cold email sequences that get replies |
| **Leonardo** | LinkedIn outreach — connection requests, DMs, follow-up sequences |
| **Eddie** | Post-discovery-call prototype builder — turns call notes into a visual demo to send before the follow-up |

**Typical flow:** Pluto (research) → Emilio or Leonardo (outreach)  
**Post-call flow:** Eddie → build prototype → send before follow-up → close on second call

---

### "I need content, copy, or marketing material"

| Agent | Best For |
|-------|----------|
| **Cicero** | Multi-platform content engine — one idea → research → LinkedIn, X Thread, Newsletter, YouTube (long + Shorts), Reels, TikTok, AI image prompt |
| **Harry** | Social media — LinkedIn posts, Instagram captions, TikTok scripts |
| **Iris** | Email marketing — newsletters, drip campaigns, nurture sequences |
| **Picasso** | Instagram reel analyzer — scrapes competitor + your reels, transcribes hooks, finds winning patterns, generates content ideas |
| **Tron** | Ad copy — paid social, Google Ads, landing page copy |
| **Cosmo (Brand)** | Brand image generator — reads reference images from `assets/brand-references/`, extracts visual language, generates brand-consistent images via Flux Pro |
| **Cosmo (Character)** | Character image generator — reads reference photos from `assets/character-references/`, uploads best ref to fal.ai, generates identity-consistent personal brand images via `consistent-character` model |

**Typical flow:** Cosmo Brand (strategy) → Cicero or Harry (content) → Tron (ads) → Iris (email nurture)

---

### "I need general research or analysis"

| Agent | Best For |
|-------|----------|
| **Metis** | General research, market analysis, data synthesis, anything not covered above |

---

## The GTM Workflow

Here's how the agents work together for a full go-to-market motion:

```
1. FIND        Felix → Build targeted lead lists
2. RESEARCH    Atlas + Pluto → Deep dive on best-fit companies and people
3. QUALIFY     Pluto's scoring → Focus on highest-fit prospects
4. OUTREACH    Emilio (email) or Leonardo (LinkedIn) → Personalized sequences
5. CONTENT     Cicero + Harry → Thought leadership and social proof
7. NURTURE     Iris → Email campaigns for longer sales cycles
```

---

## Full Agent Reference

| # | Agent | Category | What It Does | Required Keys | Status |
|---|-------|----------|-------------|---------------|--------|
| 1 | Felix | Lead Gen | Find companies + people with verified emails | APOLLO, EXA | Ready |
| 2 | Pluto | Research | Deep prospect research & personalization hooks | EXA, FIRECRAWL | Ready |
| 3 | Atlas | Research | Company research & qualification scoring | EXA, FIRECRAWL | Ready |
| 4 | Themis | Research | Competitor teardown — pricing, positioning, weaknesses | EXA, FIRECRAWL | Ready |
| 5 | Emilio | Outreach | 3-step cold email sequences (reads outreach.md) | — (INSTANTLY optional) | Ready |
| 6 | Leonardo | Outreach | LinkedIn connection + DM sequences (reads outreach.md) | — (HEYREACH optional) | Ready |
| 7 | Eddie | Sales | Post-discovery prototype builder — call notes → visual demo → send message | — (FIREFLIES optional) | Ready |
| 8 | Harry | Marketing | Viral hook generator — 15-20 hooks per platform | EXA (optional) | Ready |
| 9 | Artemis | Lead Gen | LinkedIn post comment hunter + ICP scorer + DMs | APIFY | Ready |
| 10 | Picasso | Marketing | Instagram reel analyzer + spoken hook extractor | APIFY, DEEPGRAM | Ready |
| 11 | Cicero | Marketing | One idea → research → 7 platform-optimized formats (LinkedIn, X, Newsletter, YouTube, Reels, TikTok, Shorts) | EXA (optional) | Ready |
| 12 | Iris | Marketing | Daily news research → content ideas (topics → articles → hooks → ready to post) | EXA, FIRECRAWL (optional) | Ready |
| 13 | Tron | Marketing | TikTok competitor analyzer — scrapes profiles, transcribes hooks, finds winning patterns, generates hook ideas | APIFY, DEEPGRAM | Ready |
| 14 | Cosmo (Brand) | Marketing | Brand strategy & messaging | — | Coming Soon |
| 15 | Cosmo (Image) | Marketing | Brand image & visual identity | — | Coming Soon |
| 16 | Metis | Utils | Research & analysis — any topic → structured report + SVG diagram as HTML | EXA (optional) | Ready |

> **Ready** = built, tested, and guardrailed. **Coming Soon** = spec written, not yet complete.
> Agents with "—" in Required Keys use Claude's built-in capabilities — no external APIs needed.
