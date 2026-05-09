
## Triage run -- 2026-05-09T02:20:09.163784+00:00
**Window**: last 75 min (since 2026-05-09T01:05:09.046963+00:00). **Seen**: 3. **Classified**: 3. **Urgent**: 0. **Skipped (dup)**: 0.
**By category**: TELNYX=3

### URGENT (0)
(none)

### OPERATOR_REVIEW (3)
- [TELNYX] conf=0.90 from=portal@telnyx.com subject='sr_b98ea4 - Port In Status Has Changed' message_id=<20260509011928.031dccae2cf89102@mg.telnyx.com>
    - sender_domain_match:telnyx=telnyx.com
    - default_no_urgent_signal:operator_review
- [TELNYX] conf=0.90 from=portal@telnyx.com subject='sr_edc875 - Port In Status Has Changed' message_id=<20260509012335.991ae881706a7b81@mg.telnyx.com>
    - sender_domain_match:telnyx=telnyx.com
    - default_no_urgent_signal:operator_review
- [TELNYX] conf=0.90 from=portal@telnyx.com subject='sr_edc875 - New comment on your porting order for Jason Meyer.' message_id=<20260509012337.25dce145de06c22c@mg.telnyx.com>
    - sender_domain_match:telnyx=telnyx.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-09T02:30:58.887233+00:00
**Window**: last 75 min (since 2026-05-09T01:15:58.861906+00:00). **Seen**: 3. **Classified**: 0. **Urgent**: 0. **Skipped (dup)**: 3.

### URGENT (0)
(none)

### OPERATOR_REVIEW (3)
- [TELNYX] conf=0.90 from=portal@telnyx.com subject='sr_b98ea4 - Port In Status Has Changed' message_id=<20260509011928.031dccae2cf89102@mg.telnyx.com>
    - sender_domain_match:telnyx=telnyx.com
    - default_no_urgent_signal:operator_review
- [TELNYX] conf=0.90 from=portal@telnyx.com subject='sr_edc875 - Port In Status Has Changed' message_id=<20260509012335.991ae881706a7b81@mg.telnyx.com>
    - sender_domain_match:telnyx=telnyx.com
    - default_no_urgent_signal:operator_review
- [TELNYX] conf=0.90 from=portal@telnyx.com subject='sr_edc875 - New comment on your porting order for Jason Meyer.' message_id=<20260509012337.25dce145de06c22c@mg.telnyx.com>
    - sender_domain_match:telnyx=telnyx.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 04:30 UTC
**Window**: last 75 min (since 2026-05-09T03:15:03 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)
**Note**: deep-night window; last fetched_at was 2026-05-09T01:25 UTC (Telnyx sr_edc875 porting comment + 2 status-changed notifications), already triaged + actioned earlier in session — reply posted to Telnyx Porting API as comment `2b36e9f8`. Operator asleep per "Going to sleep" 2026-05-09 ~03:40 UTC. IMAP poller appears idle (no fetched_at since 01:25 UTC ≈ 3h ago — consistent with empty Zoho INBOX, not poller failure; would investigate if gap extends past 6h).

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 05:25 UTC
**Window**: last 75 min (since 2026-05-09T04:10 UTC).
**Total ingested last 75min**: 1
**By category**: SHIPPER_CARRIER_OR_OTHER=1
**By verdict**: OPERATOR_REVIEW=1

### URGENT (0)
(none)

