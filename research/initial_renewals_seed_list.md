# Initial renewals seed list -- operator-driven population

**Iter 145.x Stream KK1 (2026-05-08).** Companion document to the
renewal-calendar platform feature shipped in `backend/renewals/`. The
scanner does NOT auto-seed; this file is the operator's worksheet for
populating `db.renewals` once via `POST /api/_renewals` (or a one-time
script).

The renewal calendar feature exists because the operator runs solo and
a missed BMC-84 bond, lapsed domain, or expired API key has the
potential to collapse the platform overnight. Embedding tracking in the
platform turns operator-attention into a substrate the platform manages
itself. (Operator's exact words 2026-05-08 EOD-16: *"identify all the
expiration dates for everything we need to operate so we are never
plagued by unpaid bills that we did not knw about. We need to build that
intothe platform and setup reminders."*)

---

## How to seed

### Option 1: One-shot via API (recommended)

```bash
curl -X POST http://localhost:8001/api/_renewals \
  -H "Authorization: Bearer $JWT" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "BMC-84 broker bond",
    "category": "regulatory",
    "vendor_or_authority": "Roanoke Insurance Group",
    "expires_at": "2027-03-15T00:00:00Z",
    "renewal_cadence_days": 365,
    "renewal_url": "https://roanoke.com/...",
    "renewal_contact_email": "agent@roanoke.com",
    "auto_renew": false,
    "operator_action_required": true,
    "notes": "Bond #BMC-84-XXXXX -- $75K coverage. Renewal premium ~$900/yr."
  }'
```

### Option 2: Script (recommended for first-time bulk seeding)

`scripts/seed_renewals.py` (Iter 145.x Stream NN3, 2026-05-08 EOD) ships
the Tier 1 + 2 + 3 baseline (34 rows) hard-coded. Idempotent by `name`,
dry-run by default. See "Run instructions" below.

---

## Tier 1 -- regulatory / legal (existential)

Each of these, if missed, has the potential to collapse broker authority
or the LLC entirely. SMS-eligible at 7d/3d/1d.

| Name | Category | Cadence | Vendor / authority | Notes |
|---|---|---|---|---|
| BMC-84 broker bond | regulatory | 365 | Roanoke Insurance Group (or current bond agent) | $75K coverage required. Lapse = automatic loss of broker authority. |
| FMCSA MCS-150 update | regulatory | 730 | FMCSA | Every 24mo. Failure deactivates USDOT. |
| Missouri Secretary of State LLC annual report | regulatory | 365 | Missouri SoS | Lapse = administrative dissolution. |
| FinCEN BOI report | regulatory | 365 | FinCEN | Initial + on-change under Corporate Transparency Act. Re-confirm yearly even if no change. |
| Federal tax filings -- annual 1120/1065 | tax | 365 | IRS | Q1 deadline. |
| Federal estimated tax -- Q1 | tax | 90 | IRS | Apr 15. |
| Federal estimated tax -- Q2 | tax | 90 | IRS | Jun 15. |
| Federal estimated tax -- Q3 | tax | 90 | IRS | Sep 15. |
| Federal estimated tax -- Q4 | tax | 90 | IRS | Jan 15 of following year. |
| Missouri state tax -- annual | tax | 365 | Missouri DoR | |
| Missouri state tax -- quarterly | tax | 90 | Missouri DoR | If applicable to entity type. |
| General liability insurance | insurance | 365 | TBD broker | Annual policy renewal. |
| Cargo insurance | insurance | 365 | TBD broker | Annual. Customers may require COI. |
| Errors & omissions (E&O) insurance | insurance | 365 | TBD broker | Annual. Highly recommended for brokers. |
| A2P 10DLC TCR brand registration | regulatory | 365 | The Campaign Registry | Annual fee. Required for SMS to US recipients via Twilio + Plivo. |
| A2P 10DLC TCR campaign registration | regulatory | 365 | The Campaign Registry | Annual fee per campaign. |

## Tier 2 -- vendor / infrastructure (operational)

Lapse = service interruption but recoverable. SMS-eligible at 3d/1d only.

| Name | Category | Cadence | Vendor | Notes |
|---|---|---|---|---|
| continentalhaul.com domain | vendor | 365 | Domain registrar (Cloudflare? Namecheap?) | Auto-renew: enable if not already. |
| SSL cert (production) | vendor | 90 | Let's Encrypt | Auto-renew via certbot or Cloudflare. Track manually as fallback. |
| Stripe account verification | vendor | 365 | Stripe | Stripe-driven re-checks. |
| Twilio US phone numbers (each DID) | vendor | 30 | Twilio | Monthly subscription. Number released if missed. |
| Telnyx US phone numbers (each DID) | vendor | 30 | Telnyx | Monthly subscription. |
| Plivo US phone numbers (each DID) | vendor | 30 | Plivo | Monthly subscription (per migration to Plivo). |
| Mongo Atlas subscription | vendor | 30 | MongoDB Inc. | Monthly billing -- watch credit balance. |
| Mercury banking subscription | vendor | 365 | Mercury | Free tier or paid. |
| OpenAI API auto-recharge threshold | vendor | 30 | OpenAI | Watch credit balance. Set auto-recharge floor. |
| Anthropic API auto-recharge threshold | vendor | 30 | Anthropic | Watch credit balance. |
| GitHub Pro subscription | vendor | 365 | GitHub | Annual or monthly. |
| VS Code subscription (if applicable) | vendor | 365 | Microsoft | If using paid extensions. |

## Tier 3 -- security (rotation)

Recommended rotation cadence for keys + secrets. Operator-action only --
no auto-rotation in scope. SMS-eligible at 3d/1d.

| Name | Category | Cadence | Vendor | Notes |
|---|---|---|---|---|
| Telnyx API key | security | 365 | Telnyx | Recommend annual rotation. Use scripts/set_env_var.ps1 to provision. |
| Stripe live API key | security | 365 | Stripe | Annual rotation. |
| OpenAI API key | security | 365 | OpenAI | Annual. |
| Anthropic API key | security | 365 | Anthropic | Annual. |
| FMCSA WebKey | security | 730 | FMCSA | Vendor-driven. |
| JWT_SECRET | security | 365 | self-issued | Annual rotation. Forces all sessions to re-auth on rotation. |
| ADMIN_PASSWORD | security | 90 | self-issued | Quarterly. |
| CHL_TOTP_AT_REST_KEY | security | 365 | self-issued | DD3 secrets store master. NEVER lose -- without it all stored TOTP secrets are unrecoverable. |
| DPAPI master keys (if used) | security | 365 | Windows | Rotation requires careful re-encryption flow. |
| TOTP backup codes | security | 365 | self-issued | Regenerate after each use; verify yearly. |
| Stripe webhook signing secret | security | 365 | Stripe | Annual. |
| Telnyx webhook signing secret | security | 365 | Telnyx | Annual. |
| Plivo webhook signing secret | security | 365 | Plivo | Annual. |

---

## Default reminder thresholds (per row)

The scanner ladder (overrideable per row in `reminder_thresholds_days`):

```
[60, 30, 14, 7, 3, 1]
```

- **60d** -- "kick off renewal process" (heads-up only)
- **30d** -- "actively working on it"
- **14d** -- "two-week buffer"
- **7d** -- "one week left" -- SMS-eligible
- **3d** -- "critical" -- SMS-eligible
- **1d** -- "tomorrow" -- last-call SMS-eligible

For monthly cadence rows (Twilio numbers etc. with cadence_days=30),
override to a tighter ladder like `[14, 7, 3, 1]` so the 60+30 thresholds
don't fire spuriously every 30-day cycle.

---

## Open questions (operator decision points)

1. **Insurance broker** -- which broker holds general liability + cargo +
   E&O? Once chosen, populate the three rows above with real
   vendor_or_authority + contact email.

2. **Domain registrar** -- need to confirm whether continentalhaul.com is
   on Cloudflare Registrar (auto-renew) or Namecheap (manual). Set
   `auto_renew=true` if covered by registrar's auto-renew + active card.

3. **Tax vs accountant** -- do we DIY tax filings or have a CPA? If CPA,
   the renewal_contact_email is the CPA, not the IRS portal.

4. **Quarterly tax dates** -- the cadence_days=90 above is approximate.
   Federal estimated tax due dates are calendar-fixed (Apr 15 / Jun 15 /
   Sep 15 / Jan 15), not 90-day rotating. Consider hardcoding the four
   rows with explicit `expires_at` for the next year, and re-rolling on
   `mark-renewed`.

5. **TOTP backup codes** -- recommend storing in 1Password or similar +
   regenerating immediately after each use. Cadence_days=365 is "verify
   yearly" not "rotate yearly."

---

## Run instructions (`scripts/seed_renewals.py`)

Iter 145.x Stream NN3 (2026-05-08 EOD) followup to KK1. The script
populates `db.renewals` with 34 baseline rows (Tier 1: 15 regulatory +
tax + insurance / Tier 2: 10 vendor / Tier 3: 9 security). Idempotent
by row `name` -- safe to re-run. Dry-run is the default; `--apply`
performs writes.

### Dry-run first (mandatory)

```powershell
C:\CHL\.venv-local\Scripts\python.exe C:\CHL\scripts\seed_renewals.py
```

Reports `[WOULD-CREATE] <name>` for each seed that would be inserted
plus `[SKIPPED] <name> (already_exists)` for any row already present
in `db.renewals`. NO writes are made.

### Apply

```powershell
C:\CHL\.venv-local\Scripts\python.exe C:\CHL\scripts\seed_renewals.py --apply
```

Inserts every seed not already present. Each new row stamps:

- `expires_at = now() + renewal_cadence_days` (operator corrects below).
- `current_value_hash = None`, `last_renewed_at = None`,
  `reminders_sent_at = []`.
- `archived = False`, `auto_renew = False`,
  `operator_action_required = True`.
- `reminder_thresholds_days = [60, 30, 14, 7, 3, 1]` by default; the
  monthly-cadence rows (Twilio/Telnyx/Plivo/Mongo Atlas/Mercury) and
  SSL (90d) override to `[14, 7, 3, 1]`; OpenAI/Anthropic (60d) use
  `[30, 14, 7, 3, 1]`.

### Filter by category

```powershell
# Seed only regulatory rows
C:\CHL\.venv-local\Scripts\python.exe C:\CHL\scripts\seed_renewals.py --apply --filter regulatory

# Categories: regulatory | tax | insurance | vendor | security
```

### Recovery: un-archive a soft-deleted seed row

```powershell
C:\CHL\.venv-local\Scripts\python.exe C:\CHL\scripts\seed_renewals.py --apply --reset-archived
```

For each seed name, if a row exists with `archived=True`, flips it
back to `archived=False`. Does NOT touch unarchived rows.

### Correcting `expires_at` after seeding

The default `expires_at = now() + cadence_days` is a placeholder. For
rows where the operator knows the real anchor date (e.g., LLC
formation date for the Missouri SoS annual; broker bond purchase date
for BMC-84; tax-quarter due dates), correct via the operator
dashboard or via:

1. **Mark renewed (preferred)** -- POST `/api/_renewals/{id}/mark-renewed`
   with `renewed_at` set to the actual last renewal date. The router
   computes `new_expires_at = renewed_at + cadence_days`, resets the
   reminder ladder, and stamps `last_renewed_at`.

2. **Direct correction** -- the dashboard (when wired) exposes a PATCH
   endpoint per Stream NN2; until then, recreate the row by archiving
   the seed (DELETE) and POSTing fresh with the correct `expires_at`.

### Verify after seeding

```powershell
# Hit the summary endpoint (requires owner JWT):
curl http://localhost:8001/api/_renewals/summary -H "Authorization: Bearer $JWT"
```

Expect `count: 34` (or whatever subset was filtered) with
`band_counts` distributed across `fine` / `soon` / `urgent` / `critical`
based on cadence. Monthly-cadence rows (30d) will land in `soon` /
`urgent` immediately, which is expected -- the operator clears
`operator_action_required` after confirming the actual subscription
billing date.

### Safety properties

- **Idempotent.** Re-running never double-creates. Match key is `name`.
- **Dry-run default.** No writes unless `--apply` is passed.
- **No data destruction.** The script only INSERTs (or, with
  `--reset-archived`, flips `archived: false`). It never deletes or
  overwrites existing rows.
- **No external deps** beyond `pymongo` (already in repo).

Tests live at `backend/tests/test_seed_renewals.py` (4 tests, all
passing as of the NN3 ship).
