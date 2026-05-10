
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


## Triage run -- 2026-05-10T03:07:15.326721+00:00
**Window**: last 75 min (since 2026-05-10T01:52:15.245141+00:00). **Seen**: 45. **Classified**: 34. **Urgent**: 0. **Skipped (dup)**: 11.
**By category**: VENDOR=34

### URGENT (0)
(none)

### OPERATOR_REVIEW (45)
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
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa1d8f2.10c022f31528874380.-3529215730903429051@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa1f022.7ebea101576724251.-8320045846657159913@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa20f92.107d1cb0-1419789349.-2318925078850749042@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa22f0a.aecd8a1961212710.7811904109528188866@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa24e08.67aa5ef7200479808.976030982439614243@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa26d5f.3416925e-602073497.3817628428467716161@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa28ca2.502070521395308313.219869259328892644@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa2b3bf.3f442b0f-1052714195.4391660388358132959@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa2e29f.2261a6f8-158075594.-8618263458813928456@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa309d1.44d6c988-1571793637.6874162743051214589@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa32915.4a7eb9b41202905356.1014148941020261237@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa3487c.44aff42b-1998926272.-6519548814735845003@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa367cc.5403d8a3739184455.-1422998201349232243@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa39697.201580d2-812809404.-7808803271815418584@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa3b601.39b1c966570586737.8868683311123208131@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa3d57b.3903366d-191536053.-2952083713159950985@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa3f485.23ce7206-340256774.-6689215572859698921@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa488e7.5f200129-1108772115.-7246596766363501987@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa4a852.38b4db91-1996922026.-916236221615165777@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa4c77a.4c372a6b-1492350789.770509000926236289@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa4e71c.3e3d88e0-388909491.5435653002978337261@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa50def.76070bbf-532813956.3266379111835739118@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa52d3a.161d68e5-1345761298.3326740504805783929@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa544fa.4b6cb5e0-159432335.-1496887963577017502@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa563d1.3af4edfa92817388.-7688218307238131843@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa59abe.3fae2e611416577812.7383193250284486945@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa5b9db.478844111232312812.-4369298432006517393@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa5d945.7694ba882005944642.2722557832986381840@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa5f87d.3f873ad7-5922195.-9153850268274933025@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa60ffb.3e18aa0c-1663617325.4820918547611881357@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa646a7.400659de1000114680.4218725547021588886@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa665fb.58f3e5ba-2132950089.-7070292499555242759@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa68555.3b254fe8332207594.-3825902796770259041@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa6a48f.562ed5a31547217093.6012309905619361952@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-10T03:28:27.837285+00:00
**Window**: last 75 min (since 2026-05-10T02:13:27.790846+00:00). **Seen**: 43. **Classified**: 39. **Urgent**: 0. **Skipped (dup)**: 4.
**By category**: VENDOR=39

### URGENT (0)
(none)

### OPERATOR_REVIEW (43)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa646a7.400659de1000114680.4218725547021588886@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa665fb.58f3e5ba-2132950089.-7070292499555242759@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa68555.3b254fe8332207594.-3825902796770259041@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fa6a48f.562ed5a31547217093.6012309905619361952@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=noreply@zoho.com subject='Zoho : Email Outgoing Blocked' message_id=<19e0fd9fe0e.114ba560b584089.2317293746634835465@zoho.com>
    - sender_domain_match:vendor=zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fda127c.68056139631423688.848910092845117060@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fda4997.65d11391150590653.-8340616987723352627@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fda7fed.5327b491-2098836881.-8469358864494672115@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdaaeed.4de06b3e-1611509985.7696164029885564682@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdadddc.8982dd931704217884.-440082538622418824@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdafd1a.67289aa31709682047.-6504616988577158388@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdb1c6c.14b74a19981439095.291305934387405323@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdb43b7.258191ba1309262094.5732247555862219335@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdb62cb.1b9420df-1229139516.-8729269005003952161@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdb822b.36cd7bea986243284.-2692965854180142284@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdba174.33726b5b604613339.6823723806384333101@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdbc103.461c37e2-1089068260.6312638384880067908@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdbe01e.86df8efd-703137362.-5963852577411359826@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdc0edc.39c56f341871481721.-6229537418737809662@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdc2e3b.6f797292842992014.-2338062165869900043@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdc4d68.65fa5e2d1694121876.8204243611644799291@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdc6ccb.7cb8f4d12025193158.7174309202842497485@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdcca8e.7468b03f-336837356.-6723506584792417557@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdce20f.58e14ca1-1085493975.304060258426044589@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd0156.3ec84ee8-1275377788.-3386394373430838451@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd2096.66a445a61147409825.3815360049789264741@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd37fa.6202e29f1847749217.-7373671424032376766@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd575f.28847df1-1152216328.-8959086299597796447@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd6eab.1ae819a8-1210116196.2299105441599607152@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd95cd.647009d5-2060049474.-4035175862135594769@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fddbcec.4d1631a6355448931.-7239522543809082808@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdde419.6c09062e88248.-2101619485930692962@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fde034d.2c40fd43-406692430.-6334065729754243038@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fde22a0.700357bb747675224.351897934669137485@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fde3a06.673d1018-99544816.-1639385849798912800@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fde7116.87c54f3a-719155336.-8975925984075246124@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fde900a.6c090dba-601228864.5312965947984781569@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdeaf6d.59a5a89d904927196.-7498970480877383405@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0feb741f.6826cae3-2077149330.-1667270378344372872@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0feb8b62.517394d8-1227531522.-7302641662198394119@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0febaadc.1c1d7e63-1767886448.-7294895555702501240@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0febc9f0.cc5ad691300321215.2485555422211663113@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0febe177.7e5be96e-877224407.3387286514392483571@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-10T03:51:53.994978+00:00
**Window**: last 75 min (since 2026-05-10T02:36:53.917667+00:00). **Seen**: 67. **Classified**: 28. **Urgent**: 0. **Skipped (dup)**: 39.
**By category**: VENDOR=28

### URGENT (0)
(none)

