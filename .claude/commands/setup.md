---
name: setup
description: First-time Agent OS configuration wizard — walks through config.md and .env setup
---

You are running the Agent OS setup wizard. Walk the user through configuring their workspace step by step.

## Process

### Step 1: Welcome
Display this banner first:
```
  █████╗  ██████╗ ███████╗███╗   ██╗████████╗     ██████╗ ███████╗
 ██╔══██╗██╔════╝ ██╔════╝████╗  ██║╚══██╔══╝    ██╔═══██╗██╔════╝
 ███████║██║  ███╗█████╗  ██╔██╗ ██║   ██║       ██║   ██║███████╗
 ██╔══██║██║   ██║██╔══╝  ██║╚██╗██║   ██║       ██║   ██║╚════██║
 ██║  ██║╚██████╔╝███████╗██║ ╚████║   ██║       ╚██████╔╝███████║
 ╚═╝  ╚═╝ ╚═════╝ ╚══════╝╚═╝  ╚═══╝   ╚═╝        ╚═════╝ ╚══════╝
                    ⚡ Setup Wizard
```

Then check if `config.md` and `.env` exist.

**If no `config.md` exists**, create it from the template first:
```bash
cp config.example.md config.md
```

**If no `.env` exists**, create it from the template:
```bash
cp .env.example .env
```

**If EXA_API_KEY is empty or missing**, ask the user BEFORE anything else:

"Welcome to Agent OS! Before we start — **do you have an Exa API key?**

Exa is a free AI search engine I use to research your company, competitors, and industry. With it, I can auto-build most of your profile instead of asking you a bunch of questions. Without it, I'll have to work from your website alone.

**It's free** — takes 30 seconds: https://dashboard.exa.ai/api-keys

Got one? Paste it here and I'll add it to your `.env`. Or say 'skip' to continue without it."

If the user provides a key, write it to `.env` (the `EXA_API_KEY=` line). Then continue.

**If EXA_API_KEY already exists**, skip this and move on.

### Step 1B: Context gathering

"Great — now let's build your profile.

**Two ways to do this — pick whichever is faster:**

