# A2P 10DLC Campaign Brief — Continental Haul Logistics LLC
**For: Twilio Trust Hub Support · Carrier Escalation Request**
*Prepared: Iteration 80.3 · Copy/Paste directly into the Twilio ticket*

---

## Paragraph 1 — Who We Are & What This Number Does

Continental Haul Logistics LLC (MC 1817555 · USDOT 4569843) is a federally
registered freight brokerage operating a private carrier network across the
continental United States. The number `+1 816-560-9180` (operator personal — replaced 2026-05-08; new business: TF +1-866-490-6433 / local +1-417-219-3856) is **not a marketing
line**. It is a dispatch-operations endpoint used exclusively to send
**safety-critical and load-operations notifications** to a closed list of
verified, onboarded owner-operators and W-9-contracted carriers who have
explicitly opted in by signing a Carrier Packet and/or scanning our
QR-Onboard card at dispatch. We are moving 80,000-pound commercial vehicles
across interstate lanes in real time; the difference between a 45-second
text and a 10-minute phone call is the difference between a safe re-route
and a DOT violation or a rollover.

## Paragraph 2 — Message Taxonomy (Safety-Ops Only)

Every outbound message from this long code falls into one of **four
operational categories**:

| Category | Example content | Frequency |
|---|---|---|
| **Load Assignment** | "Load CHL-1842 assigned. Pickup Omaha NE 04/24 0800. Reply Y to accept." | On-demand |
| **Route & Safety Alert** | "Severe weather on I-80 MP 211–248. Re-route via US-30 advised." | On-demand, infrequent |
| **Compliance / Expiry** | "Heads up: your COI expires 2026-05-12. Upload renewal at /qr-load/RENEW." | T-14 before expiry |
| **Emergency Contact** | "DISPATCH URGENT: call 816-560-9180 regarding Load CHL-1842." | Rare, manual |

There are **no promotional messages, no lead generation, no third-party
marketing, and no broadcast campaigns**. Every recipient is a contracted
carrier whose MC number, insurance, and W-9 are already on file in our
compliance vault (`/app/storage/compliance/`). Expected monthly volume
is **sub-500 messages** across our entire active fleet of under 50
owner-operators. We have full opt-in records, full HELP/STOP handling,
and an append-only audit log (`db.load_comms_history`) recording every
message dispatched.

## Paragraph 3 — Request to Expedite

Our A2P 10DLC Brand was submitted with EIN `XX-XXXXXXX` (exact IRS name:
"Continental Haul Logistics LLC") but is currently blocked at the
TCR/brand-verification step on an EIN/legal-name match issue that we
have now reconciled with the IRS. We respectfully request that Twilio
Trust Hub **re-run the EIN verification** and **unblock carrier routing
on long-code `+1 816-560-9180`**. If formal campaign classification is
required we are requesting the **Low-Volume Mixed** campaign type under
our registered Private-for-Profit brand, with the use case labeled
**"Account Notifications — Freight Operations & Safety."** We are
willing to share our carrier onboarding packet, driver opt-in consent
flow screenshots, and sample message taxonomy on request. Given that we
are currently routing safety-critical dispatch through `tel:` fallback
dialers, any delay directly affects our DOT compliance posture.

Thank you for the fast turn.

---

**Contact**
- Owner / Director: Jason Andrews
- Phone: 816-560-9180 (the number in question)
- Email: <REDACTED-EMAIL>
- MC: 1817555 · USDOT: 4569843
- Operating authority status: Active

*— End of Brief —*