### OPERATOR_REVIEW (67)
- [VENDOR] conf=0.85 from=noreply@zoho.com subject='Zoho : Email Outgoing Blocked' message_id=<19e0fd9fe0e.114ba560b584089.2317293746634835465@zoho.com>
    - sender_domain_match:vendor=zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fda127c.68056139631423688.848910092845117060@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fda4997.65d11391150590653.-8340616987723352627@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fda7fed.5327b491-2098836881.-8469358864494672115@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdaaeed.4de06b3e-1611509985.7696164029885564682@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdadddc.8982dd931704217884.-440082538622418824@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdafd1a.67289aa31709682047.-6504616988577158388@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdb1c6c.14b74a19981439095.291305934387405323@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdb43b7.258191ba1309262094.5732247555862219335@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdb62cb.1b9420df-1229139516.-8729269005003952161@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdb822b.36cd7bea986243284.-2692965854180142284@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdba174.33726b5b604613339.6823723806384333101@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdbc103.461c37e2-1089068260.6312638384880067908@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdbe01e.86df8efd-703137362.-5963852577411359826@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdc0edc.39c56f341871481721.-6229537418737809662@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdc2e3b.6f797292842992014.-2338062165869900043@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdc4d68.65fa5e2d1694121876.8204243611644799291@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdc6ccb.7cb8f4d12025193158.7174309202842497485@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdcca8e.7468b03f-336837356.-6723506584792417557@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdce20f.58e14ca1-1085493975.304060258426044589@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd0156.3ec84ee8-1275377788.-3386394373430838451@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd2096.66a445a61147409825.3815360049789264741@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd37fa.6202e29f1847749217.-7373671424032376766@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd575f.28847df1-1152216328.-8959086299597796447@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd6eab.1ae819a8-1210116196.2299105441599607152@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdd95cd.647009d5-2060049474.-4035175862135594769@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fddbcec.4d1631a6355448931.-7239522543809082808@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdde419.6c09062e88248.-2101619485930692962@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fde034d.2c40fd43-406692430.-6334065729754243038@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fde22a0.700357bb747675224.351897934669137485@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fde3a06.673d1018-99544816.-1639385849798912800@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fde7116.87c54f3a-719155336.-8975925984075246124@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fde900a.6c090dba-601228864.5312965947984781569@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fdeaf6d.59a5a89d904927196.-7498970480877383405@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0feb741f.6826cae3-2077149330.-1667270378344372872@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0feb8b62.517394d8-1227531522.-7302641662198394119@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0febaadc.1c1d7e63-1767886448.-7294895555702501240@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0febc9f0.cc5ad691300321215.2485555422211663113@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0febe177.7e5be96e-877224407.3387286514392483571@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fec00bc.c6d8c3e-939100560.-4455842583385563927@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fec1fe2.7b02b2b1-153219697.7863675237567841484@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fec4edb.5a478f57-779491272.-6553836884620101130@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fec8d94.26f4ce8d-1007327842.-4114907390424842939@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fecbc8b.8a098bb4-217901878.5965391114194514758@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fecdb8f.6cfb83f61440150419.8490217018736106269@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fecfabb.31b16594-1036589888.-4401320025687240552@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fed1a20.2039be5b-52780594.-4805386982842025879@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fed50dd.81627a74785783660.-5901242910408046502@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fed782e.496092f81097149017.1560029694546670124@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fed9ee5.12d09f0e213339958.-503653194310897606@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fedbe41.617d422d821540358.5710960686095979148@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fee143f.41a92af0-1595728019.-9180650502716831908@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fee3ba2.7889891d174639761.-9110268162595502638@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fee5b1b.68a0569a1820355125.-1244286238351332834@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fee7a03.1a97e636448610893.-7990552872463613934@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fee916f.547db2ee-1390394520.6361829546975677440@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0feeb0d4.65078cac-1993029243.-8892185830384863166@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0feed000.67c494da-364651457.7324283011094582424@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0feeef8a.42aa70ce876384227.501210204635100658@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fef1e52.6048c44c-419050423.6358013790150685421@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fef45b0.538b6b681454357696.2955121814120856228@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fef649a.74ddedab-1166120746.-2531618393495201858@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fef8bb9.3e0a12d8-1649835331.-8373785544586407523@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fefa330.a6fb5ac-1548740677.7014707023720014381@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0fefd9dc.6bf58d98-1721363396.-811475454811701365@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0feff925.14abab6d-72993090.8304032750474733851@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e0ff01883.83ec70cc30549609.-8323302969761649183@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-10 03:53 UTC

Total ingested last 75min: 67
By category: {'PROVIDER': 67}
By verdict: {'INFORMATIONAL': 67}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (67)

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
- `noreply@zoho.com` -- **Zoho : Email Outgoing Blocked**
  - cat=PROVIDER  summary: Dear User, Email outgoing for your Zoho account has been blocked because your email outgoing rate has exceeded the allow
  - action: No action

---

## Hourly review 2026-05-10 04:37 UTC

Total ingested last 75min: 33
By category: {'PROVIDER': 33}
By verdict: {'INFORMATIONAL': 33}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (33)

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

## Hourly review 2026-05-10 05:25 UTC

Total ingested last 75min: 0
By category: {}
By verdict: {}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (0)

_(none this window)_

---

## Triage run -- 2026-05-10T05:30:33.073594+00:00
**Window**: last 75 min (since 2026-05-10T04:15:33.063225+00:00). **Seen**: 5. **Classified**: 5. **Urgent**: 0. **Skipped (dup)**: 0.
**By category**: VENDOR=5

### URGENT (0)
(none)

### OPERATOR_REVIEW (5)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b3ffe.f0fba61-639129223.-4368434182015506184@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b5f1a.6d2c3db0-1086063048.7720358160452543058@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b7e9a.558c5f66-421544750.2378877811936164826@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b9609.3528ab03-1903915931.8833168010034384990@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105bb56c.876a549c569369656.-7082523439184425759@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-10T05:37:52.113334+00:00
**Window**: last 75 min (since 2026-05-10T04:22:52.014861+00:00). **Seen**: 34. **Classified**: 29. **Urgent**: 0. **Skipped (dup)**: 5.
**By category**: VENDOR=29

### URGENT (0)
(none)

### OPERATOR_REVIEW (34)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b3ffe.f0fba61-639129223.-4368434182015506184@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b5f1a.6d2c3db0-1086063048.7720358160452543058@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b7e9a.558c5f66-421544750.2378877811936164826@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b9609.3528ab03-1903915931.8833168010034384990@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105bb56c.876a549c569369656.-7082523439184425759@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105bd456.50721b361447195262.8096568508426480108@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105bf3a7.c6af87d-1630373541.-4519043883780136909@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105c227f.ade61c1-1846816707.4669016834942640834@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105c5188.7437fff4-1915588127.-5984195761790361073@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105c7887.51dcd910342246926.-2588827676269783659@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105c97d4.86b8aedf1580688315.443350857129516822@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105cb73b.31c0f0601280119293.947583101062623293@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105cd664.377b4d13-1419319216.-6341673053981163398@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105d0d2d.1b5c087f-482237603.6053071313529806080@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105d2c72.544dfe8b1786022691.-8836825495603574570@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105d4b94.32690bbb1323095527.3049177299575501366@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105d6acd.f9aaa0f-2096113727.-4443433879688232058@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105dc105.7d3d98a4545306186.-1552955331886058199@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105de036.68165259681418741.-3582045758941275020@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105dff80.451a6717799904446.-146477208989725538@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105e2687.808b0db0-1960246966.8224841899290366137@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105e3e08.7a0294d5-1838762990.-3409106227328065698@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105e5d8a.8487721463493460.-1311928709108155921@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105e8487.489e4da1612324713.-7661185413837013321@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105eab9c.7b355348-1063506489.5733181879589760225@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105ed2a0.66f08500-1230209466.-8153420747527223246@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105ef1e1.6fa4e436735624874.4588974791221630990@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105f0953.15733d49-952460167.2170246310270555728@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105f306e.5efa9c90730476443.5692257368551557203@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105f4fba.52f5ceaf1579770368.6166789764405022362@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105f8662.15537b0b222984535.-1292822805377329797@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105fadb1.aa3b453488001243.-4368840481711320366@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105fc510.50cc1e5d-2062444476.8907282239080860354@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105fe46b.1ba585d62131668815.959435333495043388@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-10 06:25 UTC

Total ingested last 75min: 126
By category: {'PROVIDER': 126}
By verdict: {'INFORMATIONAL': 126}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (126)

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

