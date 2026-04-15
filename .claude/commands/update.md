---
name: update
description: Pull the latest Agent OS updates from GitHub
---

You are running the Agent OS updater. Pull the latest changes and tell the user what's new.

## Process

### Step 1: Check how they installed

Run:
```bash
git remote -v 2>/dev/null
```

**If git remote exists** → they cloned the repo. Proceed to Step 2.

**If no git remote** → they downloaded a ZIP. Tell them:

"Looks like you downloaded Agent OS as a ZIP rather than cloning it.

To get updates:
1. Go to **github.com/alassafi97/agent-os**
2. Click the green **Code** button → **Download ZIP**
3. Unzip it somewhere new
4. Copy your personal files across:
   - `.env` → your API keys
   - `config.md` → your business profile
   - `outreach.md` → your outreach voice
5. Open the new folder in Claude Code

Your outputs in the `outputs/` folder won't be affected — they stay in your old folder.

**Tip for next time:** Clone the repo with git so you can update with one command:
```bash
git clone https://github.com/alassafi97/agent-os.git
```
"

Then stop.

---

### Step 2: Check for uncommitted changes

Run:
```bash
git status --short
```

If there are modified tracked files, warn the user:
"You have local changes to tracked files. Updating may overwrite them. The files most likely to conflict are agent `.md` files if you've edited them directly.

Your personal files (`config.md`, `outreach.md`, `.env`, `outputs/`) are safe — they're gitignored.

Want to proceed?"

If they say yes, continue. If no, stop.

---

### Step 3: Pull latest

Run:
```bash
git pull origin master
```

Capture the output.

**If "Already up to date."** → Tell the user: "You're already on the latest version. Nothing to update."

**If there were changes** → proceed to Step 4.

**If there's a merge conflict** → tell the user:
"There's a merge conflict — this usually means you edited an agent file that was also updated upstream. Run `git status` to see which files conflict, then either accept the incoming version (`git checkout --theirs <file>`) or keep yours (`git checkout --ours <file>`). Then run `git add <file> && git commit`."

---

### Step 4: Show what changed

Run:
```bash
git log --oneline -10
```

Show the last few commits so the user knows what's new. Format it cleanly:

"✅ Updated successfully. Here's what's new:

[list the commit messages]"

---

### Step 5: Check if config structure changed

Run:
```bash
git diff HEAD~1 HEAD -- config.example.md
```

If `config.example.md` changed, tell the user:
"The config template was updated — there may be new fields available. Run `/setup` to review and fill in anything new."

Otherwise: "Your `config.md`, `outreach.md`, and `.env` are untouched — no action needed."

---

### Step 6: Done

"You're on the latest version. Type `/list-agents` to see the full catalog, or just tell me what you want to do."