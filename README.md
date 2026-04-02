# Agent OS

AI agent framework for Claude Code. Pre-built agents that handle research, lead generation, outreach, content creation, and more.

Built by Ahmed Alassafi (@alassafi.ai) for the Agent OS community.

---

## Quick Start

### 1. Open in Claude Code
Unzip this folder and open it in Claude Code (CLI or VS Code extension).

### 2. Configure Your Business
Run `/setup` in Claude Code — it walks you through filling in your company details, ICP, qualification criteria, and brand voice. Or manually edit `config.md`. See `config.example.md` for a filled-out example.

### 3. Add API Keys
Copy the example env file and add your keys:
```
cp .env.example .env
```
Open `.env` and fill in the keys for the agents you want to use. Each agent's file tells you exactly which keys it needs. You only need keys for agents you plan to use.

### 4. Run an Agent
Tell Claude what you need:
- "Find me SaaS companies in Sydney with 20-50 employees"
- "Research this prospect: [Name] at [Company]"
- "Write me a cold email sequence for [prospect]"
- "Scrape commenters from this LinkedIn post: [URL]"
- "Run Felix" (if you know the agent name)

Claude will find the right agent, run it, and save the output to `outputs/`.

---

## What's Inside

```
agents/
├── research/    — Prospect, company, and competitor research
├── lead-gen/    — Lead finding and social media scraping
├── outreach/    — Cold email and LinkedIn outreach
├── marketing/   — Content, social media, ads, brand strategy
└── utils/       — General research and analysis
```

Run `/list-agents` in Claude Code to see the full catalog with a decision tree.

---

## The GTM Workflow

```
1. FIND        Felix → Build targeted lead lists
2. RESEARCH    Atlas + Pluto → Deep dive on companies and people
3. QUALIFY     Pluto/Atlas scoring → Focus on highest-fit prospects
4. OUTREACH    Emilio (email) or Leonardo (LinkedIn) → Personalized sequences
5. CONTENT     Cicero + Harry → Thought leadership and social proof
6. NURTURE     Iris → Email campaigns for longer sales cycles
```

---

## Agents

### Built and Ready

| Agent | Category | What It Does | Required Keys |
|-------|----------|-------------|---------------|
| Pluto | Research | Prospect (person) research & intelligence reports | APIFY, EXA, FIRECRAWL |
| Atlas | Research | Company research & fit scoring | EXA, FIRECRAWL, APIFY |
| Themis | Research | Competitor research & analysis | EXA, FIRECRAWL |
| Felix | Lead Gen | Lead finder — companies + people (tiered methods) | APOLLO, APIFY, EXA, HUNTER |
| Artemis | Lead Gen | LinkedIn post comment hunter + ICP scoring + DMs | APIFY |
| Emilio | Outreach | Cold email sequences (optionally pushes to Instantly) | INSTANTLY |
| Leonardo | Outreach | LinkedIn outreach — connection requests + DMs | HEYREACH |

### Coming Soon

| Agent | Category | What It Does |
|-------|----------|-------------|
| Cicero | Marketing | Long-form content & thought leadership |
| Harry | Marketing | Social media content |
| Iris | Marketing | Email marketing campaigns |
| Picasso | Marketing | Visual content & design briefs |
| Tron | Marketing | Ad copy & paid media |
| Cosmo (Brand) | Marketing | Brand strategy & messaging |
| Cosmo (Image) | Marketing | Brand image & visual identity |
| Metis | Utils | General research & analysis |

---

## API Keys

You only need keys for agents you plan to use. The research stack (Exa + Firecrawl + Apify) powers the most agents.

| Service | Used By | Free Tier? |
|---------|---------|-----------|
| Exa | Pluto, Atlas, Themis, Felix | 1K searches/mo free |
| Firecrawl | Pluto, Atlas, Themis | 500 credits one-time |
| Apify | Pluto, Atlas, Felix, Artemis | $5/mo free credits |
| Apollo | Felix | No free tier |
| Hunter | Felix | 50 searches/mo free |
| Instantly | Emilio | No free tier |
| HeyReach | Leonardo | No free tier |

---

## Need Help?

- Say "list agents" to see what's available
- Say "help me with [task]" and Claude will route you to the right agent
- Check each agent's .md file for detailed documentation
- Run `/setup` to reconfigure your business profile or API keys