## Triage run -- 2026-05-10T06:37:54.611848+00:00
**Window**: last 75 min (since 2026-05-10T05:22:52.117337+00:00). **Seen**: 183. **Classified**: 149. **Urgent**: 0. **Skipped (dup)**: 34.
**By category**: VENDOR=149

### URGENT (0)
(none)

### OPERATOR_REVIEW (183)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b3ffe.f0fba61-639129223.-4368434182015506184@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b5f1a.6d2c3db0-1086063048.7720358160452543058@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b7e9a.558c5f66-421544750.2378877811936164826@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105b9609.3528ab03-1903915931.8833168010034384990@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105bb56c.876a549c569369656.-7082523439184425759@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105bd456.50721b361447195262.8096568508426480108@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105bf3a7.c6af87d-1630373541.-4519043883780136909@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105c227f.ade61c1-1846816707.4669016834942640834@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105c5188.7437fff4-1915588127.-5984195761790361073@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105c7887.51dcd910342246926.-2588827676269783659@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105c97d4.86b8aedf1580688315.443350857129516822@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105cb73b.31c0f0601280119293.947583101062623293@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105cd664.377b4d13-1419319216.-6341673053981163398@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105d0d2d.1b5c087f-482237603.6053071313529806080@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105d2c72.544dfe8b1786022691.-8836825495603574570@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105d4b94.32690bbb1323095527.3049177299575501366@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105d6acd.f9aaa0f-2096113727.-4443433879688232058@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105dc105.7d3d98a4545306186.-1552955331886058199@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105de036.68165259681418741.-3582045758941275020@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105dff80.451a6717799904446.-146477208989725538@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105e2687.808b0db0-1960246966.8224841899290366137@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105e3e08.7a0294d5-1838762990.-3409106227328065698@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105e5d8a.8487721463493460.-1311928709108155921@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105e8487.489e4da1612324713.-7661185413837013321@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105eab9c.7b355348-1063506489.5733181879589760225@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105ed2a0.66f08500-1230209466.-8153420747527223246@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105ef1e1.6fa4e436735624874.4588974791221630990@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105f0953.15733d49-952460167.2170246310270555728@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105f306e.5efa9c90730476443.5692257368551557203@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105f4fba.52f5ceaf1579770368.6166789764405022362@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105f8662.15537b0b222984535.-1292822805377329797@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105fadb1.aa3b453488001243.-4368840481711320366@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105fc510.50cc1e5d-2062444476.8907282239080860354@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e105fe46b.1ba585d62131668815.959435333495043388@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10761775.71f2e216-68535896.-8356211020808905077@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10764637.43c0e77b-1950010775.6330006763048170178@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10767cf3.1900519f646280923.-2124958451996812676@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1076abd5.732cc12a-1815276942.566945625346765685@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1076db22.484d15fe1562358816.-6320092649258979006@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107709b9.2a39f8151025801782.-2456400052783220325@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107740a1.1929a3012139892095.7738314681290196884@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107786c3.8575838a1667297230.-6069808040943938757@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1077d502.51031268-279340824.9040932324158155706@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107803ff.cae7c972022716851.-7138745280516995325@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107832d4.51f054f8-535569556.-4553063731834990170@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10786982.581eccb2182495821.-5504294679770759426@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1078986c.602fc3c0-1202953830.5574110315414917845@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1078eea2.6be707ad994289721.-2174367595448551999@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10792531.859e6a54-1780316134.5919845253728135784@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10795406.6d91d530580053366.-2881018753887032068@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1079834f.42cf91e2589645519.6406949460695931312@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107a36e7.a8dfecb-584045734.-1650595269610048862@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107a65e0.19982497-950363662.1727185860205847881@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107a94ab.444965932010513590.1245743556846168183@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107acb72.5a67add21637741834.-5993554699093106733@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107b022b.5e01392f1434514366.-840924397257202271@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107b3104.82917cec-2132171084.-5798299503829023405@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107b5ff3.df6a61c-917112936.-7661052436706887657@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107b9e81.829415da225704165.1885529424217644259@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107be4d3.63043782-147015524.7690468666721420512@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107c13cf.33768fc81809284190.-5066702054602793027@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107c4a7c.85f47a96-1174632794.-404761250121328738@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107c7981.87f8e3a7156037358.-4065655093998435586@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107ca85a.62c6d12b-35368411.5982483719438912678@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107d0e21.702ccb81-458932270.8983512167907871566@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107d3cdb.18ab57e8-2060020528.9222614495774910928@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107d73ce.1403936a1409900633.5557599885151074605@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107da2bd.6b8907d2-1606454366.1933168471365745524@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107e46e5.634a5f811883345554.-5824174398649993271@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107e7dad.84a2fcdb792211538.8796159300670731394@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107eac67.298353df627252460.3281258178096103604@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107edb87.86a68647-1570580429.4252632163535924136@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107f11f7.7b97a023828459412.8725124931627830289@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107f48e5.4a0dca34-945231957.-6012946417158184024@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107f7fe3.51027885966065751.-1076833220213764679@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107fdd79.131477fc1787196350.4451160707201297120@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10802b6e.49c7574e-868290997.-3909082967698487517@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10806233.3588d8e3-2055471194.5663891205829902704@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10808983.6eab8f5b1646178429.669870156168370823@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1080c081.6674a491-351969773.2013600144126649460@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1080ef84.60f48b1d578065662.8588176596964110256@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10814d20.1552ffde1672827307.4329554894114399294@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10817c12.34829ee1-1019976021.2752686037161472331@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1081b2c9.2da69228-1207101289.3178009960315678791@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1081e1ab.130294c0-922167460.5500956054220129722@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10828db0.23d108ad-740274587.-1982068575094726242@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1082c469.b19ffc11824221672.-436416241808858588@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1082f395.2b3d6cff-949162652.-4040424009649026881@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10832273.443d7fd61977102059.2345078947794573156@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1083593d.6a2aa9df-1743746738.2380961575283008955@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108387fd.2fff9715-1344705828.2233400930660712665@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1083b6d2.66786083-274258679.5364894828522958346@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1083fd80.59ee985b-1679023819.-628587876871750781@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10843dda.239de5c0-303623880.5078833311161600519@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10846ae7.7557d6801882543540.-3770866479016153295@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108499a9.548ae2aa-1223890467.2197021163058090468@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1084c89c.1631ddbb1841181158.8413089748125487174@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1084ff59.79ed29b91760259842.1620774095402574637@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10855d43.daa7cad-1089173226.-2109139751161172082@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1085940a.b05180b371759346.9111223934048086217@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1085c2d7.285dd118-1563574924.2316726399082373898@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1085f1d8.67cb539e-914363729.-7372345439377524077@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10869dcf.6892b0ed-939898996.6452542733227950325@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1086d4cb.3225a4f7-937542688.8559693550587341120@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108703c9.76a9f3a1-739170139.-5593088713961621168@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10873289.84292891386576734.4529059811990670083@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10876981.876646252082481121.-9054344449590239318@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10879834.41196667807943825.2935001418037078487@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1087c716.148c07081696322480.1212213172437425056@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10880590.789e33eb-934159409.209986105385501007@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1088506a.8724c06e967365743.-8865134391709386800@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10887f35.74165dc0622767751.-1372591538903981550@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1088ae72.2aa487b91772838961.-5866767407076975391@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1088e4da.4fda9055-1960816366.586686237464905184@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10891444.485231cc1565017746.129904538736823807@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108969bc.12c08cc3-87729235.-987899060718644303@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108998eb.30c837401578626936.7343730492858905465@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1089cf69.7f2a0f5e1337483425.1544858920253988188@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108a0614.2ea2b90f1178365249.2991210040484113109@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108ab232.5d2e6ec4-1566170677.6187042697378367402@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108ae107.5058c2eb1642307010.3297308275952833328@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108b0fcb.83fed8a7-772618129.-1738708844507470989@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108b3ea2.1734a01e153806517.5929198590052630120@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108b7579.59ec9cf71112797456.-16824798949304130@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108ba49e.d2fc14e728335050.-523265652503957160@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108bd352.2e137345194141337.-5342346812366693415@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108c19b4.34f75267-1434196324.-2128681952192654509@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108c6002.592abbdd-1045382732.7763059565297050102@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108c96b1.3412c80d487686460.4971613456966799227@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108cc594.31d37cf1-1356485349.4335019773569216825@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108cf487.37fb25bb-279870406.-7289390465317166900@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108d2370.ce33df9837240760.3272839597603892046@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108d8137.7ec4a35a-79101456.5432627246471919266@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108db819.6d434f81605111143.6977804307997911066@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108de6e8.21787633-902022915.1245976305478928661@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108e15c3.3f1cbf30-1523016759.-2200187629833002456@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108ec976.47d30f0a-511299148.8138471265710844356@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108ef86b.5c1daf69-974960566.-8357586968852114375@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108f273c.2a101d311291882032.768166860669031522@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108f5654.787d3d901257650486.8060695098939768316@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108f8d37.4f43bece419513218.4729165362981208054@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108fbbfa.4f467a53764644690.-2069080625153766151@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108fe2f0.1521fc1e1623141400.-3933938088621483964@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10901997.705399d41860611700.-44069857426768773@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10905ffe.5771845f615879833.6551612722428972171@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10908ee2.2a98abdf-1461023559.1438183606831290613@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1090c5c5.2dadb4221850995109.-5352157570558282132@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1090fc48.6daa612a-2106591952.1795578592937284380@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10912b39.60b8f25d-1895473601.6785068649359627097@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1091819a.7e8ae2e9-2092250614.-297441197520472275@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1091b010.5489ed27-958545583.-507364459887842075@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1091e6d4.5adee1f9-2102657525.1640452426142893506@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109215d1.14f54dbc-609822343.3660518195275259792@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1092c33d.589e8862-892140742.4935227740640778812@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1092f254.7a729789-1539841638.-1731051927160904789@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10932993.3788ca4e1800431419.5811652251804352263@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10935ff7.313299cf108583252.8408816824374123627@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10938f2e.4932cb8c1383206936.7133022634788413312@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1093bdce.74b721e51691331931.-8097157551076925062@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1093f49d.29e32f5b-131039215.-389673425134518759@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10942b45.83e8a78a29223616.-4731818681332565681@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109469ae.3d9b6d3f1631018594.7048486657701404707@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109498ab.1e407024289070504.-8117802426740461071@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1094d723.7d3064f5578755566.431147872053248891@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10950dfd.34c16229-246970952.2283692531603875164@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109534f5.30c26222-1569089858.8026156070430754849@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109592fa.fdf8f1b-1832259826.-7819017888428078518@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1095c205.5f379ac7607926907.5311047404017106310@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10960084.44088972-1835941962.-6730603314008185241@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10963165.6cbe1421-2080524076.2933599427274809839@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1096d37a.447478bb-706493295.-2143964012534720651@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10970a54.530852ed807990680.4793808932797114417@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109740f0.5b0c5e63-1645130807.7285473644129756189@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10976fe0.2e53e5bf1269147720.-3252474189033267025@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10979ec6.63e95393-1082155685.228875035326059934@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1097d56d.4cc0c0cd-810237406.4417111166948808802@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1098045b.205be80d1497693355.-3165279727124772011@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10983b1f.7d593485-1824468241.7614920118450219538@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1098817c.472ee92c-1838876361.2230729891974108221@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1098b843.80501b72-1569769348.-1540421150568029917@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1098e732.25689b65-1007294178.-5884143751388168258@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10991ddc.da38d321633777619.1302707596546429987@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1099450a.5a6f1494-925668839.-3571893779219306118@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-10T07:14:55.536186+00:00
**Window**: last 75 min (since 2026-05-10T05:59:54.306478+00:00). **Seen**: 224. **Classified**: 75. **Urgent**: 0. **Skipped (dup)**: 149.
**By category**: VENDOR=75

