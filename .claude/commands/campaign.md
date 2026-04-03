---
name: campaign
description: Run a full GTM campaign — find leads, research, write outreach, push to sending tools
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

  Full pipeline: Find leads → Research → Outreach → Send.
  You review at every step. CSV exports at any stage.
```

You are orchestrating a full go-to-market campaign. This is the big one — it chains multiple agents together in sequence, with the user reviewing and approving at each stage.

## Pre-Flight

1. **Read `config.md`** — verify it's filled in (company, ICP, qualification criteria, voice). If not: "Run `/setup` first — I need your business profile before building a campaign."

2. **Read `.env`** — check which keys are available. Report what's possible:
   - Exa + Apify + Apollo = full pipeline (find → research → outreach)
   - Exa + Apify only = research + outreach (user provides their own lead list)
   - No keys = "You need at least Exa to run a campaign. Get a free key at dashboard.exa.ai"

3. **Get campaign details** from the user:
   - **Campaign name** — short label for the folder (e.g., "saas-founders-us")
   - **Target criteria** — who are we going after? (or "use my ICP from config.md")
   - **Channel** — email, LinkedIn, or both?
   - **Volume** — how many prospects? (default: 20-50 for first campaign)
   - **Starting point** — "Start from scratch" OR "I already have a lead list" (CSV or paste)

4. **Create campaign folder:**
   ```bash
   mkdir -p outputs/campaigns/[campaign-name]
   ```

## Stage 1: FIND LEADS

**If starting from scratch:**
Run Felix's process internally (don't spawn a separate agent — run the steps here to keep everything in one conversation).

- Use the best available method (Apollo → Leads Finder → Exa) based on `.env` keys
- Search for companies matching the target criteria
- Find decision-makers at those companies
- Save results:
  - `outputs/campaigns/[name]/companies.csv` — company list
  - `outputs/campaigns/[name]/contacts.csv` — prospect list with names, titles, emails, LinkedIn URLs

**If user provides a lead list:**
- Accept CSV file, pasted data, or a list of company names/domains
- Parse it into the same CSV format
- Save to `outputs/campaigns/[name]/contacts.csv`

**CSV format for contacts.csv:**
```
first_name,last_name,title,company,domain,email,email_status,linkedin_url,location,source
John,Smith,CEO,Acme Corp,acme.com,john@acme.com,verified,https://linkedin.com/in/johnsmith,New York,apollo
```

**Gate 1: User reviews the list**
Show summary: "[X] companies found, [Y] contacts with emails, [Z] contacts LinkedIn-only."

Then ask:
"Here's your lead list — [link to contacts.csv]. 

**Options:**
- **Continue** → I'll research the top prospects
- **Filter** → Remove anyone who doesn't look like a fit
- **Import your own** → Drop in a Google Sheets CSV to replace or supplement this list
- **Export** → Take the CSV and come back later"

Wait for approval before Stage 2.

## Stage 2: RESEARCH

Run Atlas + Pluto's research process on the top prospects. Don't go deep on everyone — prioritize.

**Research strategy:**
- If list is ≤20 contacts: research all
- If list is 20-50: research top 30 by ICP fit / email quality
- If list is 50+: research top 20, offer to continue in batches

**For each prospect, run this sequence:**
1. Exa search on the company (Atlas-style — brief, not full report)
2. Exa search on the person (Pluto-style — brief)
3. Firecrawl the company website homepage (if available)
4. Apify LinkedIn profile (if APIFY key available)

**Save research as:**
- `outputs/campaigns/[name]/research.csv` — enriched version of contacts.csv with additional columns:
  ```
  ...existing columns...,company_summary,company_signals,person_summary,personalization_hooks,qualification_score
  ```
- `outputs/campaigns/[name]/research/[prospect-name].md` — full research report per prospect (optional, for high-value targets)

**Gate 2: User reviews research**
Show summary: "[X] prospects researched. Top 5 by qualification score:
1. [Name] at [Company] — [Score]/5 — [One-line hook]
2. ..."

Then ask:
"Research complete — [link to research.csv].

**Options:**
- **Continue** → I'll write outreach for all researched prospects
- **Select** → Pick which prospects to write outreach for
- **Export** → Take the research and write outreach yourself"

## Stage 3: WRITE OUTREACH

Run Emilio and/or Leonardo's outreach process for each prospect.

**For each prospect:**
1. Read their research data from research.csv
2. Read config.md for voice and offer
3. Generate personalized copy:
   - If email channel: 3-step email sequence (Emilio's framework)
   - If LinkedIn channel: connection request + 3 DM sequence (Leonardo's framework)
   - If both: generate both

**Apply outreach fundamentals from Emilio/Leonardo:**
- Hook first, no fluff, problem→solution
- CTA escalation (soft → medium → hard)
- No AI slop (use the banned phrase list)
- Each prospect gets UNIQUE copy — never reuse hooks

**Save outreach as:**
- `outputs/campaigns/[name]/outreach-email.csv`:
  ```
  first_name,last_name,email,company,email_1_subject,email_1_body,email_2_subject,email_2_body,email_3_subject,email_3_body,rationale
  ```
- `outputs/campaigns/[name]/outreach-linkedin.csv`:
  ```
  first_name,last_name,linkedin_url,company,connection_request,dm_1,dm_2,dm_3,rationale
  ```

**Gate 3: User reviews outreach**
Show 2-3 example sequences and ask:
"Here are sample sequences. [X] total prospects with personalized outreach.

**Options:**
- **Approve** → Ready to push to sending tools
- **Adjust tone** → Tell me what to change and I'll regenerate
- **Edit specific** → Pick which sequences to rewrite
- **Export** → Take the CSVs to Instantly/HeyReach yourself"

## Stage 4: PUSH TO SENDING TOOLS

**Email → Instantly (if INSTANTLY_API_KEY exists):**
1. Create Instantly campaign
2. Upload leads with per-contact custom variables (email_step_1-3)
3. Configure: weekday schedule, daily limit (ask user), stop on reply
4. Confirm before activating

**LinkedIn → HeyReach (if HEYREACH_API_KEY exists):**
1. List existing HeyReach campaigns
2. User picks campaign + sender account
3. Push leads with profile data
4. Note: DM copy needs to be configured in HeyReach UI

**If no sending tool keys:**
"No Instantly/HeyReach keys configured. Your outreach is saved as CSVs — you can:
- Import `outreach-email.csv` into Instantly manually
- Import `outreach-linkedin.csv` into HeyReach manually
- Or add your API keys to `.env` and run `/campaign` again to push directly"

## Stage 5: CAMPAIGN SUMMARY

After all stages complete, produce a campaign summary:

```markdown
# Campaign Summary: [Campaign Name]
**Date:** [Date]
**Target:** [Criteria]
**Channel:** [Email / LinkedIn / Both]

