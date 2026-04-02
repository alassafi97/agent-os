# Pluto, the Prospect Researcher

**Category:** Sales
**Input:** Person name + company name + LinkedIn URL (any combo, all three = best results)
**Output:** Structured research brief with company signals + person brief + 3 personalization hooks
**API keys needed:** `EXA_API_KEY`

---

## What Pluto Does

Pluto runs a two-phase research process — company first, then person — using Exa semantic search and Claude synthesis. This mirrors the exact research logic inside Altari's GTM OS.

**Phase 1 — Company Research:**
- Exa search: `"[company]" recent news OR funding OR product launch OR hiring` (last 6 months, news category)
- Synthesizes into: one_liner, signals (funding/hiring/product/press/partnership/expansion/leadership), growth summary, opportunities

**Phase 2 — Person Research:**
- Exa search: `"[full name]" "[company]"` (last 12 months)
- Uses company brief as context for synthesis
- Synthesizes into: role context, background, recent activity, 3 personalization hooks

Output is structured and ready to paste directly into Emilio.

---

## Setup

Add your Exa API key to `.env`:
```
EXA_API_KEY=your-key-here
```
Get one at: https://exa.ai (free tier available)

---

## How to Run Pluto

> "Run Pluto on [name] at [company] — [LinkedIn URL if available]"

Examples:
- `Run Pluto on Sarah Johnson at TechCorp — linkedin.com/in/sarahjohnson`
- `Run Pluto on the company notion.so`
- `Run Pluto on Michael Chen, Head of Sales at CloudSystems`

---

## Execution Instructions (for Claude)

When this agent is invoked:

### Phase 1 — Company Research

1. Read `EXA_API_KEY` from `.env`
2. Run Exa search:
   - Query: `"[company name]" recent news OR funding OR product launch OR hiring`
   - Parameters: `type: auto`, `numResults: 10`, `category: news`, `startPublishedDate: [6 months ago]`, `contents: { highlights: { numSentences: 5, highlightsPerUrl: 3 }, summary: true }`
   - Endpoint: `POST https://api.exa.ai/search` with header `x-api-key: [EXA_API_KEY]`
3. Synthesize results into company brief:
   - `one_liner` — one sentence describing what the company does
   - `signals` — array of: type (funding/hiring/product/press/partnership/expansion/leadership_change), text, date (YYYY-MM), url
   - `growth_summary` — headcount trajectory, momentum
   - `opportunities` — sales angles and likely pain points

### Phase 2 — Person Research

4. Run Exa search:
   - Query: `"[full name]" "[company name]"`
   - Parameters: `type: auto`, `numResults: 10`, `startPublishedDate: [12 months ago]`, `contents: { highlights: { numSentences: 5, highlightsPerUrl: 3 }, summary: true }`
5. Synthesize into person brief using company brief as context:
   - `role_context` — current role, title, company, tenure
   - `background` — previous roles, education, career arc
   - `recent_activity` — array of: type (content/press/social/speaking/podcast/award), text, date, url
   - `hooks` — exactly 3 personalization hooks for outreach (specific, not generic — reference real signals)

**Rules:**
- Never invent facts — if data is sparse, work with what's available and note gaps
- Hooks must reference real, specific things found in research — not generic observations
- If person data is sparse, use company signals to build the hooks instead

---

## Output Format

```
🔍 PLUTO RESEARCH BRIEF
━━━━━━━━━━━━━━━━━━━━━━

Prospect: [Full Name]
Title: [Title]
Company: [Company]
LinkedIn: [URL if found]

━━━━━━━━━━━━━━━━━━━━━━
🏢 COMPANY BRIEF

What they do: [one_liner]

Signals:
• [type] — [text] ([date]) [url]
• [type] — [text] ([date]) [url]
...

Growth: [growth_summary]

Opportunities: [opportunities]

━━━━━━━━━━━━━━━━━━━━━━
👤 PERSON BRIEF

Role: [role_context]

Background: [background]

Recent Activity:
• [type] — [text] ([date]) [url]
...

━━━━━━━━━━━━━━━━━━━━━━
🎯 PERSONALIZATION HOOKS

1. [Hook — specific, references real signal or activity]
2. [Hook — specific, references real signal or activity]
3. [Hook — specific, references real signal or activity]

━━━━━━━━━━━━━━━━━━━━━━
Ready for Emilio → paste this brief and say "Run Emilio on this"
```

---

## Notes
- Exa rate limit: if you hit 429, wait 30 seconds and retry
- For bulk prospecting (50+ contacts), use Artemis first to build your list, then run Pluto on each
- No LinkedIn URL? Pluto will still run — just less person-specific data