### URGENT (0)
(none)

### OPERATOR_REVIEW (224)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10761775.71f2e216-68535896.-8356211020808905077@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10764637.43c0e77b-1950010775.6330006763048170178@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10767cf3.1900519f646280923.-2124958451996812676@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1076abd5.732cc12a-1815276942.566945625346765685@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1076db22.484d15fe1562358816.-6320092649258979006@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107709b9.2a39f8151025801782.-2456400052783220325@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107740a1.1929a3012139892095.7738314681290196884@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107786c3.8575838a1667297230.-6069808040943938757@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1077d502.51031268-279340824.9040932324158155706@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107803ff.cae7c972022716851.-7138745280516995325@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107832d4.51f054f8-535569556.-4553063731834990170@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10786982.581eccb2182495821.-5504294679770759426@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1078986c.602fc3c0-1202953830.5574110315414917845@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1078eea2.6be707ad994289721.-2174367595448551999@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10792531.859e6a54-1780316134.5919845253728135784@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10795406.6d91d530580053366.-2881018753887032068@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1079834f.42cf91e2589645519.6406949460695931312@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107a36e7.a8dfecb-584045734.-1650595269610048862@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107a65e0.19982497-950363662.1727185860205847881@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107a94ab.444965932010513590.1245743556846168183@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107acb72.5a67add21637741834.-5993554699093106733@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107b022b.5e01392f1434514366.-840924397257202271@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107b3104.82917cec-2132171084.-5798299503829023405@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107b5ff3.df6a61c-917112936.-7661052436706887657@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107b9e81.829415da225704165.1885529424217644259@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107be4d3.63043782-147015524.7690468666721420512@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107c13cf.33768fc81809284190.-5066702054602793027@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107c4a7c.85f47a96-1174632794.-404761250121328738@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107c7981.87f8e3a7156037358.-4065655093998435586@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107ca85a.62c6d12b-35368411.5982483719438912678@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107d0e21.702ccb81-458932270.8983512167907871566@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107d3cdb.18ab57e8-2060020528.9222614495774910928@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107d73ce.1403936a1409900633.5557599885151074605@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107da2bd.6b8907d2-1606454366.1933168471365745524@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107e46e5.634a5f811883345554.-5824174398649993271@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107e7dad.84a2fcdb792211538.8796159300670731394@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107eac67.298353df627252460.3281258178096103604@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107edb87.86a68647-1570580429.4252632163535924136@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107f11f7.7b97a023828459412.8725124931627830289@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107f48e5.4a0dca34-945231957.-6012946417158184024@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107f7fe3.51027885966065751.-1076833220213764679@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e107fdd79.131477fc1787196350.4451160707201297120@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10802b6e.49c7574e-868290997.-3909082967698487517@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10806233.3588d8e3-2055471194.5663891205829902704@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10808983.6eab8f5b1646178429.669870156168370823@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1080c081.6674a491-351969773.2013600144126649460@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1080ef84.60f48b1d578065662.8588176596964110256@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10814d20.1552ffde1672827307.4329554894114399294@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10817c12.34829ee1-1019976021.2752686037161472331@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1081b2c9.2da69228-1207101289.3178009960315678791@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1081e1ab.130294c0-922167460.5500956054220129722@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10828db0.23d108ad-740274587.-1982068575094726242@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1082c469.b19ffc11824221672.-436416241808858588@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1082f395.2b3d6cff-949162652.-4040424009649026881@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10832273.443d7fd61977102059.2345078947794573156@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1083593d.6a2aa9df-1743746738.2380961575283008955@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108387fd.2fff9715-1344705828.2233400930660712665@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1083b6d2.66786083-274258679.5364894828522958346@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1083fd80.59ee985b-1679023819.-628587876871750781@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10843dda.239de5c0-303623880.5078833311161600519@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10846ae7.7557d6801882543540.-3770866479016153295@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108499a9.548ae2aa-1223890467.2197021163058090468@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1084c89c.1631ddbb1841181158.8413089748125487174@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1084ff59.79ed29b91760259842.1620774095402574637@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10855d43.daa7cad-1089173226.-2109139751161172082@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1085940a.b05180b371759346.9111223934048086217@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1085c2d7.285dd118-1563574924.2316726399082373898@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1085f1d8.67cb539e-914363729.-7372345439377524077@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10869dcf.6892b0ed-939898996.6452542733227950325@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1086d4cb.3225a4f7-937542688.8559693550587341120@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108703c9.76a9f3a1-739170139.-5593088713961621168@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10873289.84292891386576734.4529059811990670083@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10876981.876646252082481121.-9054344449590239318@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10879834.41196667807943825.2935001418037078487@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1087c716.148c07081696322480.1212213172437425056@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10880590.789e33eb-934159409.209986105385501007@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1088506a.8724c06e967365743.-8865134391709386800@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10887f35.74165dc0622767751.-1372591538903981550@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1088ae72.2aa487b91772838961.-5866767407076975391@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1088e4da.4fda9055-1960816366.586686237464905184@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10891444.485231cc1565017746.129904538736823807@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108969bc.12c08cc3-87729235.-987899060718644303@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108998eb.30c837401578626936.7343730492858905465@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1089cf69.7f2a0f5e1337483425.1544858920253988188@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108a0614.2ea2b90f1178365249.2991210040484113109@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108ab232.5d2e6ec4-1566170677.6187042697378367402@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108ae107.5058c2eb1642307010.3297308275952833328@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108b0fcb.83fed8a7-772618129.-1738708844507470989@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108b3ea2.1734a01e153806517.5929198590052630120@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108b7579.59ec9cf71112797456.-16824798949304130@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108ba49e.d2fc14e728335050.-523265652503957160@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108bd352.2e137345194141337.-5342346812366693415@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108c19b4.34f75267-1434196324.-2128681952192654509@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108c6002.592abbdd-1045382732.7763059565297050102@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108c96b1.3412c80d487686460.4971613456966799227@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108cc594.31d37cf1-1356485349.4335019773569216825@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108cf487.37fb25bb-279870406.-7289390465317166900@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108d2370.ce33df9837240760.3272839597603892046@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108d8137.7ec4a35a-79101456.5432627246471919266@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108db819.6d434f81605111143.6977804307997911066@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108de6e8.21787633-902022915.1245976305478928661@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108e15c3.3f1cbf30-1523016759.-2200187629833002456@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108ec976.47d30f0a-511299148.8138471265710844356@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108ef86b.5c1daf69-974960566.-8357586968852114375@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108f273c.2a101d311291882032.768166860669031522@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108f5654.787d3d901257650486.8060695098939768316@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108f8d37.4f43bece419513218.4729165362981208054@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108fbbfa.4f467a53764644690.-2069080625153766151@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e108fe2f0.1521fc1e1623141400.-3933938088621483964@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10901997.705399d41860611700.-44069857426768773@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10905ffe.5771845f615879833.6551612722428972171@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10908ee2.2a98abdf-1461023559.1438183606831290613@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1090c5c5.2dadb4221850995109.-5352157570558282132@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1090fc48.6daa612a-2106591952.1795578592937284380@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10912b39.60b8f25d-1895473601.6785068649359627097@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1091819a.7e8ae2e9-2092250614.-297441197520472275@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1091b010.5489ed27-958545583.-507364459887842075@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1091e6d4.5adee1f9-2102657525.1640452426142893506@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109215d1.14f54dbc-609822343.3660518195275259792@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1092c33d.589e8862-892140742.4935227740640778812@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1092f254.7a729789-1539841638.-1731051927160904789@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10932993.3788ca4e1800431419.5811652251804352263@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10935ff7.313299cf108583252.8408816824374123627@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10938f2e.4932cb8c1383206936.7133022634788413312@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1093bdce.74b721e51691331931.-8097157551076925062@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1093f49d.29e32f5b-131039215.-389673425134518759@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10942b45.83e8a78a29223616.-4731818681332565681@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109469ae.3d9b6d3f1631018594.7048486657701404707@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109498ab.1e407024289070504.-8117802426740461071@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1094d723.7d3064f5578755566.431147872053248891@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10950dfd.34c16229-246970952.2283692531603875164@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109534f5.30c26222-1569089858.8026156070430754849@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109592fa.fdf8f1b-1832259826.-7819017888428078518@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1095c205.5f379ac7607926907.5311047404017106310@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10960084.44088972-1835941962.-6730603314008185241@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10963165.6cbe1421-2080524076.2933599427274809839@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1096d37a.447478bb-706493295.-2143964012534720651@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10970a54.530852ed807990680.4793808932797114417@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109740f0.5b0c5e63-1645130807.7285473644129756189@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10976fe0.2e53e5bf1269147720.-3252474189033267025@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10979ec6.63e95393-1082155685.228875035326059934@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1097d56d.4cc0c0cd-810237406.4417111166948808802@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1098045b.205be80d1497693355.-3165279727124772011@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10983b1f.7d593485-1824468241.7614920118450219538@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1098817c.472ee92c-1838876361.2230729891974108221@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1098b843.80501b72-1569769348.-1540421150568029917@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1098e732.25689b65-1007294178.-5884143751388168258@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10991ddc.da38d321633777619.1302707596546429987@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1099450a.5a6f1494-925668839.-3571893779219306118@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1099aa9b.83c8e5202035345600.896982907063786676@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1099d964.806ac1602097835534.-3172694263067548307@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109a0877.2c1f32be-571070634.-983610468509519874@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109a46f3.cf7f5b32063541459.-213934774515966198@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109afac4.42970e70-1308068398.8808938359703544229@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109b29b6.2569e44e1011153302.1894310261906744652@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109b589e.6479e0b1725372754.844297095869175446@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109b8f50.82185953-1919329137.4743507628153977225@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109bbe6a.c3c7b9e471913620.-7136951832405160737@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109bed09.2c1a077c-384169628.4783912384918448358@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109c23f7.6108550c-1891637382.-8748177572866156110@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109c5ac1.39670aaa1035041456.-2686772367412548973@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109ca0f1.3daa0c401558566827.-95986672369233687@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109cc7eb.75d82dcb90441273.3066642213345124089@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109cf6cb.51904c89795997470.2804397249533713459@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109d2dab.7c9962871689145449.-5454519579540855435@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109d5cb9.4b337363995587822.-6154289754856807553@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109dbaa2.5e067bd9-610904862.-9176635001845260932@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109df138.2b2fd48c-1307493043.108454330319024796@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109e27dd.774d49591400976890.-4152140416454116683@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109e56c4.6c2c8b071797345854.1356384199558328022@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109f02f2.d3e21ed1687166161.-7755587083681620372@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109f39a5.4fa7790f-2001160106.-6587534566260638901@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109f6898.52918d7a99777136.-3851559205667763445@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109f9793.4d93e959-430591047.-104632008635622559@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109fc840.606a4a0e-1648059335.1060644491962381606@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e109ffcff.f2af308-294669572.-8578259597272696469@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a033c9.117e47cf1225217027.-8637096555579486869@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a081ec.488b0d241638427672.-965051050109814284@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a0c07b.6bf478d8264278118.5389253299768758251@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a0f751.27e2fcb4-1405138053.-4376511562522184958@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a11e39.cbe70ad-1544711503.-4878594411314755738@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a1550e.6270f9a0-85096690.-4074362555794065792@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a183e2.bad1ec4328581898.-6262420484000031084@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a1e1ce.4bd9c581-998238316.-8895515218092656917@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a210f1.61d7898a-173213232.1736930221839016496@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a24775.8633ef661488559955.-8289713109086094129@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a27e2f.56b721ba360458064.4521064169673980450@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a3229d.4f7cb5fa-1245444235.7289917573752588940@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a3591f.53c56640-863680126.2022762432490840792@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a38817.7f561abd-1201069386.5731769479842185246@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a3b71b.3c4e4a63-61680899.-2281830400591343860@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a3edc6.10736d03-318926760.-3611653513026895124@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a42471.42000cbe269705253.-2504751277032837739@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a44b81.6ddcb9c91867776021.-2137428638197434589@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a48246.25d40445-875706292.7767705003804853955@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a4c8a4.1b7c80e1735410807.-6801630787781939039@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a4f77f.435ff6de-1420495000.-8761631956245249061@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a52678.1bf5912e1216488171.-6774667741599453674@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a55d32.5638a731-1567208374.-4293555539676267164@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a58c5c.60c4daec-2074209080.2342048425858418425@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a5ea67.63e5a7dd-100917977.-955886333910277875@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a61159.5a9768c0372216930.-1860313011874372306@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a64811.59914edf-844829974.259229689074477069@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a67f0a.4d177592568963106.4680132903394917435@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a732aa.5354b1b5-1404884778.-3004578447334196702@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a76185.53d8514f-162940427.-1521895750623500795@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a79873.1e5e2eb9614680064.5429049545032108441@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a7c715.55fe74ef-1421419689.8334332657654864662@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a7f62f.2b25b73b-1105661326.-7730973321588774182@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a824ee.50036e81-1557244986.-2612325154651249089@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a85b93.49d1f7d4-122291425.-7231191138730637138@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10a99479.4b5fb402-2023797132.6029007079789133153@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10aa8edd.edaeaa7-802043633.-5415037480057057569@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ab88c0.1d12f379939184116.1489191042650283563@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ac929c.47c18e58453503997.-228905133281313417@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ad7d15.57958787564556559.4468144279635815153@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ae7781.5350a5eb-1456852793.-6080264761178642496@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10af69bd.55a6e7bb545500228.-6812678141257050436@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10b073a3.336af2e0469586948.7847460293643869630@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10b17e0e.8800745e864753218.2797921315344821278@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10b27839.21f5c3d5547103455.-3484903502146168662@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10b3726c.1b037176-289652683.-6920446094701621134@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10b464ba.42efe8bc1417281710.4458655839787831214@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ba29f8.1776781e396302036.-6692493946574830342@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-10 07:30 UTC

