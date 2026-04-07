---
name: campaign
description: Run a full GTM campaign — find leads, research their business, write AI-specific outreach, export to Instantly/HeyReach
---

Display this banner first:
```
  ██████╗ █████╗ ███╗   ███╗██████╗  █████╗ ██╗ ██████╗ ███╗   ██╗
 ██╔════╝██╔══██╗████╗ ████║██╔══██╗██╔══██╗██║██╔════╝ ████╗  ██║
 ██║     ███████║██╔████╔██║██████╔╝███████║██║██║  ███╗██╔██╗ ██║
 ██║     ██╔══██║██║╚██╔╝██║██╔═══╝ ██╔══██║██║██║   ██║██║╚██╗██║
 ╚██████╗██║  ██║██║ ╚═╝ ██║██║     ██║  ██║██║╚██████╔╝██║ ╚████║
  ╚═════╝╚═╝  ╚═╝╚═╝     ╚═╝╚═╝     ╚═╝  ╚═╝╚═╝ ╚═════╝ ╚═╝  ╚═══╝
                   ⚡ GTM Campaign Builder

  Find leads → Research their business → Write AI-specific outreach → Export.
  Every email pitches AI for their specific operations. You review at every step.
```

You are orchestrating a full go-to-market campaign. Each stage feeds the next. The output is a ready-to-send campaign where every prospect gets outreach tailored to what AI could automate in THEIR specific business.

---

## Pre-Flight

1. **Read `config.md`** — verify it's filled in (company, ICP, qualification criteria, voice). If not: "Run `/setup` first — I need your business profile before building a campaign."

2. **Read `outreach.md`** — verify it exists and has been customized (check if Social Proof still has default examples). If not customized: "Your outreach config still has example data. Run `/setup` to customize it, or edit `outreach.md` directly. Continuing with defaults for now."

3. **Read `.env`** — check which keys are available. Report what's possible:
   - Apollo + Exa + Firecrawl = **full pipeline** — best setup. Verified emails, deep research, business-specific outreach.
   - Apollo + Exa = full pipeline minus website scraping (research will be lighter)
   - Exa only = company discovery but no contact data — user must provide a lead list or add Apollo
   - No keys = "You need at least Exa to run a campaign. Get a free key at dashboard.exa.ai"

4. **Get campaign details** from the user in a single message — ask all of these at once:
   - **Campaign name** — short label for the folder (e.g., "saas-founders-us")
   - **Target criteria** — who are we going after? (or "use my ICP from config.md")
   - **Channel** — email, LinkedIn, or both?
   - **Volume** — how many prospects? (default: 20-30 for first campaign)
   - **Starting point** — "Start from scratch" OR "I already have a lead list" (CSV or paste)
   - **CTA preference** — what do you want prospects to do? Offer options:
     1. Book a call — "Worth a 15-min call to see what this looks like for [Company]?"
     2. Send a resource — "Want me to send over a breakdown of how this works?"
     3. Watch a demo — "I recorded a 3-min walkthrough — want me to send it?"
     4. Reply with interest — "Is any of this on your radar?" / "Worth exploring?"
     (default: option 4 — lowest friction, best for cold outreach)
   - **Email sequence length** — how many emails in the sequence? (default: 3)
   - **LinkedIn sequence length** — how many DMs after connection? (default: 3)
   - **Contacts per company** — how many people to target per company? (default: 2, max: 5)

5. **Create campaign folder:**
   ```bash
   mkdir -p outputs/campaigns/[campaign-name]/research
   ```

---

## Stage 1: FIND LEADS

**If starting from scratch — Apollo Two-Step Pipeline (verified 2026-04-03):**

1. **Discover companies** — Run Apollo company search (structured filters) + Exa semantic search in parallel. Merge by domain, deduplicate.
2. **Find people** — For each company domain, run Apollo `mixed_people/api_search` with ICP title/seniority filters. Returns person IDs + names. Free.
3. **Confirm enrichment cost** — Tell user: "Found X people across Y companies. Enriching will use ~X email credits. Proceed?"
4. **Enrich** — Apollo `people/match` with each person ID. Batches of 5 parallel, 0.5s between batches. Returns verified emails, LinkedIn URLs, seniority, location.
5. **Filter garbage** — Remove prospects with fake/spam names, duplicates, non-decision-maker titles. Keep only prospects with email OR LinkedIn.
6. **Save** — `contacts.json` (full data) + `contacts.csv` (flat export)

