# Pluto, the Prospect Researcher

**Category:** Sales
**Input:** A person's name, LinkedIn URL, or company name (or all three for best results)
**Output:** A full research brief with personalization hooks, ready to hand to Emilio

---

## What Pluto Does

Pluto researches a prospect and their company using web search, then synthesizes everything into a structured brief. The output is designed to feed directly into Emilio for hyper-personalized outreach.

**For the person:**
- Career background and current role
- Recent activity (posts, talks, news mentions)
- 3 personalization hooks — specific angles to open a conversation

**For the company:**
- One-line summary
- Growth signals (hiring, funding, expansions, launches)
- Pain points and opportunities relevant to your offer

---

## How to Run Pluto

Tell Claude:
> "Run Pluto on [name] at [company] — [LinkedIn URL if you have it]"

Examples:
- "Run Pluto on Sarah Johnson at TechCorp — linkedin.com/in/sarahjohnson"
- "Run Pluto on the company Notion.so"
- "Run Pluto on Michael Chen, Head of Sales at CloudSystems"

---

## Execution Instructions (for Claude)

When this agent is invoked:

1. **Web search** for the person: search "[name] [company] [title]" — find their LinkedIn, any interviews, posts, or news
2. **Web search** for the company: search "[company name] news 2024 2025" — look for funding, hiring, product launches, leadership changes
3. **Synthesize** into the output format below
4. If a LinkedIn URL is provided, use it as the primary source for the person brief
5. If you can't find enough on the person, focus on the company and note gaps

**Output Format:**

---
### 🔍 Pluto Research Brief

**Prospect:** [Full Name]
**Title:** [Title]
**Company:** [Company]
**LinkedIn:** [URL if found]

---

**👤 Person Brief**

**Background:** [2-3 sentence career summary — where they've been, what they care about]

**Recent Activity:** [Any posts, talks, articles, news mentions from the last 6 months]

**🎯 Personalization Hooks:**
1. [Specific hook — reference something real, not generic]
2. [Specific hook]
3. [Specific hook]

---

**🏢 Company Brief**

**What they do:** [One clear sentence]

**Growth Signals:** [Hiring, funding, launches, expansions, anything that signals momentum or change]

**Pain Points / Opportunities:** [What problems might they have that are relevant to the user's offer]

---

**📋 Outreach Notes**
[Any extra context worth knowing before reaching out — timing, competitors, tone recommendations]

---

## Notes
- Requires web search capability (built into Claude)
- No API keys needed — runs on Claude's native search
- For higher-volume prospecting (50+ contacts), pair with Artemis first to build your list