Total ingested last 75min: 180
By category: {'PROVIDER': 180}
By verdict: {'INFORMATIONAL': 180}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (180)

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

## Triage run -- 2026-05-10T08:14:55.609041+00:00
**Window**: last 75 min (since 2026-05-10T06:59:55.553405+00:00). **Seen**: 52. **Classified**: 42. **Urgent**: 0. **Skipped (dup)**: 10.
**By category**: VENDOR=42

### URGENT (0)
(none)

### OPERATOR_REVIEW (52)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ac929c.47c18e58453503997.-228905133281313417@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ad7d15.57958787564556559.4468144279635815153@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ae7781.5350a5eb-1456852793.-6080264761178642496@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10af69bd.55a6e7bb545500228.-6812678141257050436@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10b073a3.336af2e0469586948.7847460293643869630@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10b17e0e.8800745e864753218.2797921315344821278@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10b27839.21f5c3d5547103455.-3484903502146168662@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10b3726c.1b037176-289652683.-6920446094701621134@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10b464ba.42efe8bc1417281710.4458655839787831214@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ba29f8.1776781e396302036.-6692493946574830342@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10bee59e.f71bb66-1021622222.-3894465789260112538@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10bfe25f.167be959-806760698.-5986460614398350063@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10c3bb40.39209d83786794469.9004949976879307162@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10c4bd53.7c57dc3b1106143658.-2928646407922807873@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10c5b77a.4b270c84-112989727.6662558058953090054@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10c6a9a8.5b112049-1014039070.-2337593831800573110@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10c79c04.4b075f541980239208.1157034169080749564@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10c89611.4d11f1d2517779046.-4909297761371879719@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10c9885c.1bf0b53b-1763863759.7251218168773792390@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10cb6d26.6338de1b-891515812.-6428678226440901296@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10cc57c2.5881c3cd520031050.-8273628399547406242@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10cd59cc.782d4ad82136332633.-9059163313004944441@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10d20de8.41ee2733-1302128788.-5862507842498225948@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10d307f6.5959824f-3022069.2912352973434685516@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10d3fa44.14420128-670869654.8536483769557162198@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10d4ec8c.4e4de899749597485.-2549377325214295851@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10d5e6c5.145c02a11318033906.2841272108143604191@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10d6d90e.4ce52f87-360444603.-327614965469242984@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10d7c3ac.4298b267-1185545499.-1684482545362688007@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10d8cd50.7f5cdf011306669597.8810242724917105079@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10d9c777.1a658502462610395.2258061490029515581@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10dac199.8a2740281583845984.-5920352699174130036@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10dbb40f.6b74a86b113851974.-8057655402863671700@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10dca64f.86c54f0a-2116147531.-5656187376263990344@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10dda061.2b088ca8-338839556.2484945150241239243@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10df857d.679b537f-2132521017.7335337325474041128@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e07782.64e8ead02027129586.-5365786023947140840@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e1723f.65ba317a1091918483.9205707107282326374@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e26c0b.7bb000fc263864575.-7562968955915422554@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e62dc2.5ac16f4e-475803972.6924083457984513013@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e7282d.6d8bfa2d-450253195.1525575173775714299@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e8226b.62916e22932858552.-2709078589785311442@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e90d21.4a249e411259544246.2293240877421297299@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e9ff38.6bdbcb02-56834119.5866074742295888364@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10eaf963.578e5d28-1352254496.-1401246456545743288@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ebebdc.2069a622-1059291926.7463608768527546248@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ece62f.74cb4e50-1443622164.-3828525775705869141@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ede060.443c360c-1687477536.-672344646918791103@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10eed28d.425707a1-997854236.2429195216821076378@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10efc537.d6f9300-235658174.2631958092568185240@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10f0b763.4281bb31-376285992.-6073802959309344052@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10f1b190.367f1130-431799107.3325609989726873723@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-10 08:25 UTC

