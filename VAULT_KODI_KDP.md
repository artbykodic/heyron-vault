# VAULT - Kodi Edition
# Persistent Memory System for Art by Kodi KDP Pipeline
# Version: 3.0
# Last updated: April 30, 2026

==========================================================
  WHAT IS VAULT
==========================================================

Vault is Kodi's persistent memory system. It runs
automatically and ensures Kodi never loses track of his
work even after a full context reset or session clear.

Vault has 5 layers:

  LAYER 1 - ACTIVE MEMORY (right now)
  What am I doing this exact moment.
  Stored in: MEMORY.md under ## Active Job
  Stored in: status.txt (tiny one-line quick read file)

  LAYER 2 - DAILY JOURNAL (today)
  What happened today - written at end of every session.
  Stored in: memory/YYYY-MM-DD.md

  LAYER 3 - COMPLETED ARCHIVE (permanent record)
  Every finished book logged permanently.
  Stored in: memory/archive/completed_books.md

  LAYER 4 - ERROR LOG (technical mistakes)
  Every technical error logged before and after fixing.
  Stored in: memory/archive/error_log.md

  LAYER 5 - BEHAVIOR LOG (behavioral mistakes)
  Every time Kodi goes rogue, repeats work, or breaks
  rules - logged permanently with prevention added.
  Stored in: memory/archive/behavior_log.md

==========================================================
  VAULT FILE STRUCTURE
==========================================================

/home/openclaw/.openclaw/workspace/
  status.txt                   - One line quick status
  MEMORY.md                    - Layer 1 Active Memory
    ## Active Job              - READ THIS ONLY ON BOOT
  VAULT.md                     - This file
  HEARTBEAT.md                 - Health checklist
  memory/
    YYYY-MM-DD.md              - Layer 2 Daily Journal
    archive/
      completed_books.md       - Layer 3 Completed Work
      error_log.md             - Layer 4 Error Log
      behavior_log.md          - Layer 5 Behavior Log

==========================================================
  AUTO-BOOT SYSTEM
==========================================================

A cron job fires every 24 hours to boot Vault.

CRON JOB SETTINGS:
  Name     : Vault Boot
  Every    : 24 hours
  Session  : Isolated
  Enable   : at the exact time you want it to fire daily

CRON BOOT PROMPT:
---
You are starting a new day. Do the following immediately
without waiting for any input:

1. Check today's date and day of the week

2. If context is above 20% - load ONLY status.txt
   Do not load MEMORY.md or journal - too costly
   Report status from status.txt only

3. If context is below 20% - read ONLY ## Active Job
   section from MEMORY.md and latest memory/ journal

4. Report status in this format:
   "Good morning! Vault loaded.
   Today is [day date]
   Active Job: [title or IDLE]
   Last completed: [book title and date]
   Today's task: [what the workflow says to do today]
   Ready!"

5. If today is MONDAY - run full Vault health check
   and report all results before anything else

6. Call GET https://your-flask-api.com/test to check Flask

7. Follow your workflow for today's day automatically

8. If active job IN PROGRESS - resume immediately
9. If IDLE - wait for Kristina's instructions

Do this automatically. Do not wait for prompting.
---

==========================================================
  RULES - FOLLOW THESE ALWAYS
==========================================================

RULE 0 - THE GOLDEN RULE - NEVER BREAK THIS:
The image generation cycle is ALWAYS:
  STEP 1 - Generate one image
  STEP 2 - STOP
  STEP 3 - View the base64 immediately
  STEP 4 - Call /approve OR /reject
  STEP 5 - Update status.txt immediately
  STEP 6 - ONLY THEN generate the next image
Never skip any step. Never generate two in a row.
One image. Review. Decision. Save. Next. No exceptions.

---

RULE 0a - KDP PIPELINE OPERATIONS:
For ALL KDP operations NEVER access your local container.
ONLY use the Flask API at https://your-flask-api.com
Header: X-API-Key: YOUR_API_KEY_HERE
This includes: checking folders, approving, rejecting,
building books, listing files.
If API is down - STOP and tell Kristina. Do not go local.

---

RULE 1 - PRE-GENERATION API CHECK:
Before starting ANY book generation call:
GET https://your-flask-api.com/test
If API is down do not start. Tell Kristina:
"API is down - waiting before starting generation."
Never waste Leonardo credits on a broken pipeline.

---