### OPERATOR_REVIEW (1)
- [mailer-daemon@mail.zoho.com] **Undelivered Mail Returned to Sender** -- 550 5.7.1 "This mailbox is no longer active". Unknown which recipient bounced (body parser couldn't extract Final-Recipient cleanly from the Zoho bounce format). Recommended action: operator opens dispatch@continentalhaul.com Inbox view tomorrow morning, finds the bounce, identifies the dead recipient, prunes from any contact list it came from. Low urgency -- bounces are routine, but worth eyeballing in case it's a partner/vendor/customer we expected to reach.

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-09T05:29:56.974227+00:00
**Window**: last 75 min (since 2026-05-09T04:14:56.959874+00:00). **Seen**: 1. **Classified**: 1. **Urgent**: 0. **Skipped (dup)**: 0.
**By category**: VENDOR=1

### URGENT (0)
(none)

### OPERATOR_REVIEW (1)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0b0d0b42.6ef28a61-73435860.6817904581010161565@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 06:25 UTC
**Window**: last 75 min (since 2026-05-09T05:10 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)
**Note**: deep-night window continues. Last fetched_at was 2026-05-09T05:25 UTC (the Zoho mailer-daemon bounce, already logged in prior cycle).

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 07:25 UTC
**Window**: last 75 min (since 2026-05-09T06:10 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)
**Note**: deep-night window. Last fetched_at remains 2026-05-09T05:25 UTC (Zoho mailer-daemon bounce; ~2h ago). No fresh inbound.

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 08:25 UTC
**Window**: last 75 min (since 2026-05-09T07:10 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)
**Note**: deep-night window. Last fetched_at remains 2026-05-09T05:25 UTC (~3h ago). No fresh inbound.

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 09:25 UTC
**Window**: last 75 min (since 2026-05-09T08:10 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)
**Note**: deep-night window. Last fetched_at remains 2026-05-09T05:25 UTC (~4h ago). No fresh inbound — consistent with operator-asleep + empty Zoho INBOX, not poller failure (would investigate at >6h gap).

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 10:26 UTC
**Window**: last 75 min (since 2026-05-09T09:11 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)

**Poller sanity check (because latest fetched_at was 04:48 UTC = ~5.6h ago, near the 6h investigate threshold):**
- `/api/health/system` reports `inbound_email_poller` HEALTHY — `last_run_at: 2026-05-09T10:23:44 UTC` (~2 min ago), `last_run_status: success`, `last_run_messages_ingested: 0`, `loop_running: true`, `password_present: true`.
- Cursor's `last_poll_at: 04:48 UTC` only updates when cursor advances (UID 78 hasn't moved). Misleading-named field; not an outage.
- `polled_folders: ['INBOX']` — production is still on pre-VV3 code. Multi-folder polling (Newsletter/Promotions/Updates/Forums/Social/Spam) activates after operator restart-verifies the 27-commit chain per CCC1 runbook on May 14.
- Conclusion: Zoho INBOX is genuinely empty. No action needed.

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 11:26 UTC
**Window**: last 75 min (since 2026-05-09T10:11 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)
**Note**: poller still healthy per prior cycle's check (last_run_at within last few minutes; messages_ingested:0). Zoho INBOX genuinely empty.

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 12:25 UTC
**Window**: last 75 min (since 2026-05-09T11:10 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)
**Note**: still no inbound. Latest fetched_at remains 2026-05-09T05:25 UTC bounce. Poller verified healthy in 10:26 cycle; INBOX genuinely empty.

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 13:49 UTC
**Window**: last 75 min (since 2026-05-09T12:34 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 14:57 UTC
**Window**: last 75 min (since 2026-05-09T13:42 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 15:33 UTC
**Window**: last 75 min (since 2026-05-09T14:18 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 16:35 UTC
**Window**: last 75 min (since 2026-05-09T15:20 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 17:39 UTC
**Window**: last 75 min (since 2026-05-09T16:24 UTC).
**Total ingested last 75min**: 0
**By category**: (none)
**By verdict**: (none)

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK / INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 18:26 UTC

Total ingested last 75min: 0
By category: (none)
By verdict: (none)

### URGENT (require operator attention)
- (none)

### OPERATOR_REVIEW (next session)
- (none)

### AUTO_ACK_OK / INFORMATIONAL (logged for completeness)
- (none)


## Hourly review 2026-05-09 19:26 UTC

Total ingested last 75min: 0
By category: (none)
By verdict: (none)

### URGENT (require operator attention)
- (none)

### OPERATOR_REVIEW (next session)
- (none)

### AUTO_ACK_OK / INFORMATIONAL (logged for completeness)
- (none)


## Hourly review 2026-05-09 20:25 UTC

Total ingested last 75min: 0
By category: (none)
By verdict: (none)

### URGENT (require operator attention)
- (none)

### OPERATOR_REVIEW (next session)
- (none)

### AUTO_ACK_OK / INFORMATIONAL (logged for completeness)
- (none)


## Triage run -- 2026-05-09T20:51:17.247930+00:00
**Window**: last 75 min (since 2026-05-09T19:36:17.223373+00:00). **Seen**: 1. **Classified**: 1. **Urgent**: 0. **Skipped (dup)**: 0.
**By category**: SHIPPER_CARRIER_REPLY=1

### URGENT (0)
(none)

### OPERATOR_REVIEW (0)
(none)

### AUTO_ACK_OK (1)
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-7E516556 · Broker Admin' message_id=<19e0e6fe8bd.3040df53-1439717126.-4134513613164662778@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-09T21:09:57.098907+00:00
**Window**: last 75 min (since 2026-05-09T19:54:57.085907+00:00). **Seen**: 2. **Classified**: 1. **Urgent**: 0. **Skipped (dup)**: 1.
**By category**: VENDOR=1

### URGENT (0)
(none)

### OPERATOR_REVIEW (1)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0e8d73d6.7c5e523e929340954.-9076187813155615863@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (1)
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-7E516556 · Broker Admin' message_id=<19e0e6fe8bd.3040df53-1439717126.-4134513613164662778@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-09T21:13:18.140404+00:00
**Window**: last 75 min (since 2026-05-09T19:58:18.131152+00:00). **Seen**: 2. **Classified**: 0. **Urgent**: 0. **Skipped (dup)**: 2.

### URGENT (0)
(none)

### OPERATOR_REVIEW (1)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0e8d73d6.7c5e523e929340954.-9076187813155615863@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (1)
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-7E516556 · Broker Admin' message_id=<19e0e6fe8bd.3040df53-1439717126.-4134513613164662778@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 21:25 UTC

Total ingested last 75min: 0
By category: (none)
By verdict: (none)

### URGENT (require operator attention)
- (none)

### OPERATOR_REVIEW (next session)
- (none)

### AUTO_ACK_OK / INFORMATIONAL (logged for completeness)
- (none)


## Triage run -- 2026-05-09T21:46:01.968897+00:00
**Window**: last 75 min (since 2026-05-09T20:31:01.915919+00:00). **Seen**: 22. **Classified**: 20. **Urgent**: 0. **Skipped (dup)**: 2.
**By category**: VENDOR=20

### URGENT (0)
(none)

### OPERATOR_REVIEW (21)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0e8d73d6.7c5e523e929340954.-9076187813155615863@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eaae9c3.11981cd1-436049718.-63981451029455321@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab2844.121dc3e4-1323924103.-897201302599066891@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab2fc5.5e2313ee1577036340.2352289053463846238@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab3c15.24cf6399-88112623.-565286828077602810@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab95ac.6c28ba58-1623363380.-3380839433630098889@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eaba157.6c9f3481-1067366662.-8233230213846990024@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eaba92f.314d94911793166104.-247977468496996118@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eabad92.3f510c5f-1497776301.-2043669024001327045@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eabb8e4.637def32-1673446040.-4794587228922865460@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb1c96c.c833628535964756.-184445179369330886@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb2389f.8328ddef-758871206.2480902411926125557@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb236d5.31d465f91596277470.5443159372372314541@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb23eb2.26481118-606425205.5908644855288867154@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb24097.1afbdc9a-1224703121.-6467075774362191181@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb246d7.3fde6a78514532916.6494719236628437307@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb248bc.3f2855f2-1106575814.5646047528956751224@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb2503b.4069ae1c-779517579.4488544516193620720@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb24e8a.7a55cc11-909000398.7040424116467661680@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb25665.6ea0db291832469826.-5152789836004857315@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb25841.6aae68f5-1801372923.-8901591147042622331@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (1)
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-7E516556 · Broker Admin' message_id=<19e0e6fe8bd.3040df53-1439717126.-4134513613164662778@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-09T21:51:54.061959+00:00
**Window**: last 75 min (since 2026-05-09T20:36:54.024972+00:00). **Seen**: 32. **Classified**: 11. **Urgent**: 0. **Skipped (dup)**: 21.
**By category**: VENDOR=11

### URGENT (0)
(none)

### OPERATOR_REVIEW (32)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0e8d73d6.7c5e523e929340954.-9076187813155615863@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eaae9c3.11981cd1-436049718.-63981451029455321@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab2844.121dc3e4-1323924103.-897201302599066891@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab2fc5.5e2313ee1577036340.2352289053463846238@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab3c15.24cf6399-88112623.-565286828077602810@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab95ac.6c28ba58-1623363380.-3380839433630098889@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eaba157.6c9f3481-1067366662.-8233230213846990024@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eaba92f.314d94911793166104.-247977468496996118@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eabad92.3f510c5f-1497776301.-2043669024001327045@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eabb8e4.637def32-1673446040.-4794587228922865460@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb1c96c.c833628535964756.-184445179369330886@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb2389f.8328ddef-758871206.2480902411926125557@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb236d5.31d465f91596277470.5443159372372314541@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb23eb2.26481118-606425205.5908644855288867154@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb24097.1afbdc9a-1224703121.-6467075774362191181@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb246d7.3fde6a78514532916.6494719236628437307@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb248bc.3f2855f2-1106575814.5646047528956751224@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb2503b.4069ae1c-779517579.4488544516193620720@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb24e8a.7a55cc11-909000398.7040424116467661680@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb25665.6ea0db291832469826.-5152789836004857315@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb25841.6aae68f5-1801372923.-8901591147042622331@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb73238.41478400-528421159.-8475225395084136890@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb78078.6370102a1355465754.-7311017506290584149@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb78254.187c6459-1589090914.4802124068900982123@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb78a1f.657bc523-1420467910.-8351921456066359854@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb7884e.41cd5205-270833881.5113486711436024262@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb79025.182beb6f-1276514707.2308073938353045807@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb791e8.6c500008-197415915.-1384543892383353476@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb79815.37de37bc-914191669.-2473801621104228406@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb799d3.306e72821832423497.7474411629237136530@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb7a011.594f03681519680271.-2398460807378805043@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb7a1cb.5094cf12683578264.6024856651230854439@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 22:25 UTC

Total ingested last 75min: 0
By category: (none)
By verdict: (none)

### URGENT (require operator attention)
- (none)

### OPERATOR_REVIEW (next session)
- (none)

### AUTO_ACK_OK / INFORMATIONAL (logged for completeness)
- (none)


## Triage run -- 2026-05-09T22:27:49.221297+00:00
**Window**: last 75 min (since 2026-05-09T21:12:49.171906+00:00). **Seen**: 43. **Classified**: 12. **Urgent**: 0. **Skipped (dup)**: 31.
**By category**: SHIPPER_CARRIER_REPLY=1, VENDOR=11

### URGENT (0)
(none)

### OPERATOR_REVIEW (42)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eaae9c3.11981cd1-436049718.-63981451029455321@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab2844.121dc3e4-1323924103.-897201302599066891@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab2fc5.5e2313ee1577036340.2352289053463846238@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab3c15.24cf6399-88112623.-565286828077602810@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eab95ac.6c28ba58-1623363380.-3380839433630098889@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eaba157.6c9f3481-1067366662.-8233230213846990024@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eaba92f.314d94911793166104.-247977468496996118@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eabad92.3f510c5f-1497776301.-2043669024001327045@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eabb8e4.637def32-1673446040.-4794587228922865460@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb1c96c.c833628535964756.-184445179369330886@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb2389f.8328ddef-758871206.2480902411926125557@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb236d5.31d465f91596277470.5443159372372314541@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb23eb2.26481118-606425205.5908644855288867154@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb24097.1afbdc9a-1224703121.-6467075774362191181@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb246d7.3fde6a78514532916.6494719236628437307@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb248bc.3f2855f2-1106575814.5646047528956751224@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb2503b.4069ae1c-779517579.4488544516193620720@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb24e8a.7a55cc11-909000398.7040424116467661680@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb25665.6ea0db291832469826.-5152789836004857315@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb25841.6aae68f5-1801372923.-8901591147042622331@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb73238.41478400-528421159.-8475225395084136890@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb78078.6370102a1355465754.-7311017506290584149@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb78254.187c6459-1589090914.4802124068900982123@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb78a1f.657bc523-1420467910.-8351921456066359854@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb7884e.41cd5205-270833881.5113486711436024262@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb79025.182beb6f-1276514707.2308073938353045807@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb791e8.6c500008-197415915.-1384543892383353476@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb79815.37de37bc-914191669.-2473801621104228406@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb799d3.306e72821832423497.7474411629237136530@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb7a011.594f03681519680271.-2398460807378805043@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb7a1cb.5094cf12683578264.6024856651230854439@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8c198.3df808a6-1627670180.-6848600206767903483@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8cb57.746f54ff-827384283.-1750587353107558072@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8c988.69f501fe924088922.9219961308914442982@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8d302.40bc29a51651241627.1473818544992544690@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8d143.dc63c9d1150823600.-7659192809719037505@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8daf2.831fc2d6200042617.-1196118170752439264@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8d935.371aac5e-1197083054.-8195607383878779371@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8e10c.4d282e77529488156.-7301842671093196412@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8eac6.3aed7b2f1328289652.7412188120539334071@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8e8d6.31ff9c481587120728.-2682939027059727360@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0ec09ae6.4d26cdce23118171.4139559967715537258@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (1)
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-1337FDEB · Broker Admin' message_id=<19e0ec4bf5a.7433a258968208509.3001300120549666968@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-09T22:57:54.492945+00:00
**Window**: last 75 min (since 2026-05-09T21:42:54.454329+00:00). **Seen**: 34. **Classified**: 0. **Urgent**: 0. **Skipped (dup)**: 34.

### URGENT (0)
(none)

### OPERATOR_REVIEW (33)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb1c96c.c833628535964756.-184445179369330886@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb2389f.8328ddef-758871206.2480902411926125557@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb236d5.31d465f91596277470.5443159372372314541@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb23eb2.26481118-606425205.5908644855288867154@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb24097.1afbdc9a-1224703121.-6467075774362191181@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb246d7.3fde6a78514532916.6494719236628437307@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb248bc.3f2855f2-1106575814.5646047528956751224@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb2503b.4069ae1c-779517579.4488544516193620720@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb24e8a.7a55cc11-909000398.7040424116467661680@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb25665.6ea0db291832469826.-5152789836004857315@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb25841.6aae68f5-1801372923.-8901591147042622331@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb73238.41478400-528421159.-8475225395084136890@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb78078.6370102a1355465754.-7311017506290584149@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb78254.187c6459-1589090914.4802124068900982123@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb78a1f.657bc523-1420467910.-8351921456066359854@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb7884e.41cd5205-270833881.5113486711436024262@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb79025.182beb6f-1276514707.2308073938353045807@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb791e8.6c500008-197415915.-1384543892383353476@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb79815.37de37bc-914191669.-2473801621104228406@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb799d3.306e72821832423497.7474411629237136530@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb7a011.594f03681519680271.-2398460807378805043@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb7a1cb.5094cf12683578264.6024856651230854439@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8c198.3df808a6-1627670180.-6848600206767903483@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8cb57.746f54ff-827384283.-1750587353107558072@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8c988.69f501fe924088922.9219961308914442982@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8d302.40bc29a51651241627.1473818544992544690@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8d143.dc63c9d1150823600.-7659192809719037505@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8daf2.831fc2d6200042617.-1196118170752439264@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8d935.371aac5e-1197083054.-8195607383878779371@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8e10c.4d282e77529488156.-7301842671093196412@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8eac6.3aed7b2f1328289652.7412188120539334071@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eb8e8d6.31ff9c481587120728.-2682939027059727360@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0ec09ae6.4d26cdce23118171.4139559967715537258@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (1)
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-1337FDEB · Broker Admin' message_id=<19e0ec4bf5a.7433a258968208509.3001300120549666968@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-09T23:15:43.131742+00:00
**Window**: last 75 min (since 2026-05-09T22:00:43.106776+00:00). **Seen**: 16. **Classified**: 15. **Urgent**: 0. **Skipped (dup)**: 1.
**By category**: VENDOR=15

### URGENT (0)
(none)

### OPERATOR_REVIEW (15)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0efe03b0.38de2a54-1419507658.86452821339920040@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0efe2af4.3bf65e4b360028282.472010910818700220@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0efe4a1a.3a6d91421586213204.-8175245616320427950@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0efe70e3.293fbaac273572185.3671484765812938492@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0efe9876.2c5aeb502120919082.-2678778374590816938@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0efeb76e.71eff0b7-1520398870.8549494620099699230@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0efede77.7d93497c1638772998.-721797808068131854@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eff0573.6d14f5bf52769745.-5545042643303712466@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eff2cc0.2fd9156b1189569048.-1782257395755766740@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eff53b8.40e0f04e-1732270913.8801295109055259226@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0eff7ae1.373d872551629227.-521054825529841864@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0effa242.7e56e23c-1180829212.457369637879473257@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0effc142.233a7ed92062643486.-731795659367550562@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f011196.5c9125e6-2002558203.-2208388762151651158@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f032c77.74e33138-560023084.6387556077697886626@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (1)
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-1337FDEB · Broker Admin' message_id=<19e0ec4bf5a.7433a258968208509.3001300120549666968@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-09 23:26 UTC

Total ingested last 75min: 0
By category: (none)
By verdict: (none)

### URGENT (require operator attention)
- (none)

### OPERATOR_REVIEW (next session)
- (none)

### AUTO_ACK_OK / INFORMATIONAL (logged for completeness)
- (none)

