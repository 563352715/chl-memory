
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


## Triage run -- 2026-05-10T00:15:43.176041+00:00
**Window**: last 75 min (since 2026-05-09T23:00:43.137347+00:00). **Seen**: 21. **Classified**: 6. **Urgent**: 0. **Skipped (dup)**: 15.
**By category**: SHIPPER_CARRIER_REPLY=3, VENDOR=3

### URGENT (0)
(none)

### OPERATOR_REVIEW (18)
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
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f391f90.70764c46-1498813414.6148582107705625685@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f392747.248ae68b1464400411.1842476649405998384@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f3936c8.1539ff18124090062.96685830498940838@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (3)
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-928F2AC1 · Broker Admin' message_id=<19e0f0da94b.1f57918d-114713197.5498517886933654237@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-D5F41766 · Broker Admin' message_id=<19e0f224389.73c56d5-1602924205.5113976614359917580@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-C9F07472 · Broker Admin' message_id=<19e0f2ec89b.10c99861-1518311747.7847499651145137042@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-10 01:11 UTC

Total ingested last 75min: 0
By category: (none)
By verdict: (none)

### URGENT (require operator attention)
- (none)

### OPERATOR_REVIEW (next session)
- (none)

### AUTO_ACK_OK / INFORMATIONAL (logged for completeness)
- (none)


## Triage run -- 2026-05-10T01:15:43.259979+00:00
**Window**: last 75 min (since 2026-05-10T00:00:43.185054+00:00). **Seen**: 75. **Classified**: 71. **Urgent**: 0. **Skipped (dup)**: 4.
**By category**: VENDOR=71

### URGENT (0)
(none)