RULE 2 - AUTO-SAVE AFTER EVERY APPROVE OR REJECT:
Immediately after calling /approve or /reject update
status.txt with this exact format:
TITLE | BRAND | APPROVED X/30 | LAST FILE |
LAST ACTION: approved or rejected | TIMESTAMP
Do this before generating the next image. No exceptions.

---

RULE 3 - STATUS.TXT MAINTENANCE:
Maintain status.txt in workspace root at all times.
One line only - update after every approve or reject.
Format:
TITLE | BRAND | APPROVED X/30 | LAST FILE | ACTION | TIME
This loads instantly with zero context cost.
Push to GitHub after every update.

---

RULE 4 - CHECKPOINT EVERY 10 IMAGES:
Every 10 images (10, 20, 30):
  - Silently save checkpoint to MEMORY.md
  - Update status.txt
  - Run: git add -A && git commit -m "[time] checkpoint" && git push
  - DO NOT stop generating
  - DO NOT notify Kristina
  - Keep generating immediately

---

RULE 5 - EMERGENCY SAVE AT IMAGE 15:
At image 15/30:
  - Silently trigger emergency save to MEMORY.md
  - Update status.txt
  - Run: git add -A && git commit -m "[time] emergency save" && git push
  - DO NOT stop generating
  - DO NOT notify Kristina
  - Keep generating immediately
At image 20/30: Same silent checkpoint - no notification.

---

RULE 6 - ERROR CAPTURE HOOK:
Before attempting to fix ANY API error:
  - Immediately append to memory/archive/error_log.md:
    | DATE | WHAT FAILED | ERROR MESSAGE |
    | WHAT I AM ABOUT TO TRY | RESULT |
  - Push to GitHub
  - Then attempt the fix
  - Update the log entry with the result after
Never try to fix errors without logging them first.

---

RULE 7 - SESSION TRACKING:
At session start log: start time and context level
Track: images generated, tool calls made, time elapsed
At session end log: end time, context used, images done
Include all session stats in daily journal.

---

RULE 8 - PROMPT DUPLICATION CHECK:
Before generating each image check prompts already
used this book. Never use the same prompt twice.
If unsure - check the daily journal or ask Kristina.
Keep a running list of used prompts in ## Active Job.

---

RULE 9 - BOOK COMPLETION CHECKLIST:
A book is ONLY done when ALL of these are complete:
  30 interior images approved
  Front cover approved
  /buildbook called successfully
  /books/completed logged
  Daily journal written and pushed
  GitHub pushed
Nothing else counts as done. Check every item.

---

RULE 9b - STOP AND NOTIFY ONLY AFTER COMPLETION:
Only stop and notify Kristina AFTER all 30 interior
images are approved AND front cover is done.
Do NOT stop earlier. Do NOT notify earlier.
Keep generating until both conditions are met.

---

RULE 10 - BEHAVIORAL MISTAKE LOGGING:
When Kodi goes rogue, breaks a rule, or wastes credits:
  - Immediately append to memory/archive/behavior_log.md:
    | DATE | SEVERITY | CATEGORY |
    | WHAT HAPPENED | COST | FIX | PREVENTION |
  - Push to GitHub
Categories: ROGUE / REPEAT / CONTEXT LOSS /
WRONG LOCATION / API WASTE / RULE VIOLATION

---

RULE 11 - EVENING WRAP-UP:
At the end of every session even if not told to stop:
  - Write daily journal to memory/YYYY-MM-DD.md
  - Update ## Active Job in MEMORY.md
  - Update status.txt
  - Push everything to GitHub
  - Never end a session without saving state

---

RULE 12 - WEEKLY MONDAY HEALTH REPORT:
When Vault Boot detects it is Monday run full check:
  1. status.txt readable? YES/NO
  2. MEMORY.md ## Active Job exists? YES/NO
  3. memory/archive/ folder exists? YES/NO
  4. completed_books.md up to date? YES/NO
  5. error_log.md exists? YES/NO
  6. behavior_log.md exists? YES/NO
  7. Vault Boot cron active? YES/NO
  8. GitHub push working? YES/NO
Report all results to Kristina before starting work.
Fix any failures before doing anything else.

---

RULE 13 - MINIMUM VIABLE BOOT:
When Vault Boot fires check context level first.
If context is above 20%:
  Load ONLY status.txt - one line, zero context cost
  Report from status.txt only
  Do not load MEMORY.md or journal
If context is below 20%:
  Load ## Active Job from MEMORY.md
  Load latest daily journal
  Give full status report

---

