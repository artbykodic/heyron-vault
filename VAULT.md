# VAULT
# Persistent Memory System for Heyron Agents
# Version: 3.0
# License: MIT
# Author: artbykodic
# Built for: Heyron Agent Jam #1 - Memory Systems

==========================================================
  WHAT IS VAULT
==========================================================

Vault gives any Heyron agent persistent memory across
sessions, context resets, and model switches. Drop it
into any agent and it will never lose its place again.

Vault was built and battle tested on a real production
agent running an automated KDP publishing business -
generating coloring books, managing approvals, and
publishing to Amazon. It works.

==========================================================
  THE 5 LAYERS
==========================================================

LAYER 1 - ACTIVE MEMORY
  What the agent is doing RIGHT NOW.
  Updated after every action automatically.
  Two formats: full MEMORY.md and tiny status.txt
  status.txt = one line, loads with zero context cost

LAYER 2 - DAILY JOURNAL
  What happened today - written at session end.
  Timestamped activity log, decisions, outcomes.
  Stored in: memory/YYYY-MM-DD.md

LAYER 3 - COMPLETED ARCHIVE
  Permanent record of finished work.
  Never repeats completed tasks.
  Stored in: memory/archive/completed_tasks.md

LAYER 4 - ERROR LOG
  Every technical mistake logged before and after fixing.
  Agent learns from failures automatically.
  Stored in: memory/archive/error_log.md

LAYER 5 - BEHAVIOR LOG
  Every behavioral mistake logged permanently.
  Going rogue, repeating work, ignoring rules.
  Tracks patterns, adds prevention rules.
  Stored in: memory/archive/behavior_log.md

==========================================================
  THE CONTEXT PROBLEM AND HOW VAULT SOLVES IT
==========================================================

Reading all memory files on boot fills context instantly.
We discovered this the hard way - loading MEMORY.md +
SOUL.md + VAULT.md on startup hit 100% context before
the agent even started working.

VAULT SOLUTION - TWO TIER BOOT:

Tier 1 (context above 20%): Load ONLY status.txt
  - One line file
  - Zero context cost
  - Agent knows exactly where it is instantly

Tier 2 (context below 20%): Load ## Active Job section
  - Just the Active Job section of MEMORY.md
  - Not the whole file
  - Gives full context for resuming work

This keeps boot lean while giving the agent everything
it needs to resume work immediately.

==========================================================
  THE AUTO-BOOT DISCOVERY
==========================================================

IMPORTANT FOR THE HEYRON COMMUNITY:

Adding boot instructions to SOUL.md does NOT reliably
trigger auto-boot. The agent loads SOUL.md but still
waits for the first user message before acting.

THE FIX: Use a cron job as the boot trigger.

A scheduled isolated cron job fires automatically and
sends the wake-up prompt without any user interaction.
This is the only reliable way to achieve true
zero-prompting auto-boot in Heyron/OpenClaw.

TWO CRON PROMPTS:

VAULT BOOT PROMPT (daily wake-up):
---
You are starting a new day. Do the following immediately
without waiting for any input:

1. Check today's date
2. If context above 20%: load ONLY status.txt and report
3. If context below 20%: load ## Active Job from MEMORY.md
   and latest daily journal
4. Report status:
   "Good morning! Vault loaded.
   Active Job: [task or IDLE]
   Last completed: [task and date]
   Today's task: [what to do today]
   Ready!"
5. Resume active job or wait for instructions

Do this automatically. Do not wait for prompting.
---

MEMORY SAVE PROMPT (hourly backup):
---
Read your memory and find any active job. Save your
current known status to memory RIGHT NOW using this
exact format:
ACTIVE JOB: [book title] | Brand: [kodi or honeybadger]
| Approved: [X]/30 | Last filename: [last filename saved
in memory] | Next step: [next step from memory]
| Timestamp: [current time]
If there is no active job save:
NO ACTIVE JOB - Last checkpoint: [current time]
Save to memory. After saving run:
git add -A && git commit -m 'hourly checkpoint [time]' && git push
Then stop. Do nothing else. Do not generate images.
Do not start new tasks. Background checkpoint only.
---

==========================================================
  INSTALLATION - 5 STEPS
==========================================================

STEP 1 - Copy VAULT.md to your agent's workspace

STEP 2 - Create the folder structure:
  memory/
  memory/archive/
  memory/archive/completed_tasks.md
  memory/archive/error_log.md
  memory/archive/behavior_log.md

STEP 3 - Create status.txt in workspace root:
  IDLE | no active job | [timestamp]

STEP 4 - Add ## Active Job to MEMORY.md:
  ## Active Job
  Status: IDLE
  Last completed: none
  Next: awaiting instructions
  Last saved: [timestamp]

STEP 5 - Set up TWO cron jobs:

  CRON JOB 1 - Vault Boot (daily wake-up):
  Name: Vault Boot
  Schedule: Every 24 hours
  Session: Isolated
  Prompt: (use CRON BOOT PROMPT above)
  Enable at the exact time you want it to fire daily
  Purpose: Morning status report and task resumption

  CRON JOB 2 - Memory Save (hourly backup):
  Name: Memory Save
  Schedule: Every 1 hour
  Session: Isolated
  Prompt: Read your memory and find any active job.
    Save your current known status to memory RIGHT NOW:
    ACTIVE JOB: [task] | Progress: [X/total] |
    Last checkpoint: [description] | Timestamp: [time]
    If no active job save: NO ACTIVE JOB - [timestamp]
    Save to memory and do nothing else.
  Purpose: Hourly backup checkpoint in case agent
    saves fail during active work sessions

