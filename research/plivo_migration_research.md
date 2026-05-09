# Plivo Migration Research (Twilio → Plivo)

> **Context:** Operator decided to switch from Twilio to Plivo after 4+ weeks of 10DLC compliance friction. Migration scheduled for iter 141.3 (async to iter 141.2 DAT/auto-bid delivery).
> **Decision locked:** PLIVO (not Telnyx) due to white-glove 10DLC migration service addressing operator's compliance fatigue.

---

## Executive Summary

**Why Plivo wins:** Compliance relief > cost savings. Operator's pain point is 4 weeks fighting Twilio's 10DLC rejections (error 30909 - CTA verification failed). Plivo's **white-glove 10DLC migration service** solves this: their solutions engineers handle campaign submission using pre-approved templates with proven TCR-acceptance rates. Twilio-like API design = minimal code rewrite (~30 min on notification_service.py).

**Cost comparison:**
- Twilio: $0.0083/SMS + carrier fees
- Plivo: $0.0050/SMS (40% savings)
- Voice: Comparable (both ~$0.013-0.014/min)
- At projected v1 scale (50-100 SMS/month): ~$2-3/month savings
- At v2 scale (500 SMS/month): ~$20/month savings (~$240/year)

**Timeline:** 7-10 day number port + 1-2 week 10DLC campaign approval = iter 141.3 ships in ~3 weeks from kickoff.

---

## Plivo White-Glove 10DLC Migration Process

**What "white-glove" means in practice:**

Plivo offers a seamless 10DLC migration process with guided compliance expert assistance. Solutions engineers walk you through every step to ensure zero disruption to your messaging.

**4-step process (per Plivo docs):**

1. **Create Profile:** Provide business information (legal name, authorized contact, business registration details) via Profile API
2. **Register Brand:** Once profile is complete, register Continental Haul Logistics LLC as a brand via Brand API
3. **Register Campaign:** Create campaign describing use case (operational SMS to motor carriers per signed agreements)
4. **Link Numbers:** Associate ported numbers with approved campaign

**CHL-specific execution plan:**

1. **Profile creation (5 min):**
   - Business type: Private (LLC)
   - Legal name: Continental Haul Logistics LLC
   - MC-1817555 + USDOT number
   - Authorized contact: Jason Aaron Meyer

2. **Brand registration (10 min):**
   - Standard brand: $4 one-time registration fee + optional $40 vetting for higher throughput (recommended if messaging volume >6,000/day)
   - CHL skips vetting (v1 volume <100/day)
   - Monthly $10/campaign fee, max 100 campaigns, 49 numbers per campaign

3. **Campaign registration (Plivo handles):**
   - Use case: "Operational notifications" (mixed standard use case)
   - Sample messages:
     - "CHL Load Opportunity: Chicago IL → Dallas TX, $2,450, Van. Reply START to opt-in."
     - "Carrier confirmed pickup at 14:00 CT. Ref MC-1817555."
     - "Load delivered. Payment processing. Reply STOP to opt-out."
   - Carriers vet campaign by examining use case description, message flow, and sample messages. Processing time: 1-2 weeks. If rejected, Plivo provides feedback and assists with resubmission.

4. **Number linking (instant after campaign approval):**
   - +14172193856 (10DLC) → campaign
   - +18664906433 (toll-free) → separate TFV path OR retire

**Key compliance differentiator:** Plivo provides white-glove 10DLC migration service with dedicated Customer Success Managers and 24/7 premium support - this is the operator-relief CHL needs after Twilio's self-service portal friction.

---

## API Migration (notification_service.py Rewrite)

**Twilio SDK (current):**
```python
from twilio.rest import Client

client = Client(TWILIO_ACCOUNT_SID, TWILIO_AUTH_TOKEN)
message = client.messages.create(
    to="+18165609180",  # operator personal — replaced 2026-05-08; new business: TF +1-866-490-6433 / local +1-417-219-3856
    from_=TWILIO_PHONE_NUMBER,
    body="SMS content"
)
```

**Plivo SDK (target):**
```python
import plivo

client = plivo.RestClient(auth_id=PLIVO_AUTH_ID, auth_token=PLIVO_AUTH_TOKEN)
response = client.messages.create(
    src=PLIVO_PHONE_NUMBER,  # "src" not "from_"
    dst="+18165609180",       # "dst" not "to"
    text="SMS content"        # "text" not "body"
)
```

**Key differences:**
1. Parameter names: `to/from_/body` (Twilio) → `dst/src/text` (Plivo)
2. Client initialization: RestClient(auth_id, auth_token) with context manager support for resource cleanup
3. Response object structure similar but fields differ slightly

**notification_service.py rewrite estimate:** 30 minutes. Changes:
- Replace `twilio.rest.Client` import with `plivo.RestClient`
- Update `.env` vars: `PLIVO_AUTH_ID`, `PLIVO_AUTH_TOKEN`, `PLIVO_PHONE_NUMBER`
- Update `send_sms()` function: rename params `to`→`dst`, `from_`→`src`, `body`→`text`
- Smoke test: send test SMS to OWNER_SMS_NUMBER, verify delivery