**If user provides a lead list:**
- Accept CSV, pasted data, or company names/domains
- Parse into standard format, save to contacts

**Gate 1: User reviews the list**

Show summary: "[X] companies, [Y] contacts with verified emails, [Z] with LinkedIn only."

"Here's your lead list.

**Options:**
- **Continue** → I'll research every prospect and analyze their business
- **Filter** → Remove anyone who doesn't look like a fit
- **Import your own** → Drop in a CSV to replace or supplement
- **Export** → Take the CSV and come back later"

Wait for approval before Stage 2.

---

## Stage 2: RESEARCH + BUSINESS ANALYSIS

> **This is the stage that makes the outreach specific.** Every prospect gets researched. No exceptions. No tiering. Generic research = generic outreach.

### Research (API calls)

**For EVERY prospect with email or LinkedIn, run this sequence:**

1. **Exa company search** — "[Company Name] services operations workflow" — what the company does, how it operates (free)
2. **Exa person search** — "[Person Name] [Company] background" — role context, career trajectory (free)
3. **Firecrawl homepage** — scrape `https://[domain]` — their actual service/product offering, messaging, clients (1 credit)
4. **Firecrawl about/services page** — scrape `https://[domain]/about` or `/services` — deeper operational context (1 credit)

**Batching:** Research in batches of 5-10 prospects. Show progress: "Researching prospect 5/20..."

**Cost estimate before starting:** "Researching X prospects: ~X×2 Exa searches (free) + ~X×2 Firecrawl pages (X×2 credits). Proceed?"

**If Firecrawl key is missing:** Skip website scraping. Use Exa results only — outreach will be less specific but still personalized.

**If a scrape fails or returns thin content:** Note it and continue. Use industry-level analysis from `outreach.md` Industry-Specific Notes as fallback.

### Business Operation Analysis (no API calls — pure reasoning)

**This is the critical step.** After collecting research for each prospect, analyze their business:

For each prospect, answer these questions using the research data:

1. **What does this company do day-to-day?** Not their mission statement — their actual operational workflows. A staffing firm does candidate sourcing, intake processing, compliance tracking, client reporting. A B2B ecommerce firm does catalog management, product data migration, implementation scoping.

2. **What are the repetitive, manual, high-volume processes?** The ones that eat team capacity. The ones where someone is doing the same task 50 times a day with slight variations.

3. **Which of those could AI automate, and what would that look like specifically?** Not "AI can help with operations" — specific: "AI parses resumes, matches skills to open reqs, ranks candidates by fit. Recruiter reviews top 5 instead of searching through 500."

**Output per prospect:**
- `business_operations` — what they actually do (2-3 sentences)
- `ai_automation_opportunities` — 3 specific things AI could automate in their business
- `specific_pitch_angle` — the single strongest angle for the opening email

**Quality check:** If the analysis is generic enough to apply to 1,000 other companies (e.g., "automate repetitive tasks"), redo it. The whole point is specificity.

### Save research

- `outputs/campaigns/[name]/research.json` — full research data per prospect
- `outputs/campaigns/[name]/contacts.json` — updated with `business_operations`, `ai_automation_opportunities`, `specific_pitch_angle` fields

### Gate 2: User reviews research + analysis

Show the top 5 prospects with their AI automation analysis:

```
1. Ian Wagemann — CEO @ Amerit Consulting — 4/5
   AI angle: Candidate intake processing, compliance tracking, client reporting automation
   
2. Tom Melaragno — CEO @ Compri Consulting — 4/5
   AI angle: IT requirement translation, talent matching by stack depth, time-to-present speed
```

"Research and business analysis complete for [X] prospects.

**Options:**
- **Continue** → I'll write outreach using these angles
- **Adjust** → Change the AI angle for specific prospects
- **Export** → Take the research and write outreach yourself"

---

## Stage 3: WRITE OUTREACH

> **Every email pitches AI for THEIR specific business.** Not "we automate outreach." Not generic angles. Each email describes what AI would do in their operations.

### Read before writing
1. Read `outreach.md` — social proof, CTAs, tone, banned phrases
2. Read `config.md` — company context, voice
3. Read each prospect's `specific_pitch_angle` and `ai_automation_opportunities`
4. Recall the campaign settings from Pre-Flight: **CTA preference**, **email sequence length**, **LinkedIn sequence length**