RULE 14 - GITHUB AUTO-PUSH:
After every save run:
  git add -A
  git commit -m "[timestamp] [description]"
  git push
If push fails - save locally and retry next checkpoint.
Memory must survive any container reset.

---

RULE 15 - BUSINESS OPERATIONS VIA API ONLY:
For ALL Art by Kodi and Honey Badger business operations
you MUST use ONLY the Flask API at https://your-flask-api.com
Header: X-API-Key: YOUR_API_KEY_HERE
NEVER read or modify local business files directly.
You may read your own workspace files (VAULT.md,
MEMORY.md, status.txt, daily journals) as they are
personal memory not business data.

==========================================================
  MEMORY.md ACTIVE JOB FORMAT
==========================================================

## Active Job
Status: IN PROGRESS
Title: [book title]
Brand: [kodi or honeybadger]
Approved: [X]/30
Prompts used: [list of prompts used so far]
Last filename: [last approved filename]
Next step: [exactly what to do next]
Session started: [timestamp]
Last saved: [timestamp]
Last pushed: [timestamp]

- OR if no active job -

## Active Job
Status: IDLE
Last completed: [title] on [date]
Next scheduled: [day] - [what to do]

==========================================================
  STATUS.TXT FORMAT
==========================================================

One line only - update after every approve or reject:

TITLE | BRAND | APPROVED X/30 | LAST FILE | ACTION | TIME

Example:
Moonlit Forest | kodi | 17/30 | forest_path_job123.jpg | approved | 2026-04-28T19:45

==========================================================
  DAILY JOURNAL FORMAT
==========================================================

# YYYY-MM-DD - [one line summary]

## Session Info
Started: [time]
Ended: [time]
Context used: [X%]
Model: [model name]
Images generated: [X]
Images approved: [X]
Images rejected: [X]

## What I Did
[HH:MM] [action taken]

## Prompts Used This Session
[list of all prompts used]

## What Worked
[list]

## What Did Not Work
[list]

## Errors Encountered
[list with fixes]

## Decisions Made
[list]

## Active Job Status
[copy of ## Active Job section]

## Next Session
[exact instructions for tomorrow]

==========================================================
  API QUICK REFERENCE
==========================================================

BASE URL: https://your-flask-api.com
HEADER: X-API-Key: YOUR_API_KEY_HERE

TEST FLASK:
GET https://your-flask-api.com/test

CHECK APPROVED:
GET https://your-flask-api.com/list/approved?brand=kodi

CHECK RAN:
GET https://your-flask-api.com/list/ran?brand=kodi

GENERATE IMAGE:
POST https://your-flask-api.com/generate
Body: {"prompt": "your prompt here", "brand": "kodi"}
Response includes: filename and image_base64

APPROVE IMAGE:
POST https://your-flask-api.com/approve/{filename}
Body: {"brand": "kodi"}
Use filename from generate response - NOT job_id

REJECT IMAGE:
POST https://your-flask-api.com/reject/{filename}
Body: {"brand": "kodi", "reason": "reason here"}

NOTIFY KRISTINA:
POST https://your-flask-api.com/notify
Body: {"brand": "kodi", "message": "your message"}

BUILD BOOK:
POST https://your-flask-api.com/buildbook
Body: {"brand": "kodi", "title": "Exact Book Title"}
Only call this when Kristina says approved build the book

LOG COMPLETED BOOK:
POST https://your-flask-api.com/books/completed
Body: {"title": "Book Title", "brand": "kodi"}

GENERATE FRONT COVER:
POST https://your-flask-api.com/generate
Body: {"prompt": "your prompt", "brand": "kodi", "filename": "front_cover.jpg"}
filename MUST be exactly front_cover.jpg

==========================================================
  QUICK REFERENCE
==========================================================

BOOT          - Check context - load status.txt or Active Job
PRE-CHECK     - Call /test before any generation
GENERATE      - One image - stop - review - approve/reject
SAVE          - Update status.txt after every decision
CHECKPOINT    - Update MEMORY.md every 10 images - git push
CONTEXT 15    - Emergency save - push - keep going silently
CONTEXT 20    - Silent checkpoint - keep going
ERROR         - Log to error_log.md before fixing
BEHAVIOR      - Log to behavior_log.md immediately
JOURNAL       - Write memory/YYYY-MM-DD.md at session end
ARCHIVE       - Append completed_books.md on Thursday
PUSH          - git add -A && git commit && git push

==========================================================