## Results
- **Companies found:** [X]
- **Contacts found:** [Y] ([Z] with verified emails)
- **Prospects researched:** [N]
- **Outreach sequences written:** [M]
- **Pushed to Instantly:** [Yes/No — X leads]
- **Pushed to HeyReach:** [Yes/No — X leads]

## Files
- `companies.csv` — company list
- `contacts.csv` — prospect list
- `research.csv` — enriched prospects with research
- `outreach-email.csv` — email sequences (ready for Instantly import)
- `outreach-linkedin.csv` — LinkedIn sequences (ready for HeyReach import)

## Top Prospects
1. [Name] at [Company] — [Score]/5
2. [Name] at [Company] — [Score]/5
3. [Name] at [Company] — [Score]/5
```

Save to `outputs/campaigns/[name]/summary.md`.

## Google Sheets Integration

Agent OS uses CSV files as the data layer. At any stage, the user can:
- **Export to Sheets:** "Take this CSV and import it to Google Sheets" (File → Import in Sheets)
- **Import from Sheets:** "Export your Google Sheet as CSV (File → Download → CSV) and drop it here"

Tell the user at each gate that CSVs are Sheets-compatible:
"This CSV is ready to import into Google Sheets, Airtable, or any spreadsheet tool."

## Rules

- **Gate every stage.** Never auto-proceed without user approval. Show what was produced and ask for next steps.
- **Always save CSVs.** Even if pushing to Instantly/HeyReach, keep the CSV backups.
- **Batch sensibly.** Don't research 200 prospects in one go. Start with 20-50, offer to continue.
- **Show progress.** Between stages, tell the user what's happening: "Researching prospect 5/20..."
- **Handle failures gracefully.** If one API call fails, skip that prospect and continue. Note failures in the summary.
- **Every CSV is importable.** Headers must be clean, consistent, and compatible with Instantly/HeyReach/Sheets.
- **Recommend next campaign.** After completion: "Want to run another campaign with different criteria? Or expand this one with more prospects?"

User input: $ARGUMENTS
