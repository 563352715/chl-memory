# Telnyx Toll-Free Port Action Items — sr_edc875 (+1-866-490-6433)

**FOC Target: 2026-05-14**

**Operator surfaced 2026-05-09 EOD-17.** Two Telnyx-thread comments on porting order `sr_edc875`:

## Telnyx asks (from staff comment 2026-05-08)

1. **Toll-Free Messaging Use Case Request** — required if we plan to message from the toll-free number. Reference: https://support.telnyx.com/en/articles/5353868-toll-free-messaging
2. **Hosted SMS request** — required to minimize SMS downtime through FOC. Takes 3-4 business days. Reference: https://support.telnyx.com/en/articles/5336668-hosted-sms-messaging-process. Telnyx CANNOT guarantee SMS migration at FOC without this.
3. **LOA must include "Port to QIT02"** — phrase required on the Letter of Authorization.

## Acknowledgment posted by Dispatch Team (2026-05-08 22:40)

The Telnyx thread shows a signed reply confirming all three items will be done. Reply was sent under "Dispatch Team / Continental Haul Logistics LLC / MC 1817555 · USDOT 4569843" signature.

**ACKNOWLEDGMENT ≠ EXECUTION. The actual portal submissions are still pending operator-side confirmation.**

## Status check (PENDING — operator must confirm or execute)

| # | Action | Owner | Status | Days to FOC |
|---|---|---|---|---|
| 1 | Submit Toll-Free Messaging Use Case at portal.telnyx.com | Operator | UNCONFIRMED | 4 days |
| 2 | Submit Hosted SMS request (3-4 business day SLA) | Operator | UNCONFIRMED | **CRITICAL — needs to start TODAY to clear FOC** |
| 3 | Upload v2 LOA with "Port to QIT02" phrase | Operator | UNCONFIRMED | 4 days |

## Risk

If Hosted SMS request isn't submitted by 2026-05-10 (today, UTC), it will NOT clear before FOC. SMS service will be DOWN at the FOC cutover until Hosted SMS finalizes 3-4 business days later. That breaks the carrier-comms loop the platform depends on.

## Why the inbox triage missed promoting this to URGENT earlier

Inbox triage script categorized Telnyx port-status messages as `OPERATOR_REVIEW` (the default Telnyx verdict). Should have been `URGENT` given May-14 FOC proximity.

**Hardened in LLL18d (2026-05-10):** [`scripts/inbox_triage_hourly.py`](scripts/inbox_triage_hourly.py) now promotes Telnyx messages containing port-status / LOA / QIT02 / messaging use case / hosted SMS / foc date / rejected keywords to URGENT through the FOC window.

## Recommended operator actions (in order, most-urgent first)

1. **TODAY (2026-05-10):** Log into portal.telnyx.com → submit Hosted SMS request for +1-866-490-6433 (3-4 business day SLA — must start NOW)
2. **TODAY:** Submit Toll-Free Messaging Use Case at the same portal. Use case description: "Outbound dispatch confirmations + inbound carrier replies for freight brokerage operations under MC 1817555 / USDOT 4569843. Operating from dispatch@continentalhaul.com domain. Volume estimate: 50-150 messages/day at FOC; growing toward 500/day by end of 2026."
3. **TODAY-2026-05-11:** Re-upload LOA v2 with the "Port to QIT02" phrase explicitly included
4. **2026-05-12:** Confirm via portal.telnyx.com that all three are in `submitted` or `approved` state. If any is still `pending` 48h before FOC, escalate to support@telnyx.com.
5. **2026-05-13:** Last-mile confirmation. Provision env vars on backend per `chl-memory/operations/may_14_platform_readiness_runbook.md`:
   - `TELNYX_FROM_NUMBER` = +14172193856 (the 10DLC port from sr_b98ea4)
   - `TELNYX_TOLLFREE_NUMBER` = +18664906433 (the toll-free from sr_edc875)
   - `TELNYX_WEBHOOK_SECRET` = (provided by Telnyx after registration completes)
   Use `scripts/set_env_var.ps1` to write these.

## Cross-references

- `chl-memory/operations/may_14_platform_readiness_runbook.md` (CCC1 runbook)
- `~/.claude/projects/c--CHL/memory/project_may_14_platform_readiness_deadline.md` (May-14 milestone)
- Telnyx port order: `sr_edc875` (toll-free) + `sr_b98ea4` (10DLC local)