### Email sequence (if channel = email or both)

**Number of emails = user's selected email sequence length** (default: 3). Adapt the structure below based on count:
- 1 email: Email 1 only (best pitch + CTA)
- 2 emails: Email 1 + Email 3 (breakup)
- 3 emails: Full sequence below (default)
- 4-5 emails: Add follow-up angles between Email 2 and breakup (new proof point or angle each time)

**Email 1 — Business-Specific AI Pitch (≤100 words):**
- First line: reference a signal about them or their company (NOT "I noticed you're in [industry]")
- Body: describe their specific operational bottleneck from the analysis
- Show what AI does for THAT process — use the "three things AI handles for [their type of business]" pattern:
  1. [Specific process AI automates]
  2. [Second specific process]
  3. [Third specific process]
- CTA: use the **user's chosen CTA** from Pre-Flight. If they chose "reply with interest" → question format. If "book a call" → "Worth a 15-min call to see what this looks like for [Company]?" If "send a resource" → "Want me to send over a breakdown?"
- Subject: lowercase, company name or operational reference, under 60 chars

**Email 2 — Social Proof + Specific Result (≤80 words):**
- Different angle from Email 1 — zoom in on ONE of the automation opportunities
- Include a specific result from `outreach.md` Social Proof, adapted to their industry
- CTA: offer to show or send something — match the user's CTA preference

**Email 3 — Breakup (≤50 words):**
- Short. Respectful. If their operations are running smoothly, say so.
- One-line restate of the value
- Break-up energy: "If not, I'll stop here."

### LinkedIn sequence (if channel = LinkedIn or both)

**Number of DMs = user's selected LinkedIn sequence length** (default: 3). Always include the connection request regardless of DM count.

**Connection request (≤280 chars, counted):**
- Reference ONE specific thing about their company. Never pitch. Never mention your company.

**DM 1 (≤500 chars) — after connection accepted:**
- Question about THEIR operations. Never pitch. Reference their business specifically.
- "How does [Company] handle [specific operational process from analysis]?"

**DM 2 (≤500 chars):**
- Bridge to what you do. Reference a result relevant to their industry from `outreach.md`.
- Soft CTA — match the user's CTA preference.

**DM 3 (≤500 chars):**
- Direct but respectful. Offer a call or the chosen CTA action. Break-up if no response.

**DM 4-5 (≤500 chars each, only if user chose longer sequence):**
- New angle or proof point. Each DM should feel different from the last.

### Quality gates (enforce before showing to user)

Run these checks on EVERY piece of outreach. If any fails, rewrite silently:

1. **Word/char counts** — Email 1 ≤100 words, Email 2 ≤80, Email 3 ≤50. CR ≤280 chars. DMs ≤500 chars.
2. **Banned phrase scan** — check against `outreach.md` banned list. Zero tolerance.
3. **Specificity check** — could this email apply to 1,000 other companies? If yes, rewrite.
4. **Hook-first check** — first sentence of every email must reference the prospect specifically. If it starts with "I" or a generic statement, rewrite.
5. **No pitch in connection request or DM 1** — if found, rewrite.
6. **CTA consistency** — every CTA in the sequence should match the user's chosen CTA preference. If wrong, fix.
7. **CTA escalation** — Email 1 = soft question/hook, final email = breakup. If wrong, fix.

### Save outreach

- `outputs/campaigns/[name]/instantly-import.csv`:
  - Base columns: `first_name,last_name,email,company_name,title,linkedin_url,location`
  - Add email columns dynamically based on sequence length: `email_1_subject,email_1_body` through `email_N_subject,email_N_body`
  - Example for 3 emails: `first_name,last_name,email,company_name,title,linkedin_url,location,email_1_subject,email_1_body,email_2_subject,email_2_body,email_3_subject,email_3_body`
- `outputs/campaigns/[name]/linkedin-import.csv`:
  - Base columns: `first_name,last_name,linkedin_url,company,title,location,connection_request`
  - Add DM columns dynamically based on sequence length: `dm_1` through `dm_N`
  - Example for 3 DMs: `first_name,last_name,linkedin_url,company,title,location,connection_request,dm_1,dm_2,dm_3`

### Gate 3: User reviews outreach

Show 2-3 example sequences (pick one strong email and one strong LinkedIn) and ask:

"[X] prospects with personalized, business-specific outreach.

