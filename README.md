# Agent OS

**Plug and play AI agents for Claude Code.**

One framework. Drop in `.md` files. Run agents instantly.

---

## How It Works

Agent OS is a folder you open in VS Code. Inside is a `CLAUDE.md` file that tells Claude Code it's operating as your AI agent system. Each agent is a single `.md` file in the `agents/` folder.

When you say *"Run Pluto on this company"* — Claude reads `agents/pluto.md`, follows the instructions inside, and executes the agent. That's it. No code, no backend, no deployment.

**The framework** = install once, never touch again.  
**The agents** = `.md` files you drag in whenever you want a new one.

---

## Quick Start

1. **Install VS Code** → https://code.visualstudio.com
2. **Install Claude Code** → search "Claude Code" in VS Code extensions
3. **Get an Anthropic API key** → https://console.anthropic.com (add $10 credit)
4. **Clone or download this repo** → open the `agent-os` folder in VS Code
5. **Type:** `"What agents do I have?"` — you're live

Full setup guide: [`setup/README.md`](setup/README.md)

---

## Agent Roster

| Agent | What it does | API keys needed |
|-------|-------------|-----------------|
| **Pluto** | Researches any prospect or company — background, signals, 3 personalization hooks | None |
| **Emilio** | Writes a 3-step cold email sequence from a Pluto brief or basic info | None |
| **Cicero** | Turns one idea or draft into 7 platform-ready formats (Reel, TikTok, LinkedIn, X Thread, Newsletter, YouTube) | None |

New agents drop weekly inside the [Agent OS community](https://skool.com/airepublic).

---

## Running Agents

Just talk to Claude naturally:

```
"Run Pluto on Sarah Johnson at TechCorp — linkedin.com/in/sarahjohnson"
```
```
"Run Emilio on this Pluto brief: [paste brief]"
```
```
"Run Cicero on this idea: why most AI agents fail in production"
```
```
"What agents do I have?"
```

---

## Adding New Agents

When a new agent drops in the community:

1. Download the `.md` file
2. Drag it into the `agents/` folder
3. Done — Claude finds it automatically next time you open the folder

---

## The Stack

- **Claude Code** — the AI engine (VS Code extension)
- **Anthropic API** — powers the agents (~$5–10/month typical use)
- **Exa API** — used by some advanced agents for web research (optional)
- **Apify** — used by Artemis for LinkedIn scraping (optional)

---

## Built by Ahmed Alassafi

[@alassafi.ai](https://instagram.com/alassafi.ai) · [Altari](https://altari.ai) · [Agent OS Community](https://skool.com/airepublic)
