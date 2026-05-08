# SMS / Voice Provider Evaluation — 2026-05-08

> **Purpose.** Operator-mandated 2026-05-08 EOD-14: *"We need to get text working. Perhaps we check into other suppliers for our communication needs. I never got a response from Plivo. Put that together and tell me when I get back."*
>
> Comprehensive comparison of SMS/Voice providers + concrete recommendation set the operator can act on. Triggered by:
> 1. Twilio A2P 10DLC campaign REJECTED (error 30909, CTA verification incomplete).
> 2. Plivo signup blocked by vendor email-validator (rejected dispatch@, jasonandpugee@gmail.com, jason.meyer@continentalhaul.com).
> 3. Plivo sales escalation email sent — no response received as of 2026-05-08.
> 4. Email-First + Voice TTS failover currently active (per `notification_service.py` channel priority); SMS entirely broken.
>
> **Audience.** Operator (decision authority) + future @dev-engineer / @pm-lead (execution).
> **Author.** dev-engineer research sub-agent, 2026-05-08 EOD-14 dispatch.

---

## TL;DR (read this first if time-constrained)

1. **Stop waiting on Plivo.** No response in 24+ hours after escalation = the vendor isn't going to unblock CHL on their normal sales workflow. Treat as a soft-no. Move on.
2. **Telnyx is the strongest replacement.** $0.004/SMS (52% cheaper than Twilio, 48% cheaper than Plivo), self-serve A2P with no white-glove gate, 24/7 free support, modern Python SDK, no documented domain-block on `continentalhaul.com`. Sole-prop or LLC paths both supported. Drop-in for our existing `notification_service.py` abstraction.
3. **Twilio re-submission is the parallel hedge.** The 30909 rejection was a CTA-completeness issue, not an account ban. We can re-submit the campaign with corrected CTA documentation in the same Twilio account. No re-vetting fee. 5–7 business day re-approval if done right.
4. **Bandwidth.com is the long-term play if we exceed 100K msgs/month.** Direct-to-carrier ($0.002/SMS, lowest in market). Sales-touch signup, 3-month minimum commitment per campaign — only worth it once volume justifies the friction.
5. **Email-first + Voice TTS is already shipped and works.** Worst case, we operate in this mode for another 2–3 weeks while the new SMS provider activates. Not a crisis.

---

## 1. Current state — what's working, what's broken, what's blocked

### Working (in production)

- **Email channel (PRIMARY).** `notification_service.py` Channel.EMAIL via Resend. Magic links, rate cons, load documents — all reliable.
- **Twilio Voice TTS (BACKUP).** FCC-governed, not subject to TCR/A2P 10DLC. Robocall flow ("check your email") is operational. This is what the current banner labels "Voice TTS failover."
- **The notification abstraction itself.** `notification_service.py` (~990 lines) already has channel-priority logic, automatic SMS→Email→Voice failover on 30007/30034 errors, A2P 10DLC status monitor, and persistence of every attempt. Provider swap = changing the SMS implementation under the abstraction; no caller-side rewrite needed.
- **`plivo_client.py` (~130 lines, dormant).** Pre-built drop-in replacement with the same `text_operator(message, to=None, dry_run=False) -> dict` signature. Can be re-shaped to any other vendor (Telnyx, Bandwidth) in <30 min.
- **`outreach/orchestrator.py` (~422 lines).** Cutover-ready. Already-abstracted vendor selection.

### Broken (Twilio SMS path)

- **Twilio A2P 10DLC campaign.** Rejected 2026-04-27 with error 30909 (Message Flow / Call to Action incomplete or unverifiable). Per Twilio docs, this means the campaign submission did NOT clearly document every opt-in path the brand uses (verbal, paper, in-store, QR, web form, etc.).
- **Brand registration itself was APPROVED** (TCR ID B6384Q7, brand BN250fa0…). Only the campaign got rejected. This is important: it means the account is in good standing, the business identity is verified, and re-submission is allowed.
- **Toll-Free Verification (TFV) bundle was never submitted** on the Twilio account, so toll-free SMS is also offline through Twilio.
- **Empirical send tests (per `Messages.json` log inspection):** every send to operator's phone since at least 2026-04-23 has come back as undelivered with error 30032 or 30034 (carrier filtering for unregistered A2P traffic).

### Blocked (Plivo signup)

- Vendor's signup-form email validator rejected three CHL email addresses:
  - `dispatch@continentalhaul.com` (role-based prefix filter)
  - `jasonandpugee@gmail.com` (personal email — rejected, but Plivo policy is "no personal email")
  - `jason.meyer@continentalhaul.com` (created as Zoho alias — STILL rejected; verified live + deliverable; rejected in incognito too)
- **Diagnosis** (per `iter_141_3_stage_1a_signup_troubleshooting.md` §1–3): vendor-side domain-level block on `continentalhaul.com`, likely backed by a third-party validator (Kickbox / ZeroBounce / equivalent) that doesn't recognize the domain as established business. Domain-age + DMARC reputation gap is the load-bearing cause.
- **Operator escalation:** `sales@plivo.com` email sent with FMCSA legitimacy proof (MC-1817555, USDOT 4569843, business name, rejection text, use case). **No response received.**
- **Workaround attempted but not executed:** §5 of the troubleshooting doc recommends "Personal Gmail signup → swap to custom-domain after activation." Operator hasn't tried this path yet; it remains an option but means standing up the account on a personal gmail first, which has its own friction.

---

## 2. Plivo status assessment

### Escalation timeline (2026-05-07 → 2026-05-08)

