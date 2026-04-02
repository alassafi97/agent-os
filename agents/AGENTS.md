# Agent OS — Agent Catalog

> Don't know which agent to use? Start here. Describe what you need and follow the tree.

---

## What do you need?

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

**Typical flow:** Pluto (research) → Emilio or Leonardo (outreach)

---

### "I need content, copy, or marketing material"

| Agent | Best For |
|-------|----------|
| **Cicero** | Long-form content — blog posts, articles, thought leadership |
| **Harry** | Social media — LinkedIn posts, Instagram captions, TikTok scripts |
| **Iris** | Email marketing — newsletters, drip campaigns, nurture sequences |
| **Picasso** | Instagram reel analyzer — scrapes competitor + your reels, transcribes hooks, finds winning patterns, generates content ideas |
| **Tron** | Ad copy — paid social, Google Ads, landing page copy |
| **Cosmo (Brand)** | Brand strategy — positioning, messaging frameworks, voice guidelines |
| **Cosmo (Image)** | Visual identity — brand imagery direction, style guides |

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

| # | Agent | Category | What It Does | Required Keys |
|---|-------|----------|-------------|---------------|
| 1 | Pluto | Research | Prospect (person) research & intelligence | APIFY, EXA, FIRECRAWL |
| 2 | Atlas | Research | Company research & fit scoring | EXA, FIRECRAWL, APIFY |
| 3 | Themis | Research | Competitor research & analysis | EXA, FIRECRAWL |
| 4 | Felix | Lead Gen | Lead finder — companies + people (tiered methods) | APOLLO, APIFY, EXA, HUNTER |
| 5 | Artemis | Lead Gen | LinkedIn post comment hunter + ICP scorer | APIFY |
| 6 | Emilio | Outreach | Cold email sequences | INSTANTLY |
| 7 | Leonardo | Outreach | LinkedIn DM sequences | HEYREACH |
| 9 | Cicero | Marketing | Long-form content & thought leadership | — |
| 10 | Harry | Marketing | Social media content | — |
| 11 | Iris | Marketing | Email marketing campaigns | — |
| 12 | Picasso | Marketing | Instagram reel analyzer + hook extractor | APIFY, DEEPGRAM |
| 13 | Tron | Marketing | Ad copy & paid media | — |
| 14 | Cosmo (Brand) | Marketing | Brand strategy & messaging | — |
| 15 | Cosmo (Image) | Marketing | Brand image & visual identity | — |
| 16 | Metis | Utils | General research & analysis | — |

> Agents marked with "—" use Claude's built-in capabilities and don't require external API keys.
> Required keys and descriptions will be updated as each agent is built.