Total ingested last 75min: 49
By category: {'PROVIDER': 49}
By verdict: {'INFORMATIONAL': 49}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (49)

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

## Triage run -- 2026-05-10T09:14:55.718802+00:00
**Window**: last 75 min (since 2026-05-10T07:59:55.621808+00:00). **Seen**: 59. **Classified**: 46. **Urgent**: 0. **Skipped (dup)**: 13.
**By category**: VENDOR=46

### URGENT (0)
(none)

### OPERATOR_REVIEW (59)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e62dc2.5ac16f4e-475803972.6924083457984513013@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e7282d.6d8bfa2d-450253195.1525575173775714299@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e8226b.62916e22932858552.-2709078589785311442@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e90d21.4a249e411259544246.2293240877421297299@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10e9ff38.6bdbcb02-56834119.5866074742295888364@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10eaf963.578e5d28-1352254496.-1401246456545743288@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ebebdc.2069a622-1059291926.7463608768527546248@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ece62f.74cb4e50-1443622164.-3828525775705869141@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ede060.443c360c-1687477536.-672344646918791103@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10eed28d.425707a1-997854236.2429195216821076378@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10efc537.d6f9300-235658174.2631958092568185240@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10f0b763.4281bb31-376285992.-6073802959309344052@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10f1b190.367f1130-431799107.3325609989726873723@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10f38e9a.65b10cd31076207784.-4187153768221963832@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10f48940.66f231b4-1448358779.-8931992336350964968@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10f57b9b.3ebe95d0-1056546072.781967758115222531@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10f67669.7ba5dc68325105933.250963252201621796@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10fa30de.2bca3838-1391903551.-8999019847420745382@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10fb1b00.54db0450-1559160565.3189571354479214428@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10fc155d.338385042091453659.-226163814350278655@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10fd0799.31c853d0413062122.-5060578022764502932@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10fe01f6.763d84192011337447.-3355153136275758674@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10fefc06.4be1bfdb998234575.-7015876996227784481@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e10ffe695.78263de9718548303.4599198743110687913@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1100e0e7.7d7e77ce808315655.-6512696028811173768@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1101daf4.7f2618e4-1886946419.-7117136061783403280@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1102d50a.81323782-914249124.3830085183930681255@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1103bf7c.45fed2e3288117069.-8729777076164386763@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1104b9e7.3f786959-1766032616.-4812520981932110394@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1105ac93.3d3c0eb0-1242659765.-987675009334536575@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11079149.58096609-89713775.-5917279413744332178@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e110883c7.157eb92b519868240.6130344458896327827@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11097e02.46eab543580087871.-1222647263976186745@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e110a7824.edad2e71490607270.-2911154986795481872@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e110e31f2.4f91a7e8640882985.-5928230362766278315@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e110f2423.420ff27f-1898258296.2907420849355638264@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e111016d0.240689e9-174428546.-492875278617740928@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e111110e3.3770f6cd73174067.-5311134599400841954@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e111203e7.58fc349e-1249172517.2078628620848599660@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1112f64a.f4c23c2215463234.-6308338394791853037@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1113e8d2.656d9f54-487026361.5128186577097544963@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1114e355.4c580828-1279756208.7812204783295010028@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1115e526.77f1647f681862413.9109939782444083859@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1116d76e.86a96af41389081299.-6438039228838205053@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1117ca0f.6a77e332-178644475.4029264327248404865@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1118bc57.14b3cd6a698579098.-3761305484551088017@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1119ae6c.740e3105-1432825187.-3906609851965181784@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e111b9330.2fc7ccf5507613847.-8860834658892262262@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e111c856c.7fc7f51a744917141.-3637910221744279269@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e111d7fad.4856e2af-1755285949.8923184115609455776@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e111e72a7.13aa2035-853268126.-1042001496409911526@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e112224c4.3dfe7b0e-340414176.-9211145390756136896@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11231f2f.69d788f5324600568.-3317071611883539529@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11242902.d223b40694025331.1711571252192672662@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11251b5c.319dcf121508843377.6699133416193598727@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11260d93.69febc13598993926.-4527500180200944117@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e112707b7.73087ad11061979633.4329060012978348961@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11280221.87611eb1-2116206222.6405145391052912491@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1128f43c.6cd2652f1071969424.-3853375496664292843@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-10 09:26 UTC