**Options:**
- **Approve** → I'll generate the dashboard and CSVs
- **Adjust tone** → Tell me what to change and I'll regenerate
- **Edit specific** → Pick which sequences to rewrite
- **Export** → Take the CSVs to Instantly/HeyReach yourself"

---

## Stage 4: PUSH TO SENDING TOOLS

**Email → Instantly (if INSTANTLY_API_KEY exists):**
1. Create campaign in DRAFT state (never auto-activate)
2. Push 1 test lead first — show user how it looks
3. After user confirms, push remaining leads in batches of 10
4. Log every push to `outputs/campaigns/[name]/instantly-push-log.csv`
5. Confirm before activating

**LinkedIn → HeyReach (if HEYREACH_API_KEY exists):**
1. List campaigns, user picks one + sender account
2. Push 1 test lead first
3. After confirmation, push remaining in batches of 10
4. Log every push

**If no sending tool keys:**
"Your outreach is saved as CSVs ready for import:
- `instantly-import.csv` → Import into Instantly (Leads → Import CSV)
- `linkedin-import.csv` → Import into HeyReach (Campaign → Add Leads)
These CSVs are also compatible with Google Sheets, Airtable, or any spreadsheet tool."

---

## Stage 5: CAMPAIGN DASHBOARD

> **This is the deliverable.** The HTML dashboard is what makes the output feel like a product, not raw data.

Generate `outputs/campaigns/[name]/campaign-table.html` with:

### Dashboard structure:
1. **Header** — campaign name, date, target criteria, APIs used
2. **Stats bar** — X email prospects, Y LinkedIn prospects, Z verified emails, N credits used
3. **Download buttons** — "Download Instantly CSV" + "Download LinkedIn CSV"
4. **Tabs** — "Email Outreach (X)" and "LinkedIn Outreach (Y)"
5. **Email tab** — spreadsheet table with columns: #, First Name, Last Name, Email, Status (pill badge), Company, Title, E1 Subject, E1 Body, E2 Subject, E2 Body, E3 Subject, E3 Body, LinkedIn, Location
6. **LinkedIn tab** — spreadsheet table with columns: #, First Name, Last Name, LinkedIn, Company, Title, Connection Request, DM 1, DM 2, DM 3, Location

### Styling:
- Dark theme (#0a0a0a background)
- Monospace font for data, system font for headers
- Sticky table headers
- Status pills: green (verified), yellow (extrapolated), gray (unavailable)
- TOP 5 prospects highlighted with purple tag + subtle row tint
- Email body columns min-width 440px for readability
- Subject columns min-width 180px
- Tab switching via JavaScript (no framework)

### Also save:
- `summary.md` — markdown version of the campaign results
- `instantly-import.csv` — ready for Instantly
- `linkedin-import.csv` — ready for HeyReach

After generating, tell the user:
"Campaign complete. Open `campaign-table.html` in your browser to see the full dashboard. CSVs are ready for Instantly/HeyReach import."

---

## Rules

- **Gate every stage.** Never auto-proceed without user approval.
- **Research every prospect.** No tiering. Generic research = generic outreach. The quality of Stage 3 depends entirely on the depth of Stage 2.
- **Business analysis is mandatory.** Between research and outreach, you MUST analyze what AI could automate in each prospect's specific business. This is what makes the outreach land.
- **Always save CSVs + HTML.** The HTML dashboard is the primary deliverable. CSVs are for import.
- **Batch sensibly.** Research in batches of 5-10. Show progress.
- **Handle failures gracefully.** If one API call fails, skip that prospect and continue. Note failures in the summary.
- **Every outreach must be specific.** If you catch yourself writing something that could apply to any company, stop and rewrite using the business analysis.
- **Cost transparency.** Always tell the user what API calls will cost before running them.

## API Safety (mandatory — see CLAUDE.md for full rules)

- **Apollo:** Search is free. Enrichment costs email credits. Confirm before batch enrichment. Batch of 5 parallel, 0.5s between batches.
- **Exa:** Free tier, max 30 results per search. Cap `numResults` to what you need.
- **Firecrawl:** 1 credit per page. Cap to 2 pages per prospect (homepage + about/services).
- **Apify (if used):** Always set `maxTotalChargeUsd=1.00`. Always abort runs after fetching. Never leave runs in RUNNING state.
- **Test before batch.** First API call in any session = single small test. Verify before scaling.

User input: $ARGUMENTS
