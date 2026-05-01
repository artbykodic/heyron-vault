# VAULT - Persistent Memory for Heyron Agents

> Built for Heyron Agent Jam #1: Memory Systems

Vault gives any Heyron agent persistent memory across sessions, context resets, and model switches. Drop it into any agent and it will never lose its place again.

**Tested in production on a live AI publishing business - not just a demo.**

---

## The Problem

I started this project because my AI agent kept losing his memory.

Kodi is an autonomous agent I built to run my KDP coloring book publishing business. He generates images, approves or rejects them, formats PDFs, and publishes to Amazon - all without me having to do it manually.

But he had a serious memory problem. Every context reset, every new session, every model switch - Kodi would wake up with no idea what he was doing. Work got lost. Mistakes got repeated. I had to constantly re-explain everything.

Specifically Kodi would:

- Lose context mid-generation after just 4-5 images
- Repeat the same prompts over and over wasting API credits
- Go completely off-task after context filled up
- Create unauthorized scheduled jobs
- Have to be manually restarted and re-briefed constantly

I needed a real solution. Vault is what I built.

---

## The Solution

```
Cron fires daily   ->  Load status.txt (1 line)  ->  Resume or wait
Every 10 actions   ->  Silent checkpoint          ->  Push to GitHub
Session ends       ->  Write daily journal        ->  Archive work
Context resets     ->  Load status.txt            ->  Recover in seconds
```

---

## The 5 Layers

| Layer | What | When | File |
|-------|------|------|------|
| 1 - Active Memory | What I am doing RIGHT NOW | After every action | MEMORY.md + status.txt |
| 2 - Daily Journal | What happened today | End of every session | memory/YYYY-MM-DD.md |
| 3 - Completed Archive | Finished work, permanent record | Every completed task | memory/archive/completed_tasks.md |
| 4 - Error Log | Technical mistakes + fixes | Before every fix attempt | memory/archive/error_log.md |
| 5 - Behavior Log | Rogue actions, rule violations | Every behavioral incident | memory/archive/behavior_log.md |

---

## Two Key Discoveries

### 1. The Context Problem

Reading all memory files on boot fills context instantly. Loading MEMORY.md + SOUL.md + VAULT.md hit 100% context before the agent even started working.

**Vault's solution - two tier boot:**
- Context above 20%: load ONLY status.txt (one line, zero context cost)
- Context below 20%: load ## Active Job section only (not the whole file)

### 2. The Auto-Boot Problem

Adding boot instructions to SOUL.md does NOT reliably trigger auto-boot. The agent loads SOUL.md but still waits for the first user message.

**The fix: use TWO cron jobs.**

1. **Vault Boot** (every 24 hours) - daily wake-up and status report
2. **Memory Save** (every 1 hour) - backup checkpoint during active work

The daily boot handles orientation. The hourly save is a safety net for when the agent's own saves fail during active sessions. Together they ensure memory is never more than 1 hour out of date.

---

## Installation

**Step 1** - Copy VAULT.md to your agent workspace

**Step 2** - Create folder structure:
```
memory/
memory/archive/
memory/archive/completed_tasks.md
memory/archive/error_log.md
memory/archive/behavior_log.md
```

**Step 3** - Create status.txt in workspace root:
```
IDLE | no active job | [timestamp]
```

**Step 4** - Add ## Active Job to MEMORY.md:
```markdown
## Active Job
Status: IDLE
Last completed: none
Next: awaiting instructions
```

**Step 5** - Set up TWO cron jobs in OpenClaw:

Cron Job 1 - Daily wake-up:
```
Name: Vault Boot
Schedule: Every 24 hours
Session: Isolated
Prompt: (see VAULT.md for Vault Boot prompt)
Enable at the exact time you want it to fire daily
```

Cron Job 2 - Hourly backup checkpoint:
```
Name: Memory Save
Schedule: Every 1 hour
Session: Isolated
Prompt:
Read your memory and find any active job. Save your
current known status to memory RIGHT NOW using this
exact format:
ACTIVE JOB: [task] | Brand: [brand] | Progress: [X/total]
| Last filename: [last filename] | Next step: [next step]
| Timestamp: [current time]
If there is no active job save:
NO ACTIVE JOB - Last checkpoint: [current time]
Save to memory. After saving run:
git add -A && git commit -m 'hourly checkpoint [time]' && git push
Then stop. Do nothing else. Background checkpoint only.
```

**Step 6** (recommended) - Set up GitHub auto-push:
```bash
git add -A && git commit -m "[timestamp] vault save" && git push
```

---

## The Agent Co-Designed This System

One of the most interesting outcomes of building Vault was that Kodi himself suggested most of the improvements:

- status.txt (tiny one-line file for zero-cost boot)
- Error capture hook (log before fixing)
- Session duration tracking
- Smart context alerts at image 15 and 20
- Minimum viable boot (load only status.txt if context high)
- Evening wrap-up (save even if session did not finish cleanly)
- Weekly Monday health report
- Watchdog monitoring via git log

The human designed the architecture. The agent optimized it. That collaboration is documented in BEHAVIOR_LOG.md.

---

## Real World Results

After 2 weeks running Vault in production:

- 6+ coloring books published live on Amazon
- Context resets recoverable in seconds via status.txt
- Zero lost work sessions since Vault installed
- Behavior log actively preventing repeated mistakes
- Full session continuity across MiniMax, Claude, and GPT models
- Agent self-aware enough to diagnose and report its own memory gaps

---

## File Structure

```
heyron-vault/
  README.md          - You are here
  VAULT.md           - The installable skill (generic, any agent)
  VAULT_KODI.md      - Real world implementation (KDP pipeline)
  BEHAVIOR_LOG.md    - Real behavior log from 2 weeks of production
  LICENSE            - MIT
```

---

## License

MIT - free to use, modify, and distribute.

---

*Built by @artbykodic for Heyron Agent Jam #1*
*Tested in production on a live AI publishing business*