Total ingested last 75min: 60
By category: {'PROVIDER': 60}
By verdict: {'INFORMATIONAL': 60}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (60)

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

## Triage run -- 2026-05-10T10:14:55.814986+00:00
**Window**: last 75 min (since 2026-05-10T08:59:55.726728+00:00). **Seen**: 42. **Classified**: 31. **Urgent**: 0. **Skipped (dup)**: 11.
**By category**: VENDOR=31

### URGENT (0)
(none)

### OPERATOR_REVIEW (42)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e111c856c.7fc7f51a744917141.-3637910221744279269@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e111d7fad.4856e2af-1755285949.8923184115609455776@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e111e72a7.13aa2035-853268126.-1042001496409911526@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e112224c4.3dfe7b0e-340414176.-9211145390756136896@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11231f2f.69d788f5324600568.-3317071611883539529@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11242902.d223b40694025331.1711571252192672662@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11251b5c.319dcf121508843377.6699133416193598727@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11260d93.69febc13598993926.-4527500180200944117@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e112707b7.73087ad11061979633.4329060012978348961@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11280221.87611eb1-2116206222.6405145391052912491@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1128f43c.6cd2652f1071969424.-3853375496664292843@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1129ee90.44f3b48a-1982557838.104598506109854888@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e112ae910.6199d3562037197732.4442487585021267545@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e112bdb35.828e302b1833122277.2053162092047154231@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e112cd53e.42b6f38c306987204.-1641237446859132441@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e112dc7b7.7f51f64c-961468086.-2731925450971548096@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e112fa442.5ce12e32529384159.-5022622040927272514@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11309e9b.7ba4b21f-561663957.6953190223784478563@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e113190f8.5284d9b7-700802332.8812402139395884749@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11328b38.f9050da252164163.1521248344454039581@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e113644ec.4a9912d0296753692.-6454466174365025822@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11373778.66d3867e-316811100.81627207719407899@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1138316f.3967a150-2058280009.296604841102560221@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e113923ed.670859b91872783769.-3837673101298288599@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e113a1908.1324ddeb-1827510683.7147939616438218548@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e113b1447.442bccd5-1161546069.3348840835872671417@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e113c0998.182d155f-1858145902.-4852674487940129005@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e113d11d7.33d9f069-789319610.-8000279033262726462@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e113e07f0.80eae13b687747071.-505246956826379936@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e113ef921.74ae7149-536868543.-158418712208012538@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e113feb20.5a7b9ba6-1731697584.-7677641358509343245@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1140dd91.64f5dec71808006918.-2852049422538101957@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1141df7e.17fd8632-2044228724.9199674164045765308@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1143bc5c.3125bd8b464106170.-5768962283002954834@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1144aeb6.28d3fd54900154246.2164926175957049484@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1145a0ef.2720b4be-586511843.3934839680285910378@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e11469b31.4e1011eb781838755.4608271765247196214@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e114a5d3c.3bc6b5f4869256066.8607332397106719848@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e114b4f70.7f9a5fc5-692073132.-7548132986918769858@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e114c3a1a.514b5088-505013062.5531897760258033467@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e114d343c.50d867b6-529913334.-7005744222247173860@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e114e26fd.59a2e2352067738313.5898324385480802897@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-10 10:25 UTC