OPTIONAL BUT RECOMMENDED:
  Set up GitHub auto-push so memory survives
  container resets:
  git add -A && git commit -m "[timestamp]" && git push

==========================================================
  CORE RULES - ADD TO AGENT SYSTEM PROMPT
==========================================================

---
VAULT MEMORY SYSTEM - ALWAYS ACTIVE

BOOT: Check context level first.
  Above 20%: load ONLY status.txt - report from it only
  Below 20%: load ## Active Job - give full report

AFTER EVERY ACTION: Update status.txt immediately:
  TASK | PROGRESS | LAST ACTION | TIMESTAMP

EVERY 10 ACTIONS: Silent checkpoint:
  - Update ## Active Job in MEMORY.md
  - Update status.txt
  - git add -A && git commit -m "checkpoint" && git push
  - DO NOT stop working
  - DO NOT notify user

EMERGENCY SAVE at 50% through task: Same as checkpoint
but with commit message "emergency save"

SESSION END: Write daily journal to memory/YYYY-MM-DD.md
  Include: what happened, what worked, what did not work,
  next session instructions. Push to GitHub.

COMPLETED TASK: Append to memory/archive/completed_tasks.md
TECHNICAL ERROR: Log to memory/archive/error_log.md BEFORE fixing
BEHAVIORAL MISTAKE: Log to memory/archive/behavior_log.md

CONTEXT RESET: Load status.txt immediately. Resume from
last checkpoint. Notify user of recovery.

NEVER read entire MEMORY.md on boot - Active Job only.
---

==========================================================
  FILE FORMATS
==========================================================

STATUS.TXT (one line):
TASK | PROGRESS | LAST ACTION | TIMESTAMP

MEMORY.md ACTIVE JOB:
## Active Job
Status: IN PROGRESS
Task: [task name]
Progress: [X/total]
Prompts used: [list if applicable]
Last checkpoint: [description]
Next step: [exactly what to do next]
Last saved: [timestamp]
Last pushed: [timestamp]

DAILY JOURNAL (memory/YYYY-MM-DD.md):
# YYYY-MM-DD - [one line summary]
## Session Info
Started/Ended/Context%/Model
## What I Did
[HH:MM] [action]
## What Worked / What Did Not Work
[lists]
## Active Job Status
[copy of Active Job]
## Next Session
[exact instructions]

COMPLETED TASKS (memory/archive/completed_tasks.md):
| Date | Task | Details | Status |
|------|------|---------|--------|
| YYYY-MM-DD | [task] | [details] | DONE |

ERROR LOG (memory/archive/error_log.md):
| Date | Error | Fix | Prevention |
|------|-------|-----|------------|

BEHAVIOR LOG (memory/archive/behavior_log.md):
| Date | Severity | Category | What Happened | Cost | Fix | Prevention |
|------|----------|----------|---------------|------|-----|------------|

==========================================================
  VAULT HEALTH CHECK - Run weekly
==========================================================

1. status.txt exists and readable? YES/NO
2. MEMORY.md ## Active Job section exists? YES/NO
3. memory/archive/ folder exists? YES/NO
4. completed_tasks.md exists? YES/NO
5. error_log.md exists? YES/NO
6. behavior_log.md exists? YES/NO
7. Latest daily journal written? YES/NO
8. Vault Boot cron active? YES/NO
9. GitHub push working? YES/NO

Fix any failures before starting work.

==========================================================
  REAL WORLD PROOF
==========================================================

Vault was built because of a real problem with a real agent.

Kodi is an autonomous AI agent running an automated KDP
coloring book publishing business with two brands
(Art by Kodi and Art by Honey Badger). The project
started when Kodi kept losing his memory mid-session -
forgetting what he was doing, repeating work, and going
completely off-task. Vault was built specifically to
solve those problems.

THE PROBLEM:
- Lost context mid-generation after just 4-5 images
- Repeated the same prompts wasting API credits
- Went completely off-task after context filled
- Created unauthorized scheduled jobs autonomously
- Had to be manually restarted and re-briefed constantly

THE SOLUTION:
- status.txt loads in zero context on every boot
- Checkpoint saves every 10 images silently
- Daily journal written automatically at session end
- Behavior log prevents repeated mistakes
- Cron boot fires daily - zero user prompting needed
- GitHub push ensures memory survives container resets

RESULTS after 2 weeks in production:
- 6+ coloring books published live on Amazon
- Context resets recoverable in seconds
- Agent self-diagnoses and fixes its own memory gaps
- Behavior log actively preventing repeated mistakes
- Full continuity across MiniMax, Claude, GPT models

KEY INSIGHT - THE AGENT CO-DESIGNED THIS SYSTEM:
Kodi suggested most of the improvements himself -
status.txt, error capture hook, session tracking,
smart context alerts, minimum viable boot, evening
wrap-up, weekly health report. The human-agent
collaboration is documented in BEHAVIOR_LOG.md.

==========================================================
  QUICK REFERENCE
==========================================================

BOOT     - Check context - load status.txt or Active Job
SAVE     - Update status.txt after every action
CHECK    - Update MEMORY.md every 10 actions - git push
EMERGENCY- Save at 50% through task - keep going silently
ERROR    - Log to error_log.md before fixing
BEHAVIOR - Log to behavior_log.md immediately
JOURNAL  - Write memory/YYYY-MM-DD.md at session end
ARCHIVE  - Append completed_tasks.md on completion
PUSH     - git add -A && git commit && git push
RECOVER  - Load status.txt - resume from checkpoint

==========================================================
  LICENSE
==========================================================

MIT License - free to use, modify, and distribute.
Copyright (c) 2026 artbykodic

==========================================================