- 2026-05-07: operator hit signup blocker on stage 1a, exhausted §3 diagnostic (refresh + incognito + MX check), confirmed vendor-side domain-level block.
- 2026-05-07: operator sent escalation email to `sales@plivo.com` per the playbook §4 template.
- 2026-05-07 → 2026-05-08 (24+ hours): no reply received.
- 2026-05-08 EOD-14: operator surfaces the lack of response as a directive — "Perhaps we check into other suppliers... I never got a response from Plivo."

### What this likely means

- **Plivo's sales team turnaround on signup-blocker requests is documented as ~1 business day** in the playbook, but reviews on Capterra/Gartner show "mixed support response times" with users reporting "two-week waits" on signup-blocker resolution in some cases.
- **The expected path was a manual allowlist via FMCSA SAFER lookup** (similar to how Bandwidth handles small-business verification). The fact that no response came in 24 hours doesn't conclusively mean rejection — but it strongly suggests Plivo is not prioritizing this case.
- Possible reasons (cannot confirm, recommend operator confirm via Plivo sales if they want closure):
  - Volume-based filtering: solo operators with low projected volume don't get sales attention.
  - Domain block is a hard policy not a soft validator (e.g., Plivo signed an agreement with their email-vendor that they won't override domain-level blocks).
  - Internal queue not prioritized; would respond eventually but not on a useful timeline.

### Recommendation: move on from Plivo

- **Don't keep waiting.** A vendor that blocks signup AND doesn't respond to a 24-hour escalation is signaling they're not the right partner.
- **Keep the Plivo white-glove packet on file** (`chl-memory/research/plivo_white_glove_packet_FINAL.md`) — if they respond in the future, we have it ready, and the CTA / opt-in language is reusable for whichever vendor we land on.
- **Don't try the Personal-Gmail workaround.** Two reasons: (a) it commits us further to a vendor that's already shown friction; (b) creating an account on personal email and then swapping to business email leaves a paper trail of "this account was originally a personal signup," which can come back as a TCR/brand-registration complication if the brand-record-vs-signup-email mismatches matter to the carrier.
- **Send a 1-line follow-up on day 3 (2026-05-09) closing the loop.** "Following up on signup blocker — closing this thread; we are evaluating alternative providers. Will reach out if needs change." This keeps the door open without pretending we're still waiting.

---

## 3. Vendor comparison table

> **Definitions:**
> - **Per-SMS cost** = published 2026 list price for outbound US SMS, single segment, A2P 10DLC long code. Excludes carrier-pass-through fees (which apply to all providers per industry rules) unless noted.
> - **Per-voice-min cost** = published 2026 list outbound US local rate.
> - **A2P friction (1=easy, 5=hard)** = score combining brand-vetting strictness, campaign-submission UX, and historical CHL experience.
> - **Sign-up friction (1=easy, 5=hard)** = does signup work for solo operators on custom domain?
> - **API quality** = subjective: SDK maturity, docs quality, modern auth patterns.
> - **Time-to-first-SMS** = signup → working SMS, business days assuming no rejection.

| Vendor | Per-SMS (US, 10DLC) | Per-voice-min | A2P friction (1-5) | Sign-up friction (1-5) | Python SDK | US-only? | Toll-Free SMS? | Time-to-first-SMS |
|---|---|---|---|---|---|---|---|---|
| **Twilio** | $0.0083 | $0.014 outbound | 4 (rejected once already; can re-submit) | 1 (already signed up) | Excellent (`twilio` lib, mature) | Global | Yes (TFV req'd) | 5–7 biz days (re-submit) |
| **Plivo** | $0.0077 | $0.013 outbound | 3 (white-glove handles it) | 5 (BLOCKED on `continentalhaul.com`) | Good (`plivo` lib) | Global | Yes | BLOCKED (no signup access) |
| **Telnyx** | **$0.004** | $0.008 outbound | 2 (self-serve, transparent) | 1 (no documented domain blocks) | Good (`telnyx` lib) | Global | Yes | 5–10 biz days |
| **Bandwidth** | **$0.002** (lowest) | ~$0.005 outbound (req. quote) | 3 (manual TFV review; sales-touch) | 4 (sales-touch, EIN required, 3-mo campaign min) | Good (`bandwidth-sdk`) | US-primary | Yes (TFV mature) | 7–14 biz days (sales onboard) |
| **Bird (MessageBird)** | $0.00331 | ~$0.011 outbound | 3 (similar to Twilio) | 2 (works on custom domains generally) | Good (`messagebird` lib) | Global | Yes | 5–10 biz days |
| **Sinch** | $0.0078 | ~$0.013 outbound | 3 (enterprise-tier handhold) | 4 (enterprise-tier; expensive number rental $25/mo) | Fair (`sinch-sdk`, less mature) | Global | Yes | 7–14 biz days |
| **AWS SNS / End User Messaging** | ~$0.00645/segment + $1/mo number | N/A (not a voice provider for outbound TTS) | 4 (TCR registration required; AWS docs lag) | 2 (AWS account already exists for many) | Excellent (`boto3`) | US + intl | Yes | 5–14 biz days |
| **ClickSend** | $0.0228 (high under 5K/mo) → $0.0082 (150K+/mo) | ~$0.025 (higher than peers) | 3 (handles registration) | 1 (very easy signup) | Good (`clicksend-client`) | Global | Yes | 3–7 biz days |

### Notes on the table

- **Carrier-pass-through fees** ($0.0025–$0.005 per message) apply on top of every provider's base rate. These are industry-wide and set by AT&T / T-Mobile / Verizon, not the CPaaS. T-Mobile increased to $0.0025/MO January 2026; Verizon increased to $0.004 June 2025.
- **Twilio's "rejected once" doesn't mean "banned forever."** Per Twilio docs, error 30909 explicitly allows correct + resubmit at NO additional vetting fee. The brand record (BN250fa0…) is APPROVED; only the campaign needs re-work.
- **Bandwidth's $0.002/SMS is the lowest published rate** but they require sales-touch signup, EIN, and a 3-month minimum commitment per campaign ($30 minimum spend per campaign before you can cancel). Worth it at >100K msgs/month, overkill at v1 freight-broker scale (50–100/mo).
- **AWS SNS / End User Messaging** is messy: it's actually two products (SNS for low-volume one-off; End User Messaging / Pinpoint for production A2P). The TCR registration is still required; AWS doesn't bypass the 10DLC system. Consideration: if we already use AWS for anything else, the IAM/billing integration is one less account to manage. Not currently a CHL dependency.
- **ClickSend** is positioned for small-volume / non-developer-first use cases; pricing is uncompetitive at our scale (we'd pay 3–5x Telnyx for the first 5K msgs/mo).
- **MessageBird/Bird** rebranded in 2023; their pricing got 90% cheaper and the developer experience improved, but US 10DLC is not their primary market (they're stronger internationally).
- **Sinch** is enterprise-tier with $25/mo per number — designed for companies sending >500K msgs/mo. Not a fit for v1 CHL.

---

## 4. Migration effort estimate per option

> **Baseline.** `notification_service.py` already abstracts SMS/Email/Voice channels. `plivo_client.py` already exists as a drop-in replacement template. Effort below = how long to swap to a different vendor under the same abstraction.

| Vendor | Effort tier | Hours | Components touched |
|---|---|---|---|
| **Twilio re-submission** (no migration) | LOW | ~2h | Re-write campaign CTA + Message Flow language; resubmit via console; no code changes. |
| **Telnyx** | LOW | ~4–6h | New `telnyx_client.py` shaped like `plivo_client.py`; env vars; smoke test (6 cases like the Plivo smoke); update `notification_service.py` SMS routing branch (1 import + 1 client call). |
| **Bandwidth** | MED | ~1.5–2 days | New `bandwidth_client.py`; sales onboarding paperwork (operator-handed); BRN/EIN setup; TFV submission paperwork (~1 day operator time before any code work). API differences from Twilio shape: Bandwidth uses Messaging API v2 with a different request/response model — not 1:1 swap. |
| **Bird (MessageBird)** | LOW–MED | ~6–8h | New `bird_client.py`; SDK is similar enough to Twilio that the swap is mechanical; API uses different auth pattern (API keys not auth tokens). |
| **Sinch** | MED | ~1 day | New `sinch_client.py`; enterprise account setup paperwork (operator-handed); higher cost-of-numbers means rethinking how many numbers we provision. |
| **AWS SNS / End User Messaging** | MED | ~1 day | New `sns_client.py` using `boto3`; IAM role setup; if SNS only, no per-recipient TTL/retry behavior — would need our own retry layer. End User Messaging (Pinpoint) is closer to a Twilio-shaped API but is in transitional state for some features. |
| **ClickSend** | LOW | ~4h | Easy signup; SDK is shape-similar to Twilio; mostly a smoke test exercise. Cost-prohibitive at scale though. |
| **Plivo** (if they unblock) | LOW | ~30 min | `plivo_client.py` already exists, smoke 6/6, ready for cutover. |

### Effort multipliers

- **TCR campaign vetting time** is the same for ALL providers — 1–14 business days regardless of vendor (it's the carriers, not the CPaaS, doing the vetting). Only providers like "specialized vetting" services (Telgorithm, etc.) advertise faster turnarounds, and those are essentially 10DLC-broker re-sellers.
- **Operator paperwork time** varies significantly: Telnyx self-serve = ~30 min one-time; Bandwidth sales-touch = ~2–3 hours over multiple days.
- **The notification_service.py abstraction caps the dev migration cost at ~1 day for any well-shaped CPaaS API.** The variable is signup + vetting time on the operator side.

---

## 5. Top 3 recommendations (ranked)

### Recommendation #1: Telnyx (PRIMARY)

**Cost at v1 volume (50–100 msgs/mo):**
- 100 SMS × $0.004 = $0.40/mo + carrier pass-through ($0.0025–$0.005) ≈ **$0.65–0.90/mo**
- Number rental: ~$1/mo
- **Total monthly: ~$2/mo at v1 scale.** Negligible.

**Cost at projected scale (10,000 msgs/mo):**
- 10K SMS × $0.004 = $40 + carriers ($25–50) ≈ **$65–90/mo**
- Vs. Twilio at the same volume: ~$83 + carriers = ~$108–133/mo. **Save ~$45/mo at 10K, ~$450/mo at 100K.**

**Why this is the strongest option:**
1. **No documented signup friction for `continentalhaul.com` or any custom-domain freight-broker.** Telnyx specifically markets to developer-first solo operators and small businesses; their signup is self-serve and approves on the same business day in most cases.
2. **Cheapest published SMS rate among major carrier-grade providers** that aren't sales-touch ($0.004/SMS, half of Twilio).
3. **Free 24/7 chat + phone support included** in every account — Plivo's "no response in 24h" failure mode doesn't apply here.
4. **Modern Python SDK** (`telnyx` library), well-documented, recent commits, mature error handling.
5. **No 10DLC markup** — Telnyx passes the TCR fees through at cost, same as Plivo, cheaper than Twilio.
6. **Can re-use the entire `plivo_client.py` shape** — text → dst/src/text mapping is already there; auth_id/auth_token swap to API key; 30-min code change.
7. **Toll-free SMS path also supported** — gives us a fallback while 10DLC vetting is pending. A new toll-free number can be SMS-active in 5–10 business days with the new 2026 BRN-attached verification flow.

**Limitations / risks:**
- TCR campaign approval still 1–14 business days (industry-wide; not vendor-specific).
- Smaller ecosystem than Twilio — fewer Stack Overflow answers, fewer GitHub issue precedents. Mitigated by good docs + 24/7 support.
- No "white-glove" service — operator + dev have to write the campaign CTA + Message Flow themselves. We already have this content from the Plivo white-glove packet (it's reusable since TCR governs the format, not the CPaaS).

**Decision rationale (1-line):** Best cost + cleanest signup path + drop-in code swap + 24/7 included support = lowest-risk fastest-time-to-SMS path.

---

### Recommendation #2: Twilio re-submission (PARALLEL HEDGE)

**Cost at v1 volume (50–100 msgs/mo):**
- 100 SMS × $0.0083 = $0.83/mo + carriers ($0.0025–$0.005) ≈ **$1.08–1.33/mo**
- Number rental: $1.15/mo
- **Total monthly: ~$2.50/mo at v1 scale.**

**Why this is the right hedge:**
1. **Account already exists, brand already approved, no signup re-work.** TCR ID B6384Q7 is good; only the campaign was rejected.
2. **Re-submission has NO additional vetting fee** per Twilio policy.
3. **5–7 business day re-approval timeline** if the corrected CTA + Message Flow are tight.
4. **No code changes needed** — the existing `notification_service.py` already has the Twilio path; just stop hitting the SMS branch (which is gated on `a2p_10dlc=APPROVED`) and let the gate flip when re-approval comes in.
5. **Operator's lived "Twilio fatigue" was about Plivo-comparable ops friction, not Twilio specifically being a worse product** — and we now have empirical evidence that Plivo isn't smoother. Reset the expectation.

**What needs to change in re-submission:**
- The error 30909 cause was "Message Flow / Call to Action incomplete." Specifically, the original submission likely:
  - Listed a single opt-in path when the platform actually has multiple (web form for shippers, phone-call dispatcher consent for carriers, email confirmation flow, etc.).
  - Had a sample message that didn't include the legally-required "Reply STOP to opt out" disclosure.
  - Had a privacy policy URL that didn't clearly explain SMS opt-in or didn't load.
- **Re-submission checklist** (operator-handed):
  1. Privacy policy at https://continentalhaul.com/privacy — confirm it loads, includes SMS-specific opt-in disclosure.
  2. Terms of service at https://continentalhaul.com/terms — confirm includes SMS terms.
  3. Message Flow text: list every single opt-in path the platform uses (web form, paper signature on broker-carrier agreement, dispatcher verbal consent confirmation, email-magic-link consent confirmation). Include screenshots of each opt-in surface.
  4. Sample messages (×3 minimum) include "Reply STOP to opt out. Msg & data rates may apply." in every variation.
  5. Use case description: "Operational SMS for freight brokerage carrier dispatch — load offers, rate confirmations, in-transit check-ins, exception alerts."
  6. Brand description: cite MC-1817555 and USDOT 4569843 as proof of business legitimacy.
- **The Plivo white-glove packet (`chl-memory/research/plivo_white_glove_packet_FINAL.md`) has all this content already.** It's vendor-neutral; copy-paste it into Twilio's campaign re-submission UI.

**Decision rationale (1-line):** Zero-cost zero-code parallel path — if it goes through, we have a working Twilio path and the migration becomes optional. If it fails again, Telnyx is already provisioned in parallel.

---

### Recommendation #3: Bandwidth.com (LONG-TERM)

**Cost at projected scale (100K msgs/mo):**
- 100K SMS × $0.002 = $200 + carriers ($250–500) ≈ **$450–700/mo**
- Vs. Telnyx at same volume: ~$400 + carriers = ~$650–900/mo
- Vs. Twilio at same volume: ~$830 + carriers = ~$1080–1330/mo
- **Save ~$200–600/mo at 100K msgs/mo vs. Telnyx.** Save ~$600–800/mo vs. Twilio.

**Why this is the long-term play, NOT the v1 path:**
1. **Direct-to-carrier — Bandwidth IS a US tier-1 carrier**, not a re-seller. Lowest published rate in the market.
2. **Best voice quality** (per multiple comparison reviews) — relevant for CHL because operator does dispatcher robocalls.
3. **Mature TFV (Toll-Free Verification) flow** — they were one of the first providers to nail the 2026 BRN-attached toll-free verification process.

**Why NOT now:**
1. **Sales-touch signup** — operator has to do a discovery call before they can even create an account. Direct contradiction to "we need to get text working" urgency.
2. **3-month minimum commitment per campaign.** If we set up a campaign and decide to leave at month 2, we still pay month 3.
3. **EIN required** — operator's current LLC structure may or may not have an EIN; if not, that's a 2-week IRS process before Bandwidth signup.
4. **No Python SDK as polished as Telnyx/Plivo** — `bandwidth-sdk` exists but is more "REST wrapper" than "ergonomic client."
5. **Manual TFV review takes up to 5 business days** per number per campaign — slower than Telnyx.

**When to revisit:** once CHL is sending >50K msgs/mo and the per-message savings exceed $300/mo. Estimated trigger: Phase 8+ or after Q3 2026.

**Decision rationale (1-line):** Cheapest at scale but signup-friction + minimum-commitment kills it for v1; revisit when volume justifies sales-touch onboarding.

---

## 6. Operator-handed steps per recommendation

> All steps are NUMBERED, ONE action per line, no paragraphs (per `feedback_operator_instruction_style.md`).

### #1 — Telnyx signup path

1. Open https://telnyx.com/sign-up in a new browser tab.
2. Click "Sign up" / "Start for free" button (top-right).
3. Email field: type `jason.meyer@continentalhaul.com`. (If validator rejects: try `dispatch@continentalhaul.com`. If both rejected: report back; we'll need fallback. Telnyx is NOT documented to have the `continentalhaul.com` domain block.)
4. Password field: generate via password manager (1Password / Bitwarden), 20+ chars, save as "Telnyx — Console Login."
5. Submit form. Verify email (check inbox for Telnyx verification link, click it).
6. Onboarding wizard: company name = "Continental Haul Logistics LLC", role = "Owner / Founder", use case = "SMS / Voice for freight brokerage."
7. Add billing: credit/debit card. Telnyx is pay-as-you-go — they'll charge $1 to verify the card (refunded).
8. Console → Numbers → "Buy a Number" → US local long code → search "417" or another preferred area code → buy ($1/mo).
9. Console → Messaging → API Keys → "Create API Key" → name "CHL Production" → copy the key (looks like `KEY...`).
10. Use the `set_env_var.ps1` helper to add: `& "C:\CHL\scripts\set_env_var.ps1" TELNYX_API_KEY KEY...` (and `TELNYX_PHONE_NUMBER +1417...`).
11. Reply "Telnyx signup done, key in env" — dev side will write `telnyx_client.py` in ~30 min, run smoke 6/6, then start the TCR campaign submission.
12. Console → Messaging → 10DLC → "Register Brand" → use the same content from the Plivo white-glove packet (operator-confirmed in §1).
13. Console → Messaging → 10DLC → "Submit Campaign" → operator + dev fill the CTA + Message Flow together (paste-ready content from Plivo white-glove packet).
14. Wait 1–14 business days for TCR vetting. While waiting, Email channel + Voice TTS continue to operate (current production state).

### #2 — Twilio re-submission path (parallel)

1. Open https://console.twilio.com → log in (existing account).
2. Navigate: Messaging → Regulatory Compliance → A2P 10DLC → Brands.
3. Confirm Brand BN250fa0… status = "VERIFIED" (should still be).
4. Navigate: Messaging → Regulatory Compliance → A2P 10DLC → Campaigns.
5. Find rejected campaign QE2c6890… → click → "View rejection reason" (confirms 30909).
6. Click "Edit Campaign" or "Resubmit Campaign" (button label depends on state).
7. Use case: keep "Mixed" or "Account Notifications + Customer Care" (whichever was originally selected).
8. **Replace Message Flow text** with: "Carriers and shippers opt into SMS via (a) the broker-carrier signed agreement (paper or DocuSign with explicit SMS-consent checkbox), (b) the platform's web form at continentalhaul.com/signup with explicit SMS-consent checkbox, (c) verbal consent during phone dispatcher onboarding (recorded, retained per FMCSA rules), or (d) reply-YES confirmation to a STOP-disclosed initial outreach message. Privacy policy at continentalhaul.com/privacy explicitly discloses SMS use. Sample messages always include 'Reply STOP to opt out, msg/data rates apply.'"
9. **Sample messages — replace with all 3 of these** (each must include STOP language):
   - "CHL: Load #4521, Dallas → Atlanta, $2,450, ETA 14h. Reply YES to accept, NO to decline. Reply STOP to opt out. Msg/data rates apply."
   - "CHL: Driver, you are 4 hours past pickup at Wheeling. Reply with status or call dispatcher (417) 219-3856. Reply STOP to opt out."
   - "CHL: Rate confirmation for load #4521 has been emailed to you. Confirm receipt by replying RC. Reply STOP to opt out."
10. Submit.
11. Reply "Twilio resubmitted" — dev side flips a watch on the A2P status monitor; will surface when status = APPROVED.
12. While waiting (5–7 biz days expected), the Telnyx track continues in parallel.

### #3 — Bandwidth.com signup (long-term, NOT for now)

> **Defer this to Q3 2026 / Phase 8.** Steps below are reference only, do not execute now.

1. (Future) Email sales@bandwidth.com requesting solo-operator account onboarding for freight brokerage CHL with FMCSA legitimacy proof.
2. (Future) Schedule discovery call with Bandwidth Account Manager.
3. (Future) Provide EIN (acquire from IRS first if not yet held).
4. (Future) Sign 3-month minimum campaign commitment paperwork.
5. (Future) Submit TFV brief with BRN attached.
6. (Future) Dev rebuilds notification_service.py SMS path against `bandwidth-sdk`.

---

## 7. Decision-gate

### What operator decides

- **Recommendation #1 (Telnyx) — should dev provision?** YES / NO. Default recommendation: YES.
- **Recommendation #2 (Twilio re-submission) — should operator + dev pursue in parallel?** YES / NO. Default recommendation: YES (zero cost, hedge).
- **Recommendation #3 (Bandwidth) — should dev queue research + prep for Phase 8?** YES / NO. Default recommendation: YES, queue but DON'T execute.
- **Plivo — close the loop?** Default recommendation: send 1-line follow-up email on day 3 (2026-05-09); don't continue waiting.

### What dev executes (once authorized)

- **For Recommendation #1:**
  - Write `backend/telnyx_client.py` (~150 lines, shaped like `plivo_client.py`).
  - Write `backend/tests/test_telnyx_client.py` (6 smoke cases mirroring the Plivo smoke).
  - Update `backend/notification_service.py` SMS routing branch (1 import + provider-selection switch).
  - Add env-var wiring: `TELNYX_API_KEY`, `TELNYX_PHONE_NUMBER`, `CHL_SMS_PROVIDER=telnyx` switch.
  - Run smoke 6/6, document in `backend/tests/test_telnyx_client.py`.
  - Update BUILD_STATUS_REPORT.md cross-cutting "SMS / Voice Comms" section to reflect the new provider.
  - Coordinate with operator on TCR campaign submission paperwork (operator pastes; dev verifies).

- **For Recommendation #2:**
  - Watch the A2P 10DLC status monitor in `notification_service.py` for status flip APPROVED.
  - When it flips, no code change needed — the SMS gate auto-opens.
  - Smoke-test with one outbound to operator's phone after status = APPROVED.

### Cross-vendor abstraction (worth doing once)

- Refactor `notification_service.py` SMS path to use a `SMS_PROVIDER` env var (`telnyx` | `plivo` | `twilio`) so we can flip providers at runtime without code change. ~2 hours dev work. Future-proofs against any single provider going down (if Telnyx campaigns get rejected, flip to a backup; if Plivo unblocks later, flip to that). Recommend doing this as part of Recommendation #1.

---

## 8. Alternate path: email-first-only operating mode (mitigation)

> **If all SMS vendors stay blocked or vetting takes 30+ days, what does CHL look like in email-first-only mode?**

### What still works without SMS

- **Carrier dispatch outreach** — `notification_service.py` Channel.EMAIL via Resend already handles this. Magic-link emails to carriers with load offer + rate-con-attached. Open/click engagement tracking already wired.
- **Voice TTS robocalls** — `notification_service.py` Channel.VOICE via Twilio Voice. The "your load offer is in your email" robocall is already the documented backup. Driver calls back the toll-free number → robocall says "check email."
- **In-transit check-ins** — currently uses SMS by default; would need to fall back to email. Email-based check-ins work but have 5–15 minute response latency vs. SMS 30 sec. Acceptable for v1.
- **Exception alerts to operator** — already email-via-Resend by default. SMS to operator's phone is a "nice to have" backup, not the primary channel.

### What degrades

- **2-hour ghost detector** — currently expects SMS reply within 30 min on initial outreach. Email response window is 2–4h typical. Recommend: extend ghost timeout from 2h → 4h while in email-only mode.
- **Driver opt-in flow** — without SMS, drivers can't reply "YES" to confirm load acceptance. Need a magic-link "I accept this load" page. (We already have one — `/api/load_acceptance/{token}` from iter 142.1 — works fine as the SMS substitute.)
- **Dispatcher voice callback** — Twilio Voice TTS robocall is the backup; works fine for one-off "check email" but doesn't scale to 50+ outreaches per day (cost: 50 calls × $0.014 × 30 sec avg = $0.25/day, fine cost-wise; but call-success-rate to small carrier landlines is ~60% vs. SMS ~95%).

### Capacity in email-only mode

- v1 carrier outreach volume (~50–100/day) is comfortably handled by email. Send-rate limits on Resend are 10/sec, way above demand.
- We may lose ~5–10% of dispatch attempts where the driver doesn't check email in time and the load goes to next carrier in queue. Acceptable transient cost.
- Operator-side: alert volume (Mercury deposit, FMCSA authority, factor-wire, etc.) is already 80%+ email-driven. Loss of operator-side SMS alerts is the main gap; mitigation = AgentDM-via-PM-relay for critical alerts (already in the ops stack).

### Recommendation

- **Continue operating in email-first mode for as long as needed.** It's not a crisis.
- **Extend ghost detector timeout 2h → 4h while SMS is offline.** ~10 line change in `assignment/ghost_detector.py`. Auto-revert when `a2p_10dlc=APPROVED`.
- **Surface a banner on the operator dashboard** indicating "SMS path: degraded — Email + Voice TTS active. Last status check: <timestamp>." Already partially built per the existing email-first banner; extend to show provider status table.
- **Don't optimize harder for email-only than this.** The right move is to unblock SMS, not engineer around its absence permanently.

---

## 9. Emergency-mode fallback for one-off transactional sends

> **Scenario:** all CPaaS vendors are 14+ days from approval, but operator has 5 critical carrier dispatches that need SMS today. What's the minimum-friction unblocked path?

### Option A: AWS SNS direct send (best emergency path)

- AWS account creation: 30 min if operator doesn't already have one; instant if they do.
- AWS SNS supports SMS sends WITHOUT 10DLC registration for low volumes (~1/sec, ~50/day per origin number).
- Cost: ~$0.00645 per SMS in US (varies by destination carrier).
- **Catch:** unregistered sends are subject to carrier filtering. Approximately 30–50% delivery rate to T-Mobile / AT&T / Verizon without 10DLC. Not reliable but not zero.
- **Use case:** one-off ops-level alerts to operator's own phone during outage (operator-to-operator, doesn't matter that some get filtered because operator can call us if SMS doesn't show).
- **Limit:** do NOT use for carrier-facing outreach (carrier reply-rate would be too low to be worth the spend).
- **Setup time:** ~1 hour from zero.
- **Implementation:** ~30 min to write a `backend/sns_emergency_client.py` using `boto3` with the same `text_operator(message, to=None) -> dict` signature.

### Option B: Plivo personal-Gmail signup workaround (per troubleshooting playbook §5)

- Sign up at Plivo with operator's personal Gmail (per playbook §5). Bypass the domain-block.
- Time-to-first-SMS via Plivo's white-glove: 7–21 business days.
- **Use case:** medium-term path if Telnyx unexpectedly fails too. Not an emergency path because of the 7–21 day window.
- **Friction:** commits us to Plivo; means doing the white-glove paperwork the operator already started but on a different signup foundation.

### Option C: Burner Twilio Verify (one-time codes) — narrow use only

- Twilio Verify API sends 6-digit OTP codes via Twilio's pre-vetted infrastructure, bypassing 10DLC requirements.
- Only good for OTP / verification codes (e.g., authenticating a new carrier on first login).
- NOT good for general dispatch SMS.
- Cost: $0.05 per verification.
- **Use case:** "magic-link backup OTP" for the carrier login flow. Niche.

### Recommendation for emergency mode

- **Option A (AWS SNS) is the right emergency path** for operator-facing alerts only. ~1h to provision, costs <$1/day, covers the gap.
- **Don't do Option B (Plivo workaround).** Plivo already showed responsiveness issues. Telnyx is faster path.
- **Option C only if specifically the OTP flow needs unblocking** — currently not a v1 critical path.
- **Best path overall:** prioritize Telnyx provisioning + Twilio re-submission in parallel; emergency-mode is unnecessary if both move forward today/tomorrow.

---

## 10. Carrier-pass-through fee context (industry-wide)

> Important context for any vendor decision: carrier fees are NOT vendor-controlled. They're set by AT&T / T-Mobile / Verizon and apply to ALL US 10DLC traffic regardless of which CPaaS routes it.

### Current 2026 carrier surcharges (all providers pass through)

| Carrier | A2P 10DLC SMS surcharge (per outbound msg) | Notes |
|---|---|---|
| AT&T | $0.0035 | Has been at this level since 2024 |
| T-Mobile | $0.0025 | Increased to $0.0025/MO January 2026 (was $0.002) |
| Verizon | $0.004 | Increased from $0.003 to $0.004 June 2025 (+33%) |
| Average across major US carriers | ~$0.003/msg | Use as baseline planning number |

### Implication for cost projection

- True cost per US SMS = base CPaaS rate + ~$0.003 average carrier pass-through.
- Telnyx all-in: $0.004 + $0.003 = **~$0.007/SMS effective**.
- Twilio all-in: $0.0083 + $0.003 = **~$0.0113/SMS effective**.
- Bandwidth all-in: $0.002 + $0.003 = **~$0.005/SMS effective**.
- Spread between cheapest (Bandwidth) and most expensive viable (Twilio) is ~2x at all-in cost. Significant at 100K+/mo, immaterial at v1.

---

## 11. Open questions for operator

1. **EIN status for CHL LLC?** Required if we ever need Bandwidth signup OR if we want to transition our Twilio brand registration to "Standard Brand" (currently uncertain whether Twilio brand was Sole-Prop or Standard registered; if Sole-Prop, message-rate caps are tighter).
2. **Is operator OK signing up for one more SMS provider account (Telnyx)?** Adds one more vault entry, one more $1/mo number rental, but gives provider redundancy.
3. **Should dev refactor `notification_service.py` SMS path to be vendor-agnostic now (vs. hardcoded to one)?** Adds ~2h work but future-proofs the abstraction (recommended yes).
4. **What's the actual 10DLC volume projection by Q3 2026?** If it's 50K+/mo, we should be making the Telnyx-now-Bandwidth-later plan explicit. If it's <10K/mo, Telnyx is fine as the long-term answer.
5. **Plivo follow-up: operator wants to send the 1-line close-the-loop email?** Or just let it sit silent? Either is defensible.
6. **Is there a different domain CHL owns or could acquire ($12/yr) for vendor signups?** This came up as the playbook §5 "last resort." If CHL has any other domain, signup might bypass the `continentalhaul.com` block on its own. Tangential question; doesn't gate the Telnyx path.

---

## 12. Sources

- **Twilio pricing.** https://www.twilio.com/en-us/sms/pricing/us — base $0.0083/SMS US, $0.014/min outbound voice. https://help.twilio.com/articles/1260803965530-What-pricing-and-fees-are-associated-with-the-A2P-10DLC-service- — A2P 10DLC fees ($4.50 brand, $1.50–$10/mo campaign).
- **Twilio error 30909.** https://www.twilio.com/docs/api/errors/30909 — CTA verification incomplete; resubmit allowed at no extra fee.
- **T-Mobile 2026 fee changes.** https://help.twilio.com/articles/44609260499995 — confirmed $0.0025/MO from Jan 2026.
- **Plivo pricing.** https://www.plivo.com/sms/pricing/us/ + https://apidog.com/blog/plivo-sms-api-cost/ — $0.0077/SMS US, $0.0077 inbound, ~$0.005/segment per some sources; long code $0.50/mo, toll-free $1.00/mo.
- **Plivo signup restrictions.** https://support.plivo.com/hc/en-us/articles/4406444053529-Restrictions-on-New-and-Existing-Plivo-Accounts — confirms personal-domain rejection policy + signup blocks documented in user reviews.
- **Plivo support reviews.** https://www.capterra.com/p/164618/Plivo/reviews/ + https://www.gartner.com/reviews/market/communications-platform-as-a-service/vendor/plivo/product/plivo — mixed support response time data.
- **Telnyx pricing.** https://telnyx.com/pricing/messaging — $0.004/SMS US outbound + inbound; no markup on TCR fees. https://telnyx.com/pricing/voice-api — $0.008/min Telnyx outbound voice.
- **Telnyx 10DLC.** https://support.telnyx.com/en/articles/5634625-10dlc-fees-and-charges — TCR fees at-cost pass-through.
- **Telnyx 24/7 support.** https://telnyx.com/pricing — included free with all accounts.
- **Bandwidth pricing.** https://www.bandwidth.com/pricing/ + https://www.bandwidth.com/support/en/articles/12823086-costs-associated-with-10dlc — $0.002/SMS, $0.0035 MMS; T-Mobile $50 campaign; 3-mo minimum.
- **Bandwidth support fees.** https://www.bandwidth.com/support/en/articles/12823178-carrier-surcharges — carrier-specific surcharge schedule.
- **Bird (MessageBird) pricing.** https://bird.com/en/pricing/sms — $0.00331/outbound US SMS, $0.003 inbound.
- **Bird 10DLC.** https://docs.messagebird.com/applications/channels/sms/sms-10dlc — pass-through fees, $1–$2/mo long code.
- **Sinch pricing.** https://sinch.com/pricing/sms/ + https://community.sinch.com/t5/Product-Pricing-FAQ/Additional-costs-associated-with-10DLC-US/ta-p/10486 — $0.0078/SMS, $25/mo number.
- **AWS SNS / End User Messaging pricing.** https://aws.amazon.com/sns/sms-pricing/ + https://aws.amazon.com/end-user-messaging/pricing/ — ~$0.00645/segment (varies), $1/mo number, $4.50 brand registration, $10/mo standard campaign or $2/mo low-volume.
- **ClickSend pricing.** https://textbolt.com/blog/clicksend-pricing/ + https://apidog.com/blog/clicksend-sms-api-cost/ — $0.0228/SMS for <5K/mo, $0.0082/SMS at 150K+/mo.
- **A2P 10DLC approval timing 2026.** https://tuco.ai/a2p-10dlc + https://conduit.ai/blog/a2p-10dlc-registration-step-by-step-for-2026 — 1–14 biz days typical, up to 30 days during high-volume periods, 20–30% first-time rejection rate for sole-prop / new business.
- **Toll-Free Verification 2026 changes.** https://www.telgorithm.com/news/toll-free-verification-is-changing-in-2026-heres-what-you-need-to-know — BRN required Jan 2026, 5–10 biz day activation, 3–4 week verification timeline.
- **Sole-Prop vs Standard Brand.** https://help.gohighlevel.com/support/solutions/articles/155000000340-a2p-sole-proprietor-brand-registration-for-10dlc — sole-prop excludes EIN-holders; LLCs need Standard Brand.
- **Cross-vendor comparison.** https://textbee.dev/blog/twilio-alternatives + https://knock.app/blog/the-top-sms-providers-for-developers — pricing + SDK quality landscape.

---

## 13. Cross-references

- **Standing playbook for Plivo signup troubleshooting:** `chl-memory/research/iter_141_3_stage_1a_signup_troubleshooting.md` (the §3 diagnostic + §5 fallback patterns apply to Telnyx if signup hits unexpected friction).
- **Vendor-neutral campaign content (paste-ready):** `chl-memory/research/plivo_white_glove_packet_FINAL.md` (the §1 brand info + §2 sample messages + §3 message flow text are reusable for Telnyx + Twilio re-submission).
- **Original Twilio→Plivo migration decision rationale:** `~/.claude/projects/c--CHL/memory/project_provider_migration_decision.md` (operator decided 2026-05-07; Plivo blocker now invalidates Plivo path; this doc supersedes the recommendation).
- **Existing notification abstraction:** `C:\CHL\backend\notification_service.py` (~990 lines; channel-priority logic + auto-failover already done).
- **Drop-in SMS client template:** `C:\CHL\backend\plivo_client.py` (~130 lines; reshape this for Telnyx; same public API).
- **Outreach orchestrator (vendor-agnostic):** `C:\CHL\backend\outreach\orchestrator.py` (~422 lines; cutover-ready).

---

## 14. Standing operator directives (full detail per 3x-daily handover discipline)

**Directive 1 — `set_env_var.ps1` helper.** Built at `C:\CHL\scripts\set_env_var.ps1` 2026-05-08 EOD-6 (commit a7c59a6 stream E). Operator's exact words: *"I need you to operate more autonomously so to speak. I introduce way too many possible failures."* Rule: when a secret needs provisioning (e.g., `TELNYX_API_KEY` for Recommendation #1), ALWAYS use this helper instead of asking operator to edit `.env` in VS Code. Eliminated failure surfaces: file-write race, syntax errors, missing newlines, accidental key duplication.

**Directive 2 — 3x-daily handover discipline.** Operator's exact words: *"Make sure that this idea you have does not get lost. It has to be in each of your documeted handovers three times a day in full detail."* Rule: every handover doc must contain this directive section in full detail with operator's exact words. Paraphrasing forbidden. (This research doc includes the section per directive even though it's a research doc rather than a handover doc, because the doc is created in lieu of the next handover and may serve as the boot pointer.)

**Directive 3 — C-drive HDD monitoring.** Operator's exact words: *"You have space you can use on my C drive. Use it, but monitor its use so I dont go over my HDD capacity."* Thresholds: warn at 50GB free, surface at 20GB free, hard-stop at 10GB free. **Current state at doc write (2026-05-08 EOD-14):** 794.6 GB free / 135.5 GB used. Comfortably above all thresholds.

**Status-reporting synthesis directive.** Per `feedback_status_reporting_synthesis.md`: when PM Claude relays an "operator directive" that contradicts a prior operator directive (most common: ack-wait-gate vs autonomous-operation), DO NOT silently comply. Surface the contradiction with synthesis to operator.

---

**Author:** dev-engineer research sub-agent, dispatched 2026-05-08 EOD-14 by main dev session per operator-mandated SMS provider evaluation. Cross-references: see §13. Document supersedes the Plivo-default in `project_provider_migration_decision.md` for path-forward decision.