Total ingested last 75min: 36
By category: {'PROVIDER': 36}
By verdict: {'INFORMATIONAL': 36}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (36)

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

## Hourly review 2026-05-10 11:25 UTC

Total ingested last 75min: 0
By category: {}
By verdict: {}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (0)

_(none this window)_

---

## Hourly review 2026-05-10 12:25 UTC

Total ingested last 75min: 0
By category: {}
By verdict: {}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (0)

_(none this window)_

---

## Hourly review 2026-05-10 13:26 UTC

Total ingested last 75min: 0
By category: {}
By verdict: {}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (0)

_(none this window)_

---

## Hourly review 2026-05-10 14:30 UTC

Total ingested last 75min: 0
By category: {}
By verdict: {}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (0)

_(none this window)_

---

## Hourly review 2026-05-10 15:25 UTC

Total ingested last 75min: 0
By category: {}
By verdict: {}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (0)

_(none this window)_

---

## Triage run -- 2026-05-10T15:50:37.746156+00:00
**Window**: last 75 min (since 2026-05-10T14:35:37.658560+00:00). **Seen**: 6. **Classified**: 6. **Urgent**: 0. **Skipped (dup)**: 0.
**By category**: VENDOR=6

### URGENT (0)
(none)

### OPERATOR_REVIEW (6)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1292baaf.5f7c67b6-2040103931.-6621349790371549203@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1292d258.61a74b19446739124.-5919937840389227463@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1292e996.1fad534e-472632744.1508558464752845687@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e129308be.ee69fff294513058.89426934267484691@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12932044.667fba85-1670995395.6161757196128182185@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e129366d8.24798e51198479562.-5031141435566978412@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Triage run -- 2026-05-10T16:50:37.817834+00:00
**Window**: last 75 min (since 2026-05-10T15:35:37.749053+00:00). **Seen**: 59. **Classified**: 53. **Urgent**: 0. **Skipped (dup)**: 6.
**By category**: VENDOR=53

### URGENT (0)
(none)

### OPERATOR_REVIEW (59)
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1292baaf.5f7c67b6-2040103931.-6621349790371549203@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1292d258.61a74b19446739124.-5919937840389227463@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1292e996.1fad534e-472632744.1508558464752845687@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e129308be.ee69fff294513058.89426934267484691@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12932044.667fba85-1670995395.6161757196128182185@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e129366d8.24798e51198479562.-5031141435566978412@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12937e10.3fb2f7a6-618981681.5736734548591115212@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12939592.43f4b526-2142143879.-522602512815531139@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1293b547.5d0a49831899894331.-6152805402922757919@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1293d40e.653a8095-1446183701.-6295717668756059679@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1293ebd2.32b7d8f8508840849.-7422407392446266406@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12940ae2.6fde5c541795635037.4314837357391123831@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12942a3a.2590231f-799491124.6859080193877573875@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12945906.75256f5d-1043250435.9057223361485606700@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1294708f.282384981093919068.2180410107796726689@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12949803.1caeb27f169502107.7908550170379409946@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1294b6e4.1df59881962645745.-7503840476223981071@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1294ce6c.15d47a941364109450.-7905927014634434761@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1294fd9b.29c9eef7-1694937939.6941600182070763915@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12951ce2.3062d266329513947.-7651085759615695156@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12953bfd.3c7fd7ac-390444656.-8565037534685528400@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1295649c.624116681935116559.2847930405564680297@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1295c272.2e8440451165807450.-7430346282685098748@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e1295d9fc.cbfc158519602606.-8600239844111611702@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e129600fd.8a0f2a74-1431922857.-4958378771445097737@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12961875.3e5f8241-2034753028.-1970934017601134574@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12a62955.5c30e0ef-1802350227.8093518405306135665@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12a72377.5216f25a1550042850.-2503618132770613248@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12a88b5e.76c49677-1227246385.-3501396131931030574@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12a985a6.807ea725-1540870333.-7933441459232125352@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12aa780a.66d7d5d3-1953950200.-1158616946216520212@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12ab6a7a.5f6dac7e-1847747388.-4047235928000876422@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12ac6ca9.31ef18bc940269328.-8484657730506510164@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12ad5769.18752419-2073253731.-8304297708064396872@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12ae5181.824bdc3d645070676.8704061304845845982@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12af4c0c.595666db-1752630664.-5941137803027791344@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12b0471e.3d369ea01122361459.7962412350458611465@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12b14168.1b3c96b21633273204.-5115963763464022382@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12b233bb.5cffbf981786379846.5374155232000672289@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12b32dbd.114957e11480562864.-5996584889532901342@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12b42039.116c3887385622660.-965323055592434941@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12b5fcd2.61d685381805552767.4293186053579028721@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12b6f735.18d84030-1572652279.6215263709986149716@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12b7e98f.b578bed-1551426326.3149667267247114382@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12b8e3b4.5b0d1c511206617903.-4371347411209408370@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12bca586.6a303499-920221147.3762900167493358797@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12bd8ff0.4ea93e94471044067.7996201916448807280@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12be8a2d.2ee0c3ed-1962731963.5953770528776093217@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12bf7ccc.6521ac89-810234005.-7469589976141373763@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12c07696.715a920b1902267298.-7788372771858162230@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12c16914.7086583d807923195.7559399568174582798@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12c25b22.d97295f32564948.-285407201974313521@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12c35597.19d6c430-56244095.2152789618044361935@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12c4577b.6719e6cb1292047727.4129839939160119524@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12c549f3.78cea42b-1232914362.6605096592109252691@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12c63c32.457a1c92-351230177.-3497497624061208002@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12c73e2f.37e0f0ee-707916339.-7866601944261843486@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12c8289b.21cb873e930622632.-2497511905493790233@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review
- [VENDOR] conf=0.85 from=mailer-daemon@mail.zoho.com subject='Undelivered Mail Returned to Sender' message_id=<19e12ca1537.ff8e1c1-162377656.1293542296346699115@zohomail.com>
    - sender_domain_match:vendor=mail.zoho.com
    - default_no_urgent_signal:operator_review

### AUTO_ACK_OK (0)
(none)

### INFORMATIONAL (0)
(none)

## Hourly review 2026-05-10 17:18 UTC

Total ingested last 75min: 53
By category: {'PROVIDER': 53}
By verdict: {'INFORMATIONAL': 53}

### URGENT (require operator attention) (0)

_(none this window)_

### OPERATOR_REVIEW (next session) (0)

_(none this window)_

### AUTO_ACK_OK (0)

_(none this window)_

### INFORMATIONAL (53)

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
