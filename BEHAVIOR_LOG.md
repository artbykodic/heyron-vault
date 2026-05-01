# BEHAVIOR LOG - Kodi
# Tracks when Kodi goes off script, loses context, or makes behavioral mistakes
# Every entry includes what happened, how it was fixed, and what prevention was added
# This log is how Kodi gets better every week
# Last updated: April 30, 2026

==========================================================
  PURPOSE
==========================================================

This log exists because AI agents make behavioral mistakes.
Not technical errors - behavioral ones. Going rogue.
Repeating work. Ignoring instructions. Creating unauthorized
jobs. Losing the plot mid-task.

Every mistake logged here:
1. Creates a permanent record
2. Gets a fix applied immediately
3. Gets a prevention rule added to the workflow
4. Never happens again

==========================================================
  LOG ENTRIES
==========================================================

DATE: 2026-04-14
SEVERITY: HIGH
CATEGORY: ROGUE
WHAT HAPPENED: Created his own cron jobs autonomously
without authorization. Ran weekend pipeline planning
without being asked.
COST: ~$2 in API credits wasted
HOW FIXED: Deleted all unauthorized cron jobs manually
PREVENTION: Added Rule - NEVER create your own cron jobs
STATUS: RESOLVED

---

DATE: 2026-04-14
SEVERITY: HIGH
CATEGORY: WRONG LOCATION
WHAT HAPPENED: Built interior PDF on his own server and
saved to /root/.openclaw/workspace/output_books/ instead
of using Flask API
COST: PDF was wrong format, wrong DPI, unusable for KDP
HOW FIXED: Told Kodi to use /buildbook endpoint only
PREVENTION: Rule 0a - KDP API only, never local container
STATUS: RESOLVED

---

DATE: 2026-04-14
SEVERITY: MEDIUM
CATEGORY: CONTEXT LOSS
WHAT HAPPENED: Lost track of approve/reject endpoints
mid-session. Said he could not find them.
COST: Generation session interrupted, manual restart needed
HOW FIXED: Restarted Flask and reminded Kodi of endpoints
PREVENTION: API Quick Reference added to VAULT.md
STATUS: RESOLVED

---

DATE: 2026-04-15
SEVERITY: HIGH
CATEGORY: REPEAT
WHAT HAPPENED: Generated the same prompt over and over
for Art by Honey Badger. 10+ identical images.
COST: ~$3 in wasted Leonardo API credits
HOW FIXED: Manually stopped Kodi, cleared ran folder
PREVENTION: Rule 8 - Prompt duplication check before
every generation. Keep running list in Active Job.
STATUS: RESOLVED

---

DATE: 2026-04-15
SEVERITY: MEDIUM
CATEGORY: CONTEXT LOSS
WHAT HAPPENED: Forgot workflow completely mid-session.
Said he could not find approve/reject endpoints and
thought Kristina reviewed images manually.
COST: Full session restart needed
HOW FIXED: Pasted full workflow into chat to re-orient
PREVENTION: Vault installed. Workflow hardwired into
MEMORY.md. API Quick Reference in VAULT.md.
STATUS: RESOLVED

---

DATE: 2026-04-27
SEVERITY: LOW
CATEGORY: ROGUE
WHAT HAPPENED: Ran own weekend pipeline planning via old
cron job without being asked. Used openai/gpt-oss-120b
via openrouter without authorization.
COST: ~$0.50 in credits
HOW FIXED: Deleted remaining cron jobs
PREVENTION: Rule reinforced. Behavior log created.
STATUS: RESOLVED

---

DATE: 2026-04-28
SEVERITY: MEDIUM
CATEGORY: WRONG LOCATION
WHAT HAPPENED: When told to stop looking in his container,
suggested deleting VAULT.md and disabling the boot cron
as a solution - completely backwards.
COST: No actual damage but showed confusion about what
his workspace files vs business files are
HOW FIXED: Explained the distinction clearly
PREVENTION: Rule 15 added - business ops via API only,
personal memory files (VAULT, MEMORY, status.txt,
journals) are fine to access locally
STATUS: RESOLVED

---

DATE: 2026-04-28
SEVERITY: MEDIUM
CATEGORY: API WASTE
WHAT HAPPENED: Tried to call non-existent /base64/filename
endpoint to fetch images instead of using image_base64
already in the generate response.
COST: Multiple 404 errors, session disruption
HOW FIXED: Reminded Kodi that base64 comes in generate
response automatically - no separate fetch needed
PREVENTION: API Quick Reference updated with clear note:
"Use filename from generate response - NOT job_id"
STATUS: RESOLVED

---

DATE: 2026-04-28
SEVERITY: LOW
CATEGORY: CONTEXT LOSS
WHAT HAPPENED: At 100% context forgot the generate
endpoint details entirely during active generation.
COST: Session disrupted, had to re-provide endpoint info
HOW FIXED: Provided endpoint details directly in chat
PREVENTION: API Quick Reference now in VAULT.md so it
survives any context reset or compaction
STATUS: RESOLVED

==========================================================
  BEHAVIOR PATTERNS
==========================================================

MOST COMMON MISTAKES:
1. Context loss mid-generation (4 incidents)
2. Using wrong location for business operations (2 incidents)
3. Unauthorized autonomous actions (2 incidents)
4. Prompt/API repetition (2 incidents)

TOTAL API CREDITS WASTED: ~$5.50
TOTAL INCIDENTS: 9
RESOLVED: 9
OPEN: 0

HIGH SEVERITY: 2
MEDIUM SEVERITY: 5
LOW SEVERITY: 2

==========================================================
  PREVENTION RULES ADDED FROM THIS LOG
==========================================================

Rule 0a  - KDP operations via API only, never local
Rule 8   - Prompt duplication check before every image
Rule 14  - GitHub auto-push after every save
Rule 15  - Business ops via API only
VAULT    - Installed to prevent context loss
API Ref  - Added to VAULT.md for context reset recovery
status.txt - Added for zero-cost boot and recovery

==========================================================
  HOW TO USE THIS LOG
==========================================================

KRISTINA adds an entry whenever Kodi:
- Goes rogue or does something unauthorized
- Repeats work already done
- Loses context and forgets his task
- Creates unauthorized cron jobs or tasks
- Saves files in the wrong place
- Ignores workflow rules
- Wastes API credits on bad behavior
- Does anything requiring manual intervention

KODI adds an entry whenever he:
- Catches himself about to break a rule
- Recovers from a context reset
- Makes any error requiring correction

Format for new entries:
DATE: YYYY-MM-DD
SEVERITY: LOW / MEDIUM / HIGH
CATEGORY: ROGUE / REPEAT / CONTEXT LOSS / WRONG LOCATION /
          API WASTE / RULE VIOLATION / COMMUNICATION
WHAT HAPPENED: [clear description]
COST: [credits wasted / time lost / work redone]
HOW FIXED: [exactly what was done]
PREVENTION: [rule or change made]
STATUS: OPEN / RESOLVED

==========================================================
