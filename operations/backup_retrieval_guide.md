# CHL Backup Retrieval Guide

**Purpose:** Step-by-step recovery instructions for the backup locations operator does NOT see day-to-day. C: and D: locations are skipped per operator request — operator knows those.

**When you'd use this:** D: drive fails / corrupts, AND you need to rebuild from cloud-side copies.

---

## Location 1 — GitHub `Continental-Haul-Logistics` (CHL platform code)

**URL:** `https://github.com/563352715/Continental-Haul-Logistics`

**What it has:**
- ALL backend code (`backend/*.py`)
- ALL frontend code (`frontend/src/*.js`, `*.jsx`)
- ALL scripts (`scripts/*.py`, `*.ps1`)
- Memory canonical at `memory/` (the gitignored exception varies — most CAPABILITIES.md / handoff_*.md ARE there)
- Test files (`backend/tests/*.py`)
- Frontend assets (`frontend/public/*`)
- Configuration files (`craco.config.js`, `requirements.txt`, etc.)

**What it does NOT have:**
- `.env` files (gitignored — secrets)
- MongoDB data
- Claude session memory at `~/.claude/projects/c--CHL/memory/`
- API key files (`.tmp_key.txt`, `*API*KEY*.txt`)
- Health probe artifacts (`health.json`)
- Local logs (`logs/`)

### How to retrieve from a fresh machine

**Prerequisites you need installed first:**
1. Git for Windows — https://git-scm.com/download/win (one-click installer, run installer)
2. Python 3.14 — https://www.python.org/downloads/release/python-3140/ (Windows installer, check "Add to PATH")
3. Node.js LTS — https://nodejs.org/en/download (Windows installer)
4. MongoDB Community Server — https://www.mongodb.com/try/download/community (Windows MSI; install as service)

**Recovery steps (assuming the new box has nothing):**

```powershell
# 1. Clone the repo to where CHL needs to live
cd C:\
git clone https://github.com/563352715/Continental-Haul-Logistics.git CHL

# 2. Move into the project
cd C:\CHL

# 3. Install backend Python dependencies
cd backend
pip install -r requirements.txt
cd ..

# 4. Install frontend dependencies
cd frontend
npm install
cd ..

# 5. Restore secrets — REQUIRES THE .env FILE FROM YOUR LAST FULL BACKUP
# The .env is in any of the D:\CHL_backups\<iter_X>_close_*\env_files\ dirs.
# If D: is gone too, you must rebuild .env by hand (operator has the keys
# in personal records / password manager).
Copy-Item -Path "<recovered .env source>" -Destination "C:\CHL\backend\.env"

# 6. Restore the chl-memory submodule
cd C:\CHL
git clone https://github.com/563352715/chl-memory.git chl-memory

# 7. Start MongoDB (it should auto-start as a service after install)
# Verify with: Get-Service MongoDB

# 8. Restore Mongo from your last available D-drive mongo_export
# (the per-collection JSON dumps live at:)
#   D:\CHL_backups\<iter>_close_*\mongo_export\<collection>.json
# Manual restore script: scripts/restore_mongo_from_json.py (operator-side)

# 9. Start the backend service (NSSM)
# If NSSM service config was saved in a backup, restore it.
# Otherwise re-create per scripts/install_nssm_service.ps1
```

