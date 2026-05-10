# Telnyx Toll-Free Verification — saved field values for resubmission

**Created 2026-05-10 ~14:55 UTC** — initial submit blocked because Hosted SMS for `+18664906433` not yet provisioned (we submitted Hosted SMS 30 min earlier; Telnyx needs ~24-48h to provision before TFV can reference the number).

**Action:** wait 24-48h, return to portal.telnyx.com → Messaging Suite → Toll-Free Compliance → re-do the form using the values below.

---

## Section 1 — Business Details

| Field | Value |
|---|---|
| Business Name | `Continental Haul Logistics LLC` |
| Corporate Website | `https://continentalhaul.com` |
| Legal form | `Private Company` |
| DBA or brand name | (blank) |
| Business Registration Number | `42-2003434` |
| Business Registration Type | `EIN (US)` |
| Business Registration Issuing Country | `United States of America` |
| First Name | `Jason` |
| Last Name | `Meyer` |
| Email | `dispatch@continentalhaul.com` |
| Phone Number | `+1 816 560 9180` |
| Status update webhook URL | (blank) |

## Section 1 — Address

| Field | Value |
|---|---|
| Address | `618 West Greenwood Street` |
| Extended Address | (blank) |
| City | `SPRINGFIELD` |
| State | `MO - Missouri` |
| ZIP | `65807` |
| Country | `United States of America` |

## Section 2 — Assign Numbers

- Tab: **Hosted Numbers**
- Number: `+18664906433` (type: tollfree)
- Action: click **Assign**

## Section 3 — Use Case Details

| Field | Value |
|---|---|
| Expected messaging volume per month | `10,000` |
| Use-case | `Mixed` |
| Summarize use-case | (see block A below) |
| Message content | (see block B below) |
| Opt-In workflow | (see block C below — 423 chars) |
| Additional use-case details | (see block D below — 470 chars) |
| Opt-In workflow image URL | `https://continentalhaul.com/opt-in` |
| Opt in keywords | `START, UNSTOP, YES` |
| Opt in message | (see block E below) |
| Help message | (see block F below) |
| Privacy policy URL | `https://continentalhaul.com/privacy` |
| Terms and conditions URL | `https://continentalhaul.com/terms` |
| ISV Reseller | (blank) |
| Age-Gated Content | `No` |

### Block A — Summarize use-case
```
B2B carrier-dispatch SMS for Continental Haul Logistics LLC, a federally-registered freight broker (MC-1817555 / USDOT 4569843). Messages coordinate active freight loads with contracted owner-operators and motor carriers — load offers, signed rate confirmations, pickup/delivery reminders, status updates, payment notifications. No marketing, no consumer messaging, no age-gated content.
```

### Block B — Message content
```
CHL Freight: New load — Dallas TX to Atlanta GA, 53' Dry Van, $2,400 line haul. Tap to review & accept: https://continentalhaul.com/offer/Bf7xK9pQr — Reply STOP to opt out, HELP for support.
```

### Block C — Opt-In workflow (423 chars)
```
Three opt-in paths at https://continentalhaul.com/opt-in: (1) Web - offer page shows TCPA disclosure above Accept Load; tapping Accept = express written consent, timestamped with IP/user-agent/GPS. (2) Verbal - dispatchers read disclosure during recorded onboarding calls. (3) Keyword - opted-out carriers rejoin via START/UNSTOP/YES. STOP/STOPALL/CANCEL/END/QUIT/UNSUBSCRIBE trigger immediate opt-out + final confirmation.
```

### Block D — Additional use-case details (470 chars)
```
Continental Haul Logistics LLC is a federally-registered freight broker (MC-1817555 / USDOT 4569843, BMC-84 bond on file) arranging truckload freight between U.S. shippers and contracted motor carriers. No trucks owned, no drivers employed, no consumer messaging. +1-866-490-6433 is the B2B dispatch line for active loads with owner-operators and fleet dispatchers in established business relationships with CHL. Strictly transactional. ~3-7 SMS per active carrier/week.
```

### Block E — Opt in message
```
Continental Haul Logistics: You are opted in for load-dispatch, rate-confirmation, status-update, and payment SMS. Msg & data rates may apply. Msg frequency varies by load volume. Reply STOP to opt out, HELP for support.
```

### Block F — Help message
```
Continental Haul Logistics dispatch. Questions? dispatch@continentalhaul.com or +1-866-490-6433. Msg & data rates may apply. Reply STOP to opt out.
```

---

## Blocking error captured 2026-05-10 14:55 UTC

```
+18664906433 not found
+18664906433 is not yet provisioned
```

This is the Hosted SMS provisioning lag — number was added to Hosted SMS order `80019e12-4300-4c1f-ad67-f58c71e26380` at ~14:35 UTC (20 min before TFV attempt). Provisioning takes Telnyx 24-48h (sometimes faster).

## Re-submission cadence

- **2026-05-11 ~15:00 UTC** — first retry attempt
- **2026-05-12 ~15:00 UTC** — second retry if first fails
- **2026-05-13 ~15:00 UTC** — escalate to support@telnyx.com if still blocked

Once submitted, Telnyx TFV review takes 3-5 business days. Approval not required before 2026-05-14 FOC — TFV is the messaging-use-case gate, separate from the porting itself. Hosted SMS clearance + the v2 LOA on the porting order are what matter for FOC.

## Cross-references

- `chl-memory/operations/telnyx_port_action_items_2026_05_09.md` — porting order status
- Canonical packet source: `C:/CHL/memory/TOLLFREE_RESUBMISSION.md`