**Option A: Drop context in.** Paste or attach any of these and I'll extract everything I need:
- Your website URL (I'll scrape it and research you)
- A pitch deck, proposal, or one-pager
- An existing CLAUDE.md or company description
- A previous brand guide or messaging doc
- Or just paste a few paragraphs about your business

**Option B: Just tell me your company name and website** and I'll do the rest.

What do you want to do?"

### Step 2: Business Profile (config.md)

**If the user provided context (Option A):**
- Read/scrape whatever they gave you (use WebFetch for URLs, Read for files)
- Extract all the information needed for config.md: company name, website, industry, what they sell, ICP, voice, differentiators, etc.
- Fill in config.md with what you found
- Show the user what you filled in and ask: "Here's what I pulled from your context. Anything I got wrong or missing?"
- Ask follow-up questions ONLY for sections you couldn't fill from the provided context (e.g., qualification criteria, social profiles, API keys)

**If the user chose questions (Option B) or if you need to fill gaps:**
Read `config.md` to check what's already filled in.

### Approach: Propose, Don't Interrogate

**CRITICAL:** Do NOT ask open-ended homework questions. Instead:
- **Make a hypothesis first** based on whatever you already know (company name, website, industry, prior answers)
- **Propose a draft answer** and ask the user to confirm, tweak, or correct
- **Offer options** when there are common patterns instead of asking from scratch
- **Batch related questions** — don't ask 9 separate things one at a time

The user should feel like they're reviewing and refining YOUR suggestions, not filling out a form.

### Flow

**Round 1: Get the basics (1 question)**
"What's your company name and website? (Or just the name — I'll look up the rest.)"

After the user answers, gather as much context as possible before proposing:

1. **Scrape their website** with WebFetch — homepage, about page, services page. Extract positioning, offerings, team size, client types.

2. **Check `.env` for EXA_API_KEY.** If it exists, use Exa to deepen the research:
   ```bash
   curl -s "https://api.exa.ai/search" \
     -H "x-api-key: $EXA_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
       "query": "[COMPANY NAME] company overview",
       "numResults": 5,
       "type": "auto",
       "category": "company",
       "contents": {"text": true, "highlights": true}
     }'
   ```
   Also search for their competitors and industry context:
   - "[COMPANY NAME] competitors alternatives"
   - "[INDUSTRY] market trends"
   
   This gives you revenue estimates, employee count, market position, competitor names — making your config.md draft significantly better.

3. If no Exa key, that's fine — work with what the website gives you.

Then move to Round 2 with all this context.

**Round 2: Propose the full profile (1 big draft)**
Based on what you know, draft the ENTIRE config.md and show it to the user:

"Based on [what you told me / your website], here's my read on your business. Edit anything that's off:

**What you sell:** [your best guess from context]
**ICP:** [proposed target — role, size, industry, geography]
**Qualification criteria:** Based on your offering, here's how I'd score prospects:
- 5/5: [proposed perfect fit]
- 4/4: [proposed hot]
- 3/5: [proposed warm]
- 2/5: [proposed cold]
- 1/5: [proposed no-fit]
**Brand voice:** [proposed tone based on website/context — e.g., 'Bold and direct — short sentences, no fluff, numbers over adjectives']
**Differentiators:** [proposed based on what you can infer]

What should I change?"

**Round 3: Learn their voice (from examples, not adjectives)**
Don't ask "how would you describe your tone?" — that gets useless answers like "professional but friendly."

Instead ask: "Drop me 1-2 examples of writing or speaking that sounds like YOU. Could be:
- A cold email or DM you've sent
- A LinkedIn post or caption
- A sales page or proposal
- A discovery call transcript (from Fireflies, Fathom, Otter, etc.)
- Even a voice note transcript

I'll learn your voice from the real thing.

**Got a call notetaker?** If you use Fireflies, I can pull your transcripts directly (add your API key later). Or just paste a transcript from any notetaker — Fathom, Otter, whatever you use."

If they provide examples, analyze them for:
- Sentence length and structure
- Formality level
- Use of questions, bold claims, humor, directness
- Words/phrases they naturally reach for
- What they DON'T do (no emojis, no jargon, etc.)

Write the Brand Voice section of config.md based on what you observe — not generic labels like "professional" but specific patterns like "Short punchy sentences. Leads with numbers. Never says 'I hope this finds you well.' Uses 'here's the thing' as a transition."

If they don't have examples or skip this, propose a voice based on their website copy and let them tweak it.

**Round 4: Fill remaining gaps (only what's missing)**
After voice is captured, ask ONLY about things you genuinely couldn't infer:
- Social profile URLs (can't guess these)
- Platform preferences (propose based on what you see — "Looks like you're active on LinkedIn and Instagram — sound right?")
- Custom instructions (only if the user seems like they'd have specific rules)

Save everything to `config.md` after each round. Use the user's exact words — don't corporate-ify their voice.

### Step 2.5: Outreach Configuration (outreach.md)

After config.md is populated, set up the outreach voice. This controls how every email and LinkedIn message sounds.

Read `outreach.md`. If it still has the default example data (check if Social Proof mentions "Altari" or "Ahmed"), walk the user through customizing it:

"Your business profile is set. Now let's configure how your outreach sounds — this controls every email and LinkedIn message the campaign writes."

**Offer positioning** — propose a one-liner based on what you learned from config.md:
"Based on your profile, here's how I'd position your offer in one sentence: '[proposed sentence]'. Want to tweak it?"

**Social proof** — ask for specific numbers:
"What are your strongest proof points? Things like: clients served, hours saved, revenue generated, ROI achieved, notable client names. Drop whatever you have — I'll format it."

**Example sequences** — ask for real examples:
"Do you have any cold emails or LinkedIn messages that have worked well? Paste one in and I'll match the style. If not, I'll use a direct, peer-to-peer tone."

**Industry notes** — propose based on their ICP:
"Based on your ICP, you're targeting [industries]. Any specific pain points you know work well for each? For example, 'staffing firms → reps spend more time sourcing than selling.'"

Save updates to `outreach.md`. If they skip any section, keep the defaults.

If user says "skip" for the whole step, leave outreach.md as-is and move on.

### Step 3: API Keys (.env)
Check if `.env` exists. If not, copy from `.env.example`:
```bash
cp .env.example .env
```

Then tell the user:
"Now let's set up your API keys. You only need keys for agents you plan to use.

**How keys are handled:** Your keys are stored locally in a `.env` file that never gets shared or committed to git. When an agent needs to call an API (like Apify or Apollo), Claude reads the key from `.env` and makes the call. Your keys are never saved in output files or displayed in chat.

Here's what each service does and costs:"

Present this table:

| Service | What it does | Price | Get a key |
|---------|-------------|-------|-----------|
| **Exa** | AI-powered web search — finds companies, people, news by meaning not just keywords | Free 1K searches/mo | dashboard.exa.ai |
| **Firecrawl** | Scrapes any website into clean text — reads company sites, competitor pages | Free 500 pages, then $16/mo | firecrawl.dev |
| **Apify** | LinkedIn scraping — profiles, company pages, post comments | Free $5/mo credits, then $49/mo | console.apify.com |
| **Apollo** | Lead database — find companies and people by filters, get verified emails | From $49/mo | app.apollo.io |
| **Instantly** | Sends cold email campaigns automatically — sequences with follow-ups | From $30/mo | app.instantly.ai |
| **HeyReach** | Sends LinkedIn outreach campaigns — connection requests + DMs | From $79/mo | app.heyreach.io |
| **Hunter** | Finds email addresses from company domains — backup when Apollo misses | Free 50 searches/mo | hunter.io |
| **Deepgram** | Transcribes audio — used to extract spoken hooks from Instagram reels | Free $200 credits, then $4/hr | console.deepgram.com |
| **Fireflies** | Call notetaker — pull meeting transcripts for research and voice learning | Free plan, then $19/mo | fireflies.ai |
| **OpenAI** | DALL-E 3 image generation — Cicero auto-generates images for your content | Pay-per-image (~$0.04/image) | platform.openai.com/api-keys |

**My recommendation to start:** Exa (free) + Apollo ($49/mo) gets you company discovery + verified emails — enough for a full `/campaign` run. Add Firecrawl (free 500 pages) for deeper research. Everything else is optional.

Tell me which ones you have and add them to your `.env` file, or say 'skip' to set up keys later."

For each key the user says they have:
- Tell them to open `.env` in their text editor
- Tell them the exact variable name to fill in (e.g., `EXA_API_KEY=your-key-here`)
- Confirm they've added it

### Step 4: Validation
Read `.env` and check which keys are present (non-empty). Report:
- Which keys are configured
- Which agents are ready to use (based on available keys)
- Which agents need additional keys

### Step 5: Done
"You're all set! Here's what to do next:

**Run your first campaign:** Type `/campaign` — it will find leads matching your ICP, research each company's operations, and write outreach that pitches AI automation specific to each prospect's business. You get an HTML dashboard + CSVs ready for Instantly/HeyReach.

**Or run individual agents:**
- `/felix` — find leads
- `/pluto` — research a specific prospect
- `/atlas` — research a company
- `/emilio` — write a cold email sequence
- Type 'list agents' to see everything available.

Your business profile is saved in config.md, outreach voice in outreach.md, and keys in .env. Edit any of these anytime."