**What you cannot recover from GitHub alone:**
- Your `.env` (must come from D-drive iter-close backup OR be rebuilt by hand from operator's personal secret store)
- MongoDB data (must come from D-drive `mongo_export/` JSONs)
- The Claude session memory dir (~/.claude/projects/c--CHL/memory/)

---

## Location 2 — GitHub `chl-memory` (memory + research mirror)

**URL:** `https://github.com/563352715/chl-memory`

**What it has:**
- All research docs (`research/*.md`)
- All operations docs (`operations/*.md` — including this file)
- Progress tracking (`progress/current_status.md`, `NORTH_STAR_BUILD_PLAN.md`, iter close docs)
- Test run reports (`test_runs/*.md`)
- Strategic decisions in markdown form

**How to retrieve:**

```powershell
cd C:\CHL
git clone https://github.com/563352715/chl-memory.git chl-memory
```

Same `git clone` pattern. No build steps required — it's all markdown.

---

## Location 3 — Cloud backup (NOT YET CONFIGURED)

**Status:** Operator has not yet provisioned a cloud backup account beyond GitHub.

**To set up (operator-side, one-time):**

### Option A — Backblaze B2 (~$2/month at our data size)

1. Sign up at https://www.backblaze.com/cloud-storage
2. Create a bucket (private, default settings)
3. Generate an Application Key
4. Provide the Key ID + Secret to dev (use `scripts/set_env_var.ps1` — never paste in chat)
5. Dev wires up nightly push of the iter-close backup to B2

**Retrieval from B2:**
```powershell
# Download via b2 CLI (free tool from Backblaze)
b2 download-file-by-name <bucket-name> <file-key> <local-path>
```

### Option B — AWS S3 Glacier Deep Archive (~$1/month)

1. Sign up at https://aws.amazon.com/
2. Create an IAM user with S3 + Glacier permissions
3. Create a bucket configured for Glacier Deep Archive
4. Provide AWS_ACCESS_KEY_ID + AWS_SECRET_ACCESS_KEY (via set_env_var.ps1)
5. Dev wires up nightly push

**Retrieval from S3 Glacier:** SLOW — Deep Archive takes 12-48 hours to retrieve. NOT for emergency recovery; only for "we lost everything else and need history."

### Option C — Google Drive / Dropbox / OneDrive (consumer cloud)

1. Sign up if you don't have an account (most people already have one of these)
2. Install the desktop sync client
3. Designate a folder (e.g. `D:\CloudSync\CHL_backups\`)
4. Sync runs automatically; just point one of the D: backup operations to write into that folder occasionally
5. **Retrieval:** open the cloud's web UI, navigate to the folder, download

**Note:** Consumer cloud is convenient but typically capped at 15-100 GB free / $10-15/mo for terabytes. Backblaze B2 is the cheapest for backup-tier storage.

---

## Location 4 — Off-machine physical (USB / external drive — NOT YET CONFIGURED)

**Status:** No external drive currently plugged in for ongoing backup.

**Recommended setup:**
1. Buy a USB stick (32-64 GB, $10-20 — Samsung BAR Plus or SanDisk Ultra Fit recommended for portability/durability)
2. Plug into a free USB-A port on this box
3. Tell dev which drive letter Windows assigned (typically E: or F:)
4. Dev configures `backup_to_d.ps1` to ALSO mirror to that drive after each run

**Retrieval:** physical — plug the USB into any computer, files are visible. Bundle restore steps same as D: drive recovery.

---

## What's currently saved + where (snapshot summary)

After tonight's 4-copy creation completes, the platform's full backup landscape will be:

| Copy # | Location | Type | Currency | Operator-known? |
|---|---|---|---|---|
| 1 | C:\ (live) | full | NOW | ✓ |
| 2 | D:\CHL_backups\5copy_floor_a_<ts>\ | full | tonight | ✓ |
| 3 | D:\CHL_backups\5copy_floor_b_<ts>\ | full | tonight | ✓ |
| 4 | D:\CHL_backups\5copy_floor_c_<ts>\ | full | tonight | ✓ |
| 5 | D:\CHL_backups\5copy_floor_d_<ts>\ | full | tonight | ✓ |
| (partial 1) | github.com/563352715/Continental-Haul-Logistics | code only | NOW (each push) | now retrievable per Location 1 above |
| (partial 2) | github.com/563352715/chl-memory | memory only | NOW | now retrievable per Location 2 above |
| (partial 3) | D:\CHL_backups\rolling\ | bundles + memory only, no DB/env | minutes-fresh | ✓ |
| (historical 1-3) | D:\CHL_backups\iter_*_close_* | full at that iter | days-stale | ✓ |
| (safety) | D:\CHL_backups\pre_purge_2026_05_09_full_* | mongo dump only | 2026-05-09 12:46 | ✓ |

## Maintenance going forward

**Standing rule (operator-mandated 2026-05-10):** maintain a floor of 5 current full copies.

**How that's enforced:**
- `snapshot_to_d.ps1` (lightweight rolling, runs after every git push) ALSO checks: is the most-recent `5copy_floor_*` backup older than 24h? If yes, fire `backup_to_d.ps1` to refresh one slot.
- 5-copy slots rotate (`5copy_floor_a` → `b` → `c` → `d` → `a` → ...) so the oldest of the 5 always gets refreshed first
- Historical iter-close backups are NEVER overwritten — they remain immutable point-in-time recoveries

**Verification:** the operator can confirm the 5-copy floor at any time by running:
```powershell
Get-ChildItem D:\CHL_backups\5copy_floor_* -Directory | Sort CreationTime
```
Expected: 4 dirs visible (the C: live is the 5th of the 5).

---

## TL;DR for emergency recovery

If C: drive dies AND D: drive dies AND no off-machine backup exists:

1. **Clone CHL from GitHub** (steps in Location 1 above) — recovers code + most memory
2. **Clone chl-memory from GitHub** (Location 2) — recovers research + ops docs
3. **Manually rebuild .env** from operator's personal records — there is NO cloud backup of secrets currently
4. **MongoDB data is LOST** (no cloud backup currently) — start with empty database, the platform will rebuild operational state from carrier-vetting + scrape over days/weeks

**To prevent this scenario:** set up Location 3 (cloud) OR Location 4 (USB) so secrets + DB get backed up off-machine.
