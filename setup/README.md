# Agent OS — Setup Guide

**Time to set up: ~5 minutes**

---

## What You Need

| Tool | Required for | Free? |
|------|-------------|-------|
| VS Code | Running the agents | ✅ Free |
| Claude Code (VS Code extension) | The AI brain | ✅ Free |
| Anthropic API key | Powers everything | Pay-as-you-go (~$5-10/mo typical use) |

Pluto and Cicero work with just the above. Emilio needs no extras either.
Future agents (Artemis, Leonardo) may need Apify or Exa — each file will tell you.

---

## Step 1 — Install VS Code

Download from: https://code.visualstudio.com
(Skip if you already have it)

---

## Step 2 — Install Claude Code

1. Open VS Code
2. Click the Extensions icon in the left sidebar (or press `Cmd+Shift+X`)
3. Search for **"Claude Code"**
4. Click Install
5. A chat panel will appear on the left

---

## Step 3 — Get Your Anthropic API Key

1. Go to https://console.anthropic.com
2. Sign up / log in
3. Click **API Keys** → **Create Key**
4. Copy the key (starts with `sk-ant-...`)
5. Add $10 credit to start — this covers a lot of usage

---

## Step 4 — Connect Your API Key to Claude Code

1. In VS Code, open the Claude Code panel
2. Click **Settings** or when prompted, paste your API key
3. Done

---

## Step 5 — Open Agent OS in VS Code

1. Unzip the Agent OS folder you downloaded
2. In VS Code: **File → Open Folder** → select the `agent-os` folder
3. Open the Claude Code chat panel
4. Type: **"What agents do I have?"**

You should see the full agent roster. You're live.

---

## Running Your First Agent

**Test Pluto** (no setup needed):
> "Run Pluto on [any company name or LinkedIn URL]"

**Test Cicero** (no setup needed):
> "Run Cicero on this idea: [any content idea]"

**Test Emilio** (add your sender details first):
> "Run Emilio — I'm [your name] from [company], we [what you do]. Write a sequence for [prospect name] at [company]"

---

## Costs

Typical usage costs:
- Pluto research brief: ~$0.01–0.03 per run
- Emilio 3-email sequence: ~$0.02–0.05 per run
- Cicero 7-format pack: ~$0.05–0.10 per run

A $10 credit will last most people several weeks of regular use.

---

## New Agent Drops

Every week, a new agent `.md` file is posted in the Agent OS community.

To add it:
1. Download the `.md` file
2. Drag it into your `agents/` folder
3. That's it — Claude will find it automatically

---

## Questions?

Post in the Agent OS community on Skool: skool.com/airepublic