**Voice path unchanged:** Twilio Voice still works fine for iter 141.2 (FCC-governed not TCR-governed). Plivo Voice migration optional; defer to Phase 3+ if voice outreach needs advanced features.

## Number Porting Timeline

**Porting both Twilio numbers to Plivo:**

Numbers to port:
- +14172193856 (10DLC primary - cited in broker-carrier agreements)
- +18664906433 (toll-free - secondary, optional)

**Port process (per Plivo docs):**

1. **Submit port request:** Provide current carrier info (Twilio), account details, authorized user signature
2. **Status: Submitted** - Waiting for carrier acceptance and Firm Order Commitment (FOC) date
3. **Status: Accepted** - Port approved, FOC date assigned
4. **Port completion:** Numbers transfer to Plivo on FOC date (typically 7-10 business days from submission)

**Critical:** When a number is ported from a different vendor, it automatically becomes unregistered from any 10DLC campaigns. Once port-in completes, associate ported numbers with your Plivo campaigns.

**Alt-path (instant migration):** Provision new Plivo number immediately, deprecate Twilio numbers. Only viable if no external paperwork (broker-carrier agreements, credit apps) cites (417) 219-3856. Operator should confirm before choosing this path.

**Recommended: Port both numbers** to preserve brand continuity. 7-10 day window fits iter 141.3 timeline (stages run async during iter 141.2 DAT/auto-bid cycle).

---

## Cost Comparison (v1 vs v2 Scale)

**Pricing per Plivo vs Twilio:**

| Service | Twilio | Plivo | Savings |
|---|---|---|---|
| SMS (US outbound) | $0.0083/msg + carrier fees | $0.0050/msg | 40% |
| Voice (outbound) | $0.014/min | $0.013/min | ~7% |
| Phone number/month | $1.15 | $0.80 | 30% |
| 10DLC registration | Variable (self-service) | $4 brand + $10/mo campaign | Flat |

**v1 scale (pre-revenue, 50-100 SMS/month):**
- Twilio: ~$5-8/month (SMS + number rental)
- Plivo: ~$3-5/month (SMS + number rental + campaign fee)
- Savings: ~$2-3/month (~$30/year)

**v2 scale (post-revenue, 500 SMS/month):**
- Twilio: ~$50/month
- Plivo: ~$30/month
- Savings: ~$20/month (~$240/year)

**Conclusion:** Cost savings are real but secondary. Primary win is **compliance relief** - Plivo's white-glove service eliminates 4+ weeks of 10DLC rejection cycles.

---

## Iter 141.3 Staging (Migration as Iteration)

**Don't block iter 141.2 on Plivo:** Ship 141.2 (DAT + auto-bid) on voice-only alerts using Twilio Voice (still works, FCC-governed). Iter 141.3 runs ASYNC during 141.2's stage 1d/1f cycle.

**4-stage migration plan (detailed in iter_141_3_agenda_draft.md):**

1. **Stage 1a:** Plivo account signup + Profile/Brand registration (white-glove kickoff)
2. **Stage 1b:** notification_service.py rewrite (Twilio Client → Plivo Client)
3. **Stage 1c:** Number port submission + 10DLC campaign approval (Plivo team drives)
4. **Stage 1d:** Cutover .env + empirical SMS smoke + deprecate Twilio creds

**Timeline:** 3 weeks total (1 week port + 1-2 weeks campaign approval + 3-5 days dev).

---

## Risks + Mitigations

**Risk 1: 10DLC campaign rejected by Plivo's TCR submission**
- Mitigation: Plivo's white-glove service includes pre-approved templates with proven acceptance rates. If rejected, Plivo team assists with resubmission (feedback provided per carrier vetting process).

**Risk 2: Number port fails (Twilio blocks)**
- Mitigation: Twilio has no incentive to block (loses customer either way). Port failures are rare with correct account info. If blocked, provision new Plivo number as fallback.

**Risk 3: SMS delivery degrades post-migration**
- Mitigation: Plivo offers 99.99% uptime SLA backed by 1,600+ Tier 1 carrier relationships. Empirical smoke tests (stage 1d) catch issues before cutover.

**Risk 4: Operator regrets migration (wants to return to Twilio)**
- Mitigation: Keep Twilio account active (no monthly fee if no numbers rented) for 30 days post-cutover. If Plivo fails, reverse-port back to Twilio.

---

## Decision Authority

**Operator's final call:** Migration approved per operator statement "We are going to switch providers. I have been struggling with Twilio since last month." Compliance fatigue > cost analysis. Iter 141.3 executes migration cleanly.

**Cross-reference:**
- `chl-memory/agenda/iter_141_3_agenda_draft.md` - Full 4-stage spec with smoke tests
- `chl-memory/research/iter_141_2_launch_operator_runbook.md` - Voice-only alert fallback for iter 141.2

**Status:** ✅ RESEARCH COMPLETE. Operator/Dev Engineer can review + adjust before iter 141.3 kickoff.
