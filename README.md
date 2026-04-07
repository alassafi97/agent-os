# Agent OS

<!-- TODO: Add demo GIF or screenshot of /campaign dashboard output here -->

A team of AI agents that find your ideal customers, research what their business actually does, and write outreach that pitches AI automation specific to their operations — not generic templates.

Built for Claude Code. Run `/setup`, then `/campaign`, and get an HTML dashboard with personalized email + LinkedIn sequences ready to import into Instantly or HeyReach.

---

## What You Get in 3 Steps

### 1. Run `/setup` (2 minutes)
Answer a few questions about your business. Agent OS auto-researches your website and proposes your business profile, ICP, outreach voice, and social proof. You tweak, it saves.

### 2. Run `/campaign` (tell it who to target)
Agent OS finds companies matching your ICP, discovers decision-makers with verified emails, researches each company's operations, and figures out exactly what AI could automate in their business.

### 3. Get Your Campaign Dashboard
Open `campaign-table.html` in your browser — a dark-themed spreadsheet with every prospect, their business-specific email sequences, LinkedIn outreach, and CSV download buttons for Instantly/HeyReach.

**Every email pitches AI for their specific business.** Not "we help companies automate." But "your recruiters are spending 3 hours per candidate on intake paperwork — an AI agent handles that in 90 seconds."

---

## What Makes This Different

Most outreach tools generate generic templates with `{{first_name}}` and `{{company}}` mail merge.

Agent OS researches what each company actually does — their operations, workflows, bottlenecks — then writes outreach that describes what AI would automate in THEIR business specifically.

A staffing firm gets "candidate intake processing, compliance tracking, client reporting."
A B2B ecommerce firm gets "product data enrichment, catalog migration, implementation scoping."
A media company gets "editorial workflow routing, ad ops reporting, content repurposing."

Same framework, different pitch for every prospect.

---

## The Pipeline

```
/campaign

  1. FIND        → Apollo + Exa discover companies and people with verified emails
  2. RESEARCH    → Exa + Firecrawl analyze each company's actual operations
  3. ANALYZE     → AI identifies what could be automated in their specific business
  4. WRITE       → Business-specific email + LinkedIn sequences (quality-gated)
  5. DELIVER     → HTML dashboard + Instantly CSV + HeyReach CSV
```

---

## Agents

### Outbound Pipeline (used by `/campaign`)

| Agent | What It Does | Required Keys |
|-------|-------------|---------------|
| Felix | Find companies + people with verified emails | APOLLO, EXA |
| Pluto | Deep prospect (person) research | EXA, FIRECRAWL |
| Atlas | Deep company research + qualification scoring | EXA, FIRECRAWL |
| Emilio | 3-step cold email sequences | — (INSTANTLY optional) |
| Leonardo | LinkedIn connection + DM sequences | — (HEYREACH optional) |
| Eddie | Post-discovery-call prototype builder — turns call notes into a visual demo | — (FIREFLIES optional) |

### Research & Analysis

| Agent | What It Does | Required Keys |
|-------|-------------|---------------|
| Themis | Competitor teardown — pricing, positioning, weaknesses | EXA, FIRECRAWL |
| Metis | Research & analysis on any topic → structured HTML report + SVG diagram | EXA (optional) |

### Content & Marketing

| Agent | What It Does | Required Keys |
|-------|-------------|---------------|
| Cicero | Multi-platform content engine — one idea → LinkedIn, X Thread, Newsletter, YouTube, Reels, TikTok | — |
| Harry | Viral hook generator — 15–20 platform-specific hooks from your business context | EXA (optional) |
| Iris | Content intelligence — daily news research → 10–15 content ideas tied to real articles | EXA, FIRECRAWL (optional) |
| Picasso | Instagram reel analyzer — scrapes reels, extracts spoken hooks, finds winning patterns | APIFY, DEEPGRAM |
| Tron | TikTok competitor analyzer — scrapes profiles, transcribes hooks, finds winning patterns, generates 10 hook ideas | APIFY, DEEPGRAM |
| Artemis | LinkedIn post comment hunter — scrapes commenters, scores ICP fit, writes DMs | APIFY |

---

## What You Need

**Minimum to run `/campaign`:**
- Exa — free (1K searches/month) → [dashboard.exa.ai](https://dashboard.exa.ai/api-keys)
- Apollo — from $49/mo (verified emails + LinkedIn URLs) → [app.apollo.io](https://app.apollo.io/#/settings/integrations/api)

**Recommended addition:**
- Firecrawl — free (500 pages) → [firecrawl.dev](https://firecrawl.dev/app/api-keys) — enables deep website research per prospect

**Optional (for sending):**
- Instantly — from $30/mo → auto-push email campaigns
- HeyReach — from $79/mo → auto-push LinkedIn campaigns

---

## File Structure

```
config.md           ← your business profile (filled by /setup)
outreach.md         ← your outreach voice, angles, social proof (filled by /setup)
outreach.example.md ← template — copy to outreach.md if starting fresh
.env                ← your API keys (never shared)
agents/             ← all agent specs
outputs/            ← all outputs, organized by agent/campaign
outputs/api-tests/  ← verified API behavior logs
```

---

## Getting Started

1. Clone or download this repo
   ```bash
   git clone https://github.com/alassafi97/agent-os.git
   ```
   > No git? Click the green **Code** button → **Download ZIP** → unzip → open the folder in Claude Code.

2. Open the folder in Claude Code

3. Type `/setup`

4. Type `/campaign`

That's it.
