---
title: Twilio 30034 + A2P 10DLC + TFV campaign rejection history
created: 2026-05-08 EOD-16-late (Stream MM2)
purpose: Operationally useful Twilio compliance history archived OFF the operator-visible UI surfaces. Operator can grep here when context is needed; the live dashboard no longer displays Twilio-branded labels.
---

# Why this archive exists

Operator's verbatim 2026-05-08 EOD-16-late directive:

> "You can also remove all reference to twilio and rejecting the campaign everywhere in the platform or otherwise unless we need that info somewhere to keep it. Just get it out of my sight."

Stream MM2 swept frontend-visible Twilio + campaign-rejection mentions out of operator-facing surfaces. This doc preserves the *operationally useful* compliance history so:

1. Backend code that still talks to Twilio (SMS dispatch, voice TwiML, browser softphone) keeps running until the Telnyx port lands (FOC May 14-15, 2026).
2. The 30034 / 30032 / TFV rejection-code history isn't lost — needed if the SMS provider swap surfaces analogous compliance issues.
3. Future devs / agents can grep `chl-memory/research/twilio_30034_campaign_history.md` for context the live UI no longer surfaces.

# Error 30034 — what it is

Carrier-network filter applied when an A2P (application-to-person) long-code SMS is sent from a phone number whose 10DLC (10-digit long code) brand + campaign vetting is not APPROVED in The Campaign Registry (TCR).

- `30032` — TFV (toll-free) verification not approved → toll-free SMS blocked.
- `30034` — 10DLC campaign not approved → long-code SMS blocked.
- Both result in `undelivered` status with the message billed but not delivered.

Affected period: every send to operator's phone since at least 2026-04-23 returned `undelivered` with 30032 or 30034 (per `Messages.json` log inspection).

# Architecture isolation (welded 2026-04-28 in FACTS.md §11)

- TFV track (toll-free 866 number) vs 10DLC track (long-code 417 number) are independent compliance tracks. A failure on one does not affect the other.
- `+14172193856` (10DLC long code) is configured to forward incoming **voice calls** to the operator's cell. This Twilio Voice TwiML feature has zero impact on SMS delivery, A2P 10DLC vetting, or any 30034 warning. SMS and Voice are isolated tracks.

# Failover behavior (notification_service.py)

- Channel priority: Email → Voice → SMS.
- `_UNDELIVERABLE_A2P_CODES = {"30007", "30034", "30035", "30036"}` triggers automatic SMS → Email → Voice failover when the SMS path returns these codes.
- 10DLC campaign-status check gates SMS sends entirely if Brand != APPROVED or Campaign != VERIFIED — prevents racking up 30034 errors at $0.03/segment.

# TFV rejection codes (4 active during 2026-04 review cycle)

- `30445` — Business identity / FMCSA mismatch
- `30497` — Use-case summary insufficient
- `30499` — Sample messages don't show consent compliance
- `30513` — Opt-in workflow description incomplete

The toll-free resubmission console (`/twilio-tollfree`) coordinates ~25 fields needed to address all 4 codes in one POST.

# Related code paths still using Twilio (do NOT touch until Telnyx port lands)

- `backend/automations.py` — Twilio voice + SMS production paths.
- `backend/browser_phone.py` — Twilio Voice SDK softphone (Iter 134.16).
- `backend/auto_dispatch.py` — uses TWILIO_BROKER_FORWARDING_NUMBER for inbound voice forwarding.
- `backend/circuit_breaker.py` — Twilio circuit breaker (runtime guard).
- `backend/notification_service.py` — Twilio Voice TTS robo-call dispatcher.
- `backend/outreach/orchestrator.py` — Twilio Voice rate-con outreach (PROVIDER_TWILIO_VOICE).
- `backend/voice_operator.py` — voice operator (FCC-governed, not TCR/A2P).
- `backend/server.py` — `/api/twilio/voice/*` endpoints, phone_status, voice_inbound, voice_dial_bridge, voicemail_complete, voice_status.
- `backend/system_health.py` — SMS outbound subsystem (key now `sms_outbound`; legacy `twilio_outbound` alias retained).
- `backend/ops_state.py` — integration health pills (now labeled "Toll-Free SMS" + "SMS Campaign (10DLC)"; keys still `twilio_tfv` / `twilio_10dlc` for API stability).
- `backend/tcr_status.py` — TCR/A2P 10DLC status reader.
- `backend/tollfree_compliance.py` — TFV resubmission API.

# UX cleanup applied (Stream MM2, 2026-05-08 EOD-16-late)

Frontend operator-visible labels swapped to provider-agnostic copy:

