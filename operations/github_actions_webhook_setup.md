# GitHub Actions Webhook Setup -- Operator Runbook

**Stream BBB4** (TT2 follow-up #1, 2026-05-08).

## Why this exists

`backend.self_healing.rollback_watcher._sample_test_pass_rate` is one of
four real metric samplers grounding Phase 4 rollback decisions. Until
BBB4, this sampler was a stub reading the
`CHL_TEST_PASS_RATE_OVERRIDE` env var (operator-fed manual values).

BBB4 closes that gap. With the GitHub webhook configured, the platform
receives a delivery POST every time a CI run completes, persists it to
`db.ci_runs`, and the sampler queries the last 50 completed runs to
compute the success ratio. The env-var override remains as a fallback
for environments where the webhook is not configured.

## What you'll do

1. Provision the shared HMAC secret on the platform (one-time, via
   `set_env_var.ps1`).
2. Configure the webhook in GitHub repo settings (one-time, manual click
   path).
3. Verify a real workflow_run delivery lands in `db.ci_runs`.

## Step 1 -- Provision the secret

Run on the platform host (PowerShell, ADMIN-elevated):

```powershell
# Generate a strong random secret (40+ chars).
$secret = -join ((1..48) | ForEach-Object { [char[]](48..57 + 65..90 + 97..122) | Get-Random })
Write-Host "Generated GITHUB_WEBHOOK_SECRET: $secret"

# Persist to the encrypted vault via the standard helper.
C:\CHL\scripts\set_env_var.ps1 -Key GITHUB_WEBHOOK_SECRET -Value $secret

# Restart the NSSM service so the new value is picked up.
nssm restart chl-backend
```

Save the secret value somewhere safe -- you'll paste it into GitHub in
step 2. After step 2 you can delete it locally (the vault has it).

## Step 2 -- Configure the webhook in GitHub

1. Go to https://github.com/<owner>/<repo>/settings/hooks
2. Click "Add webhook"
3. Fill in the form:
   - **Payload URL**: `https://<your-platform-host>/api/_ci/github_actions_webhook`
   - **Content type**: `application/json`
   - **Secret**: paste the value generated in step 1
   - **SSL verification**: Enable
4. Under "Which events would you like to trigger this webhook?", choose
   "Let me select individual events" and check ONLY:
   - **Workflow runs**
   (uncheck everything else -- BBB4 ignores other event types but the
   smaller surface keeps the audit trail tight)
5. Active: checked
6. Click "Add webhook"

GitHub will send a `workflow_run` "ping" delivery immediately. You
should see it succeed (green checkmark) under "Recent Deliveries".

## Step 3 -- Verify

After the next CI run completes:

```bash
# On the platform host, query the ci_runs collection.
mongo chl --eval 'db.ci_runs.find().sort({completed_at: -1}).limit(3).toArray()'
```

You should see at least one row with the most recent `workflow_run_id`,
`conclusion`, `branch`, and `head_sha` populated.

## What if it doesn't work?

- **GitHub delivery shows 401**: secret not provisioned. Re-run step 1
  + verify the NSSM restart picked up the new env var.
- **GitHub delivery shows 400**: signature mismatch. Most likely the
  secret in GitHub doesn't match the one in the vault. Re-paste in
  GitHub's webhook settings.
- **GitHub delivery shows 200 but no row in `db.ci_runs`**: payload was
  acknowledged but skipped (e.g., `action != "completed"`). This is
  expected for in-progress events. Check the backend logs for the
  specific skip reason.
- **Sampler still returns 100.0 after several real CI runs**: the
  sampler requires 5+ rows in the trailing 7 days before switching off
  the env-var fallback. Wait for ~5 CI runs to accumulate.

## Ongoing maintenance

- **Retention**: db.ci_runs is pruned daily by CRON 26 (365-day
  retention; rejects retention <30d or >1095d as operator-typo guards).
- **Rotation**: rotate the webhook secret periodically (say, every 6
  months). Re-run step 1 with a new value, re-paste in GitHub. The
  vault retains the old value until next `set_env_var.ps1` write
  overwrites it; GitHub's webhook UI shows the secret only at creation
  time.
- **Multiple repos**: the same secret can be reused across multiple
  GitHub repos pointing at the same webhook URL. The endpoint
  signature-verifies against the shared secret regardless of which repo
  delivered the payload.

## Cross-references

- Endpoint: `backend/ci_intake/github_actions_router.py`
- Sampler: `backend/self_healing/rollback_watcher.py:_sample_test_pass_rate`
- Prune: `backend/maintenance/ci_runs_prune.py` (CRON 26)
- Tests: `backend/tests/test_ci_intake.py`,
  `backend/tests/test_ci_runs_prune.py`
- TT2 commit: `f3e1c88` (real samplers + REVERT mode)
- BBB4 commit: pending