### OPERATOR_REVIEW (74)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f391f90.70764c46-1498813414.6148582107705625685@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f392747.248ae68b1464400411.1842476649405998384@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f3936c8.1539ff18124090062.96685830498940838@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4298e5.20032cfa-138969990.-1350487575883913850@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f431db3.76d9988f-1809980840.7472575297816054962@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f439ab6.8776e8a2221210446.5427003922326659582@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f441b38.43776eee-637358642.-7738531639857477221@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f449932.1e2103b1-2017757950.-1016946991135102108@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4519be.2654e9a1-17691600.-1796522715616712139@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4596c8.60f53daf-1140961691.7143344310611410917@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f462f3c.5eafafb81974649120.8364967781408176836@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f46c818.570fa7381434547524.8482697529599303348@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f474519.6df48347-4312850.2949911910474623135@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f47c259.655f58771862571191.-8643242823832842026@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f484720.884790a9644909283.8218083968313599781@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f48c445.77319c87391987871.8789737338876190368@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f49be73.2c4a91c9-1062724114.2890340119029592877@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4a3b95.7a45ea73-1596451652.6758207418022285275@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4ac825.3ea57ad5226858140.5120715489465696455@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4b4985.653f89e61169755029.6035154921399146034@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4d2e38.7849e567833997660.-5297144091176912384@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4daf1e.71864b66-1762464169.3924121263697782385@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4e3002.11cc1d391510734527.4965127990451099979@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4ea967.4a74d407756833495.-7602539977047008640@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4f2e3f.641499851440239036.-7173934857536522678@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4fb39a.2a3770ba-1276693060.5504507479245650273@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f50305e.3f5f63f9675161798.-7164054042119712357@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f50bd39.77c65307631382981.4640275965510297801@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f515631.6f60cd2e500376621.7919252419511857533@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f51df50.25fc2332725040325.-3058624754478482295@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5273be.45d1f195109512183.-1264324094817925459@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f52f893.c7c6e22-1473637419.-5417119833711240227@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5375e8.147c28e8-1331631268.-1697975223348956517@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5477b6.77dccff5-1704978529.-7109354899906874116@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f54f4df.2fc304f5-2052446116.-2733416271910086602@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5579f6.32a1c3a3682149554.-8553721837089714330@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f55fe9f.437b7b5f-2126311542.-5422311841578102229@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f57e318.883916ae1618643200.2863253100507118592@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f586042.82f98414369447726.-4254396326499253735@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f58e907.5a2cd3d2622175868.6423057364978892410@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f596a0a.6dd60aa31243971171.-7902336645952830505@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f59e734.202b1883139713275.9194716614179036576@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5a6465.3b79e721-1873079139.-1008950649557456078@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5ae148.13856ffa-742427031.6352225388290569952@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5b79ad.3dc1d9b61579998419.-736145903959596487@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5c12c8.49ee9688-1760587968.4397579749651024187@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5c87f7.6af9f6181073910397.2563382571194245858@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5d0cd3.72a6b593-1938480417.6160886703216123484@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5d95c7.2e0b3ba6-1069621139.1006445262727511472@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5e12f5.2e6fe7f2119389549.3214174278103631341@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5f090c.177a08691558953777.-4187923512636526283@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5f8e21.5cd2fa0e-704525463.-4408045683316463387@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f600aef.37719ccb250895283.5342770299146277795@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f608fe1.3124dcfa328126744.-2111035179525176301@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f627c5c.6b07351f1925352815.-8555646099155535524@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f62f982.2b5b4d9b95933138.-3216896400975696155@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f63765d.345b4ebb-1041563171.-4178446957963585052@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f63fb38.547ed85d-854467795.-7947520064614273674@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f64785e.3758200e-428221212.-696765040421721079@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f64f9b1.34f36287-1811371640.-8525509534703130558@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f657a68.80e93a341026085379.2087417445038682396@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6614eb.6818186e66818102.4649649641304970748@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f66b0a8.6281b335-989357443.4959691390620295473@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f672921.2cc68eca-629946764.5240771219172009178@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f67a64b.113dc6b1-623933063.2226852618228203307@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f682b44.4f94d948-1023079874.7431038323202528656@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f68b02f.6c3dd8ac282052411.4666570745030235992@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f69a2aa.1766d23d380611452.7804812140469316593@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6a1fbd.58200f07-1594632628.-917338738828713460@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6aac70.7c23036e-1406466155.-5322359914801686388@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6b3122.8166804b568605796.-7566918457880014533@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6d2199.62a7ae05827918590.14467855437159416@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6da2d1.59d4c884294462673.-917917203116929340@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6e2017.2c8880b6383111327.6570405049876869325@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (1)
- [SHIPPER_CARRIER_REPLY] conf=0.55 from=dispatch@continentalhaul.com subject='📄 Signed Rate Con · RC-C9F07472 · Broker Admin' message_id=<19e0f2ec89b.10c99861-1518311747.7847499651145137042@zoho.com>
    - freight_body_term:\bload\s+(?:#?\s*\w+)\b
    - auto_ack_pattern:\b(?:rate|load)\s+confirmation\b.*\b(?:confirm(?:ed)?|accepted|booked)\b

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-10 01:27 UTC

Total ingested last 75min: 0
By category: (none)
By verdict: (none)

### URGENT (require operator attention)
- (none)

### OPERATOR_REVIEW (next session)
- (none)

### AUTO_ACK_OK / INFORMATIONAL (logged for completeness)
- (none)


## Triage run -- 2026-05-10T01:28:19.788107+00:00
**Window**: last 75 min (since 2026-05-10T00:13:19.710749+00:00). **Seen**: 77. **Classified**: 3. **Urgent**: 0. **Skipped (dup)**: 74.
**By category**: VENDOR=3

### URGENT (0)
(none)

### OPERATOR_REVIEW (77)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f391f90.70764c46-1498813414.6148582107705625685@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f392747.248ae68b1464400411.1842476649405998384@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f3936c8.1539ff18124090062.96685830498940838@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4298e5.20032cfa-138969990.-1350487575883913850@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f431db3.76d9988f-1809980840.7472575297816054962@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f439ab6.8776e8a2221210446.5427003922326659582@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f441b38.43776eee-637358642.-7738531639857477221@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f449932.1e2103b1-2017757950.-1016946991135102108@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4519be.2654e9a1-17691600.-1796522715616712139@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4596c8.60f53daf-1140961691.7143344310611410917@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f462f3c.5eafafb81974649120.8364967781408176836@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f46c818.570fa7381434547524.8482697529599303348@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f474519.6df48347-4312850.2949911910474623135@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f47c259.655f58771862571191.-8643242823832842026@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f484720.884790a9644909283.8218083968313599781@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f48c445.77319c87391987871.8789737338876190368@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f49be73.2c4a91c9-1062724114.2890340119029592877@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4a3b95.7a45ea73-1596451652.6758207418022285275@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4ac825.3ea57ad5226858140.5120715489465696455@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4b4985.653f89e61169755029.6035154921399146034@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4d2e38.7849e567833997660.-5297144091176912384@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4daf1e.71864b66-1762464169.3924121263697782385@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4e3002.11cc1d391510734527.4965127990451099979@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4ea967.4a74d407756833495.-7602539977047008640@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4f2e3f.641499851440239036.-7173934857536522678@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4fb39a.2a3770ba-1276693060.5504507479245650273@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f50305e.3f5f63f9675161798.-7164054042119712357@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f50bd39.77c65307631382981.4640275965510297801@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f515631.6f60cd2e500376621.7919252419511857533@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f51df50.25fc2332725040325.-3058624754478482295@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5273be.45d1f195109512183.-1264324094817925459@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f52f893.c7c6e22-1473637419.-5417119833711240227@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5375e8.147c28e8-1331631268.-1697975223348956517@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5477b6.77dccff5-1704978529.-7109354899906874116@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f54f4df.2fc304f5-2052446116.-2733416271910086602@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5579f6.32a1c3a3682149554.-8553721837089714330@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f55fe9f.437b7b5f-2126311542.-5422311841578102229@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f57e318.883916ae1618643200.2863253100507118592@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f586042.82f98414369447726.-4254396326499253735@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f58e907.5a2cd3d2622175868.6423057364978892410@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f596a0a.6dd60aa31243971171.-7902336645952830505@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f59e734.202b1883139713275.9194716614179036576@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5a6465.3b79e721-1873079139.-1008950649557456078@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5ae148.13856ffa-742427031.6352225388290569952@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5b79ad.3dc1d9b61579998419.-736145903959596487@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5c12c8.49ee9688-1760587968.4397579749651024187@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5c87f7.6af9f6181073910397.2563382571194245858@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5d0cd3.72a6b593-1938480417.6160886703216123484@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5d95c7.2e0b3ba6-1069621139.1006445262727511472@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5e12f5.2e6fe7f2119389549.3214174278103631341@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5f090c.177a08691558953777.-4187923512636526283@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5f8e21.5cd2fa0e-704525463.-4408045683316463387@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f600aef.37719ccb250895283.5342770299146277795@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f608fe1.3124dcfa328126744.-2111035179525176301@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f627c5c.6b07351f1925352815.-8555646099155535524@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f62f982.2b5b4d9b95933138.-3216896400975696155@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f63765d.345b4ebb-1041563171.-4178446957963585052@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f63fb38.547ed85d-854467795.-7947520064614273674@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f64785e.3758200e-428221212.-696765040421721079@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f64f9b1.34f36287-1811371640.-8525509534703130558@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f657a68.80e93a341026085379.2087417445038682396@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6614eb.6818186e66818102.4649649641304970748@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f66b0a8.6281b335-989357443.4959691390620295473@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f672921.2cc68eca-629946764.5240771219172009178@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f67a64b.113dc6b1-623933063.2226852618228203307@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f682b44.4f94d948-1023079874.7431038323202528656@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f68b02f.6c3dd8ac282052411.4666570745030235992@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f69a2aa.1766d23d380611452.7804812140469316593@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6a1fbd.58200f07-1594632628.-917338738828713460@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6aac70.7c23036e-1406466155.-5322359914801686388@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6b3122.8166804b568605796.-7566918457880014533@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6d2199.62a7ae05827918590.14467855437159416@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6da2d1.59d4c884294462673.-917917203116929340@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6e2017.2c8880b6383111327.6570405049876869325@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f7d8fba.75c10967294590599.4403802009702778980@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f7dc65d.72c7cf0f-827125503.1184887668320761418@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f7dfd03.26336558-413758828.-764904853526857899@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-10T01:40:24.145047+00:00
**Window**: last 75 min (since 2026-05-10T00:25:24.075636+00:00). **Seen**: 69. **Classified**: 0. **Urgent**: 0. **Skipped (dup)**: 69.

### URGENT (0)
(none)

### OPERATOR_REVIEW (69)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4519be.2654e9a1-17691600.-1796522715616712139@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4596c8.60f53daf-1140961691.7143344310611410917@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f462f3c.5eafafb81974649120.8364967781408176836@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f46c818.570fa7381434547524.8482697529599303348@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f474519.6df48347-4312850.2949911910474623135@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f47c259.655f58771862571191.-8643242823832842026@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f484720.884790a9644909283.8218083968313599781@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f48c445.77319c87391987871.8789737338876190368@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f49be73.2c4a91c9-1062724114.2890340119029592877@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4a3b95.7a45ea73-1596451652.6758207418022285275@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4ac825.3ea57ad5226858140.5120715489465696455@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4b4985.653f89e61169755029.6035154921399146034@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4d2e38.7849e567833997660.-5297144091176912384@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4daf1e.71864b66-1762464169.3924121263697782385@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4e3002.11cc1d391510734527.4965127990451099979@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4ea967.4a74d407756833495.-7602539977047008640@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4f2e3f.641499851440239036.-7173934857536522678@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f4fb39a.2a3770ba-1276693060.5504507479245650273@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f50305e.3f5f63f9675161798.-7164054042119712357@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f50bd39.77c65307631382981.4640275965510297801@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f515631.6f60cd2e500376621.7919252419511857533@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f51df50.25fc2332725040325.-3058624754478482295@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5273be.45d1f195109512183.-1264324094817925459@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f52f893.c7c6e22-1473637419.-5417119833711240227@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5375e8.147c28e8-1331631268.-1697975223348956517@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5477b6.77dccff5-1704978529.-7109354899906874116@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f54f4df.2fc304f5-2052446116.-2733416271910086602@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5579f6.32a1c3a3682149554.-8553721837089714330@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f55fe9f.437b7b5f-2126311542.-5422311841578102229@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f57e318.883916ae1618643200.2863253100507118592@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f586042.82f98414369447726.-4254396326499253735@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f58e907.5a2cd3d2622175868.6423057364978892410@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f596a0a.6dd60aa31243971171.-7902336645952830505@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f59e734.202b1883139713275.9194716614179036576@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5a6465.3b79e721-1873079139.-1008950649557456078@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5ae148.13856ffa-742427031.6352225388290569952@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5b79ad.3dc1d9b61579998419.-736145903959596487@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5c12c8.49ee9688-1760587968.4397579749651024187@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5c87f7.6af9f6181073910397.2563382571194245858@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5d0cd3.72a6b593-1938480417.6160886703216123484@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5d95c7.2e0b3ba6-1069621139.1006445262727511472@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5e12f5.2e6fe7f2119389549.3214174278103631341@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5f090c.177a08691558953777.-4187923512636526283@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5f8e21.5cd2fa0e-704525463.-4408045683316463387@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f600aef.37719ccb250895283.5342770299146277795@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f608fe1.3124dcfa328126744.-2111035179525176301@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f627c5c.6b07351f1925352815.-8555646099155535524@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f62f982.2b5b4d9b95933138.-3216896400975696155@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f63765d.345b4ebb-1041563171.-4178446957963585052@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f63fb38.547ed85d-854467795.-7947520064614273674@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f64785e.3758200e-428221212.-696765040421721079@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f64f9b1.34f36287-1811371640.-8525509534703130558@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f657a68.80e93a341026085379.2087417445038682396@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6614eb.6818186e66818102.4649649641304970748@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f66b0a8.6281b335-989357443.4959691390620295473@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f672921.2cc68eca-629946764.5240771219172009178@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f67a64b.113dc6b1-623933063.2226852618228203307@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f682b44.4f94d948-1023079874.7431038323202528656@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f68b02f.6c3dd8ac282052411.4666570745030235992@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f69a2aa.1766d23d380611452.7804812140469316593@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6a1fbd.58200f07-1594632628.-917338738828713460@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6aac70.7c23036e-1406466155.-5322359914801686388@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6b3122.8166804b568605796.-7566918457880014533@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6d2199.62a7ae05827918590.14467855437159416@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6da2d1.59d4c884294462673.-917917203116929340@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6e2017.2c8880b6383111327.6570405049876869325@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f7d8fba.75c10967294590599.4403802009702778980@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f7dc65d.72c7cf0f-827125503.1184887668320761418@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f7dfd03.26336558-413758828.-764904853526857899@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-10T02:07:13.356465+00:00
**Window**: last 75 min (since 2026-05-10T00:52:13.278080+00:00). **Seen**: 68. **Classified**: 36. **Urgent**: 0. **Skipped (dup)**: 32.
**By category**: VENDOR=36

### URGENT (0)
(none)

### OPERATOR_REVIEW (68)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5c12c8.49ee9688-1760587968.4397579749651024187@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5c87f7.6af9f6181073910397.2563382571194245858@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5d0cd3.72a6b593-1938480417.6160886703216123484@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5d95c7.2e0b3ba6-1069621139.1006445262727511472@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5e12f5.2e6fe7f2119389549.3214174278103631341@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5f090c.177a08691558953777.-4187923512636526283@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f5f8e21.5cd2fa0e-704525463.-4408045683316463387@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f600aef.37719ccb250895283.5342770299146277795@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f608fe1.3124dcfa328126744.-2111035179525176301@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f627c5c.6b07351f1925352815.-8555646099155535524@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f62f982.2b5b4d9b95933138.-3216896400975696155@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f63765d.345b4ebb-1041563171.-4178446957963585052@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f63fb38.547ed85d-854467795.-7947520064614273674@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f64785e.3758200e-428221212.-696765040421721079@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f64f9b1.34f36287-1811371640.-8525509534703130558@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f657a68.80e93a341026085379.2087417445038682396@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6614eb.6818186e66818102.4649649641304970748@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f66b0a8.6281b335-989357443.4959691390620295473@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f672921.2cc68eca-629946764.5240771219172009178@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f67a64b.113dc6b1-623933063.2226852618228203307@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f682b44.4f94d948-1023079874.7431038323202528656@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f68b02f.6c3dd8ac282052411.4666570745030235992@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f69a2aa.1766d23d380611452.7804812140469316593@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6a1fbd.58200f07-1594632628.-917338738828713460@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6aac70.7c23036e-1406466155.-5322359914801686388@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6b3122.8166804b568605796.-7566918457880014533@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6d2199.62a7ae05827918590.14467855437159416@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6da2d1.59d4c884294462673.-917917203116929340@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f6e2017.2c8880b6383111327.6570405049876869325@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f7d8fba.75c10967294590599.4403802009702778980@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f7dc65d.72c7cf0f-827125503.1184887668320761418@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f7dfd03.26336558-413758828.-764904853526857899@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8e99e8.611ed1da322958110.-1079827470322904262@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8eb8fa.33a186a9-489485468.449157915190743514@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8ed0cc.3c7d73901980419984.2784820144319017684@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8ef012.a60fcf9-1717374205.7008767090676493987@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8f0fb3.2bdadb55821984551.5346195218339553920@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8f2e87.6ffe7700150230924.-8745825214101023017@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8f45f1.4794b0791001156878.6682417026098382626@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8f8556.e117bc8785265983.3837460521059571198@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8fabd9.3e211aab145563240.-4723636192620167526@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8fcb19.3aa13eef-553071698.6628277335166499178@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f8feaa7.7397aa47-244374677.-5136642591714747150@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f901172.62b8a4ff-434696015.-8846818704459837745@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f9028f6.3d332c372143785994.6299757954791255976@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f905fa5.5664de16-187852061.-7974318166887137045@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f907f25.1bd45d37-887894184.-8010517947947085185@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f909e37.206bc0e01708646880.-7881911172667429886@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f90bd98.1950da432031450048.3238605563100572351@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f911b6f.63d4cc71-1225676879.-4039406903091002987@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f91334c.7339cdea-418309242.-954471565294368370@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f9153fb.19a042e6-1131595161.-457622410453321702@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f917331.29e136b0-1231121544.-7109814028202060579@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f91925e.6a4438b9-1503954767.4317717444939839614@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f91b1c8.73044efc-758457281.7960181438344133168@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f91c946.2b9cbbb6-637637214.-5052761274292383190@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f91f06c.164b3583-1328422763.-8283567420420350917@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f923e66.81012757-2132830669.-4696981618173214717@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f9226e5.2661f0a0-389177174.-7378442521244006842@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f925d9d.54e2fd3b1091210588.-323532211885667304@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f927ce8.4769aa33-1964596870.-3320901463319997287@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f929c2e.199010f4352835101.-1221341097765603888@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f92d2e1.1a28e4d8-189131308.1596800244893068355@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f92ea7a.5dba49081081985154.-7729067009414834438@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f931938.6d97145d-1445078388.1525164376962533161@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f933868.7d31305b551835306.-3694665294800872718@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f938e8c.164caeb72095002125.2463408315165996786@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0f93ada8.7b0f2f9e900643619.-2059714022188433466@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-10 02:26 UTC

Total ingested last 75min: 73
By category: {'PROVIDER': 73}
By verdict: {'INFORMATIONAL': 73}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (73)

- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action
- `mailer-daemon@mail.zoho.com` -- **Undelivered Mail Returned to Sender**
  - cat=PROVIDER  summary: This message was created automatically by mail delivery software. A message that you sent could not be delivered to one 
  - action: No action

---

### URGENT REVIEW (post-triage promotion)

**Bounce-back flood detected:** 73 of 73 emails ingested last 75min are `mailer-daemon@mail.zoho.com` "Undelivered Mail Returned to Sender" notices. The triage script categorized them as PROVIDER/INFORMATIONAL because the sender domain is zoho.com, but the volume + content indicate a deliverability emergency:

- Likely causes: SPF/DKIM/DMARC misconfig, recipient blacklist, Zoho rate limit, or domain-reputation issue
- Action: investigate bounce-reason headers (550 / 553 / 421 / 5.7.x codes), check SPF + DKIM at MXToolbox, review Zoho outbound deliverability dashboard
- Promoted to URGENT: bounce volume that high means real outbound (carrier offers, factor docs, shipper invoices) is failing silently

**Recommended next-session investigation:** sample 5 bounces, extract `Final-Recipient` + `Diagnostic-Code`, group by failure reason, route fix to outbound email policy queue.


### CORRECTION -- bounce-flood is SYNTHETIC TEST NOISE, not urgent

The 73-bounce signal above is `*@test.continentalhaul.com` — a fake domain my synthetic test runners (sop_flow_orchestrator, multi_actor_synthesizer, synthetic_auto_driver) use as the "carrier email" when accepting offers. The domain has no DNS record, so every synthetic accept-offer email bounces. Recipient examples: `sop+<uuid>-NNN-xxxx@test.continentalhaul.com`, `auto-driver+...@test.continentalhaul.com`.

**Bounce count correlates with synthetic-load test volume**, not real outbound. Real outbound (carrier offers, factor docs, shipper invoices) goes through `dispatch@continentalhaul.com` to real domains and is unaffected.

**Action:** none on the bounces themselves (working as designed). **Triage script TODO**: skip-list `mailer-daemon@mail.zoho.com` rows whose body contains `test.continentalhaul.com` so future hourly fires don't generate URGENT false-positives.