| Surface | Before | After |
|---|---|---|
| Phone tab subtitle | "...through your Twilio business number." | "...through your business number." |
| Phone tab status badge | `LIVE · ${twilio_number}` | `LIVE · ${phone_number}` (with `twilio_number` fallback) |
| Phone-not-connected banner | "Twilio not connected — everything below runs in mock mode." | "Phone provider not connected — everything below runs in mock mode." |
| SMS-mocked notification | "MOCKED (Twilio not connected)" | "MOCKED (SMS provider not connected)" |
| Call-mocked notification | "Twilio not connected — call MOCKED." | "Voice provider not connected — call MOCKED." |
| Business Settings → Messaging | "Twilio SMS · PLACEHOLDER" + "Paste your Account SID, Auth Token & Number..." | "SMS Provider · PLACEHOLDER" + "Configure your messaging provider credentials..." |
| Business Settings → Placeholder mode | "Once you point your domain's email to us and buy a Twilio number..." | "Once you point your domain's email to us and provision a business phone number..." |
| Driver Messages header | "For SMS delivery, integrate Twilio later." | "For SMS delivery, integrate an SMS provider later." |
| System Health card label | "Twilio Outbound" | "SMS Outbound" |
| A2P banner labels | "A2P 10DLC Campaign PENDING/REJECTED/NOT_REGISTERED" + "Twilio Trust & Compliance is reviewing..." / "Twilio rejected the 10DLC campaign..." / "No brand/campaign filed with Twilio yet." | "SMS Campaign PENDING/NEEDS RESUBMIT/NOT REGISTERED" + "Carrier compliance review in progress..." / "Carrier compliance returned issues..." / "No carrier-compliance filing yet." |
| A2P banner refresh button | "Refresh Twilio Status" | "Refresh SMS Status" |
| TCR status widget header | "A2P 10DLC · ${badge.label}" | "SMS Campaign · ${badge.label}" |
| TCR status widget refresh tooltip | "Force refresh from Twilio API (owner-only)" | "Force refresh from SMS provider (owner-only)" |
| TCR status widget loading | "Checking A2P status…" | "Checking SMS campaign status…" |
| TCR status widget error | "A2P Status Unavailable" | "SMS Campaign Status Unavailable" |
| Notification Log ops cards | "A2P 10DLC" / "Twilio Voice" | "SMS Campaign" / "Voice Line" |
| CommandSearchBar emergency drawer | "Twilio voice/SMS is currently blocked (A2P 10DLC pending) — the tap-to-dial link below uses your phone's native dialer." | "The tap-to-dial link below uses your phone's native dialer." |
| CommandPhoneView call tooltip | "Bridge call to ${phone} via Twilio (your cell rings first)" | "Bridge call to ${phone} (your cell rings first)" |
| BrowserPhoneView registering banner | "Registering with Twilio… if prompted, click Allow..." | "Registering with voice provider… if prompted, click Allow..." |
| AutomationsDashboard check-ins | "Twilio webhook: ... (configure in Twilio console)" | "SMS provider webhook: ... (configure in your messaging provider's console)" |
| AutomationsDashboard voice agent steps | "Log into Twilio console → Phone Numbers" / "...forwards to your cell (TWILIO_BROKER_FORWARDING_NUMBER)" / "Requires ... Twilio voice-capable number." | "Log into your voice provider's console → Phone Numbers" / "...forwards to your cell (configured forwarding number)" / "Requires ... a voice-capable business number." |
| TollFreeCompliancePage header | "Twilio Toll-Free Verification" | "Toll-Free SMS Verification" |
| TollFreeCompliancePage buttons | "Pull live Twilio status" / "Resubmit to Twilio" | "Pull live verification status" / "Resubmit" |
| TollFreeCompliancePage confirm | "Apply this packet to the live Twilio toll-free verification? This sends a POST to messaging.twilio.com..." | "Apply this packet to the live toll-free SMS verification? This sends an API call to your messaging provider..." |
| TollFreeCompliancePage edit-reason | "Edit reason (paste in Twilio Console resubmit form)" | "Edit reason (paste in provider Console resubmit form)" |
| Day-1 readiness email blocker hint | "Twilio TFV PENDING_REVIEW" | "Toll-Free SMS verification PENDING_REVIEW" |
| Gap-close watcher email | "External service (Twilio TFV, FMCSA, HaulPay) changed status..." | "External service (SMS provider TFV, FMCSA, HaulPay) changed status..." |
| /api/system/health-text gate hint | "Twilio TFV == TWILIO_APPROVED" | "Toll-Free SMS verification == APPROVED" |
| /api/health/system response | `subsystems.twilio_outbound` | `subsystems.sms_outbound` (legacy `twilio_outbound` alias retained) |
| /api/broker/phone/status response | `twilio_number` | `phone_number` (legacy `twilio_number` alias retained) |

LEFT IN PLACE (intentionally):

- `frontend/src/views/CompliancePages.jsx` — public-facing legal disclosures listing Twilio + Resend as data subprocessors. This is required A2P 10DLC + privacy-law disclosure copy, not an operator-facing surface. Stays until the Telnyx port lands and the privacy policy gets updated to swap the vendor.
- `frontend/src/views/LandingPage.jsx` — Twilio mentions are JS code comments only (`// Twilio TFV / A2P 10DLC consent text...`); the rendered consent block speaks of "text messages from Continental Haul Logistics" with no provider mention.
- `frontend/src/App.js` route comments (`// Twilio Toll-Free Verification Console — Iter 134.3` etc.) — JS comments, not rendered. Documents current backend reality and survives provider swap.
- All backend code that actively dispatches via Twilio (automations, browser_phone, auto_dispatch, circuit_breaker, notification_service, outreach orchestrator, voice_operator, server.py voice/sms endpoints, tcr_status, tollfree_compliance). Removing these would break SMS dispatch — directive explicitly carved out the post-port iter for the swap.

# Pointers if you need to dig

- Original cause analysis: `chl-memory/A2P_10DLC_RESUBMISSION_PACKET.md`, `chl-memory/FACTS.md` (§11), `memory/FACTS.md`, `memory/system_state_v75.md`, `memory/system_state_v79_2.md`.
- SMS provider migration plan: `chl-memory/research/sms_provider_evaluation_2026_05_08.md`.
- Iter 141.2 launch runbook (which 30032/30034 outcomes triggered fallback): `chl-memory/research/iter_141_2_launch_operator_runbook.md`.
- Plivo migration delta: `backend/plivo_client.py` (header comment lists Twilio-vs-Plivo error-code differences).
- Self-heal triage classifier: `backend/self_heal_audit.py` (`(re.compile(r"a2p|10dlc|message.*block|30034|30007", re.I), ...)`).
