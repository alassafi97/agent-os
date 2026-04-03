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

Then tell the user:
"Welcome to Agent OS! Let's get you set up. I need to know about your business so every agent can work for you.

**Two ways to do this — pick whichever is faster:**

**Option A: Drop context in.** Paste or attach any of these and I'll extract everything I need:
- Your website URL (I'll scrape it)
- A pitch deck, proposal, or one-pager
- An existing CLAUDE.md or company description
- A previous brand guide or messaging doc
- Or just paste a few paragraphs about your business

**Option B: I'll ask you questions.** Takes about 5 minutes — I'll go one section at a time.

Either way works. What do you want to do?"

### Step 2: Business Profile (config.md)

**If the user provided context (Option A):**
- Read/scrape whatever they gave you (use WebFetch for URLs, Read for files)
- Extract all the information needed for config.md: company name, website, industry, what they sell, ICP, voice, differentiators, etc.
- Fill in config.md with what you found
- Show the user what you filled in and ask: "Here's what I pulled from your context. Anything I got wrong or missing?"
- Ask follow-up questions ONLY for sections you couldn't fill from the provided context (e.g., qualification criteria, social profiles, API keys)

**If the user chose questions (Option B) or if you need to fill gaps:**
Read `config.md` to check what's already filled in.

Walk through each section conversationally — ask the user one section at a time, don't dump all questions at once:

1. **Company basics** — "What's your company name, website, and industry?"
2. **What you sell** — "What products or services do you offer? What's your typical price range and sales cycle?"
3. **ICP** — "Who's your ideal customer? What role, company size, and industry are you targeting? What geography?"
4. **Qualification criteria** — "How do you score whether a company is a good fit? Walk me through what makes a 5/5 perfect fit vs a 1/5 no-fit. Think about: business maturity, whether they have outbound already, deal size, growth appetite, and openness to tech/AI." Fill in both the criteria descriptions AND the 1-5 scoring scale in config.md.
5. **Brand voice** — "How would you describe your brand voice? Bold and direct? Friendly? Professional? Any words or phrases you always use or always avoid?"
6. **Differentiators** — "What makes you different from competitors? Give me your top 2-3."
7. **Social profiles** — "Drop your LinkedIn, Instagram, or any other social links you want agents to use."
8. **Target platforms** — "Which platforms do you primarily use for business? LinkedIn, email, Instagram, etc?" Check the relevant boxes in config.md.
9. **Custom instructions** — "Anything else your agents should always know? Any rules or preferences?"

After each answer, update `config.md` with their responses. Use their exact words where possible — don't corporate-ify their voice.

### Step 3: API Keys (.env)
Check if `.env` exists. If not, copy from `.env.example`:
```bash
cp .env.example .env
```

Then tell the user:
"Now let's set up your API keys. You only need keys for agents you plan to use. Which of these do you have?"

List the services from `.env.example` and ask which ones they use. For each one they have:
- Tell them to open `.env` in their text editor
- Tell them where to find the key (link is in `.env.example`)
- Confirm they've added it

### Step 4: Validation
Read `.env` and check which keys are present (non-empty). Report:
- Which keys are configured
- Which agents are ready to use (based on available keys)
- Which agents need additional keys

### Step 5: Done
"You're all set! Here's what you can do now:
- Say 'list agents' to see all available agents
- Say 'run [agent name]' to run a specific agent
- Or just tell me what you need and I'll find the right agent.

Your business profile is saved in config.md and your keys are in .env. You can update either anytime."
