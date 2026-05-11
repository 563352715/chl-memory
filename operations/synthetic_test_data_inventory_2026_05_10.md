# Synthetic Test Data Inventory — 2026-05-10

**Batch tag:** `synthetic_batch: "smoke_test_2026_05_10"`
**Generator:** `scripts/seed_synthetic_test_data.py`
**Purpose:** End-to-end / Playwright smoke testing without touching real data. Every doc carries `synthetic: true` so production reports (P&L, vetting funnel, broker-of-record dashboards) filter them out.

---

## Quick reference

| Command | Effect |
|---|---|
| `python scripts/seed_synthetic_test_data.py` | DRY-RUN (default) — prints what would be created, no writes |
| `python scripts/seed_synthetic_test_data.py --apply` | Commit the seed. Refuses (exit 2) if batch already exists |
| `python scripts/seed_synthetic_test_data.py --clean` | Delete every doc tagged with this batch across all 6 collections |

**Re-seed cycle:** `--clean` then `--apply`.

---

## Carriers (5 — `db.carriers`)

| # | id | MC | DOT | Company | Equipment | State |
|---|---|---|---|---|---|---|
| 1 | `synth-carrier-001` | `MC-9999001` | `9999001` | Acme Test Freight LLC | van | GA |
| 2 | `synth-carrier-002` | `MC-9999002` | `9999002` | Beta Synthetic Transport | reefer | TX |
| 3 | `synth-carrier-003` | `MC-9999003` | `9999003` | Gamma Mock Carriers | flatbed | OH |
| 4 | `synth-carrier-004` | `MC-9999004` | `9999004` | Delta Stub Logistics | tanker | IL |
| 5 | `synth-carrier-005` | `MC-9999005` | `9999005` | Epsilon Demo Trucking | heavy_haul | FL |

All carriers: `vetting_status=VETTED`, `authority_status=A`, `safety_rating=satisfactory`. Phones `+15555550100..104`, emails `carrier{N}@synthetic-test.local`.

## Drivers (3 — `db.drivers`)

| # | id | Name | Assigned carrier | Phone |
|---|---|---|---|---|
| 1 | `synth-driver-001` | Test Driver One   | `synth-carrier-001` (Acme)  | `+15555550200` |
| 2 | `synth-driver-002` | Test Driver Two   | `synth-carrier-002` (Beta)  | `+15555550201` |
| 3 | `synth-driver-003` | Test Driver Three | `synth-carrier-003` (Gamma) | `+15555550202` |

All drivers: `active=True`, `cdl_class=A`, stub CDL state IDs.

## Loads (10 — `db.loads`)

| # | BOL | Status | Origin → Destination | Equip | Miles | Rate |
|---|---|---|---|---|---|---|
| 1 | `CHL-TEST-001` | available  | Atlanta GA → Houston TX       | van        | 850  | $1500.00 |
| 2 | `CHL-TEST-002` | available  | Houston TX → Chicago IL       | reefer     | 1080 | $1944.44 |
| 3 | `CHL-TEST-003` | booked     | Chicago IL → Columbus OH      | flatbed    | 340  | $2388.89 |
| 4 | `CHL-TEST-004` | booked     | Columbus OH → Jacksonville FL | tanker     | 780  | $2833.33 |
| 5 | `CHL-TEST-005` | booked     | Jacksonville FL → Atlanta GA  | heavy_haul | 350  | $3277.78 |
| 6 | `CHL-TEST-006` | in_transit | Dallas TX → Memphis TN        | van        | 450  | $3722.22 |
| 7 | `CHL-TEST-007` | in_transit | Memphis TN → Nashville TN     | reefer     | 250  | $4166.67 |
| 8 | `CHL-TEST-008` | delivered  | Nashville TN → Charlotte NC   | flatbed    | 410  | $4611.11 |
| 9 | `CHL-TEST-009` | delivered  | Los Angeles CA → Phoenix AZ   | tanker     | 370  | $5055.56 |
| 10 | `CHL-TEST-010` | delivered  | Seattle WA → Denver CO        | heavy_haul | 1330 | $5500.00 |

Each load: weight 25k-45k lbs spread, pickup within next 7 days, delivery 1-3 days after pickup. Booked/in-transit/delivered have `assigned_carrier_id`, `carrier_name`, `agreed_rate`. Delivered also carry `delivered_at` + `synthetic_pod_url` placeholder.

### Federal-compliance fields (49 CFR §371.3)
All loads populate: shipper name/address/contact, consignee name/address/contact, pickup/delivery dates, weight, commodity, rate, distance, and `broker_compensation_disclosure` note.

## Rate confirmations (8 — `db.rate_confirmations`)

One RC per non-available load (`synth-rc-001`..`synth-rc-008`). Status: `signed` for in-transit/delivered, `sent` for booked. `pdf_base64=None` (no synthesized PDF body).

## Broker invoice + NOA (1 + 1)

`synth-inv-001` (`INV-SYNTH-001`) and `synth-noa-001`, both attached to the first delivered load (`CHL-TEST-008`). Marked `factored=True` with assignee "Synthetic Test Factor". Gives the BOL search feature documents to find for that BOL.

---

## Verification — applied 2026-05-10

```
$ python scripts/seed_synthetic_test_data.py --apply
...
Inserted (total: 28)
  carriers                  5
  drivers                   3
  loads                    10
  rate_confirmations        8
  broker_invoices           1
  notices_of_assignment     1

Seed complete.
```

Direct mongo count by `synthetic_batch="smoke_test_2026_05_10"` matches inserted counts on all six collections. Second `--apply` correctly refuses with exit 2 and `ERROR: batch ... already has rows`.

## Cleanup

```
python scripts/seed_synthetic_test_data.py --clean
```

Removes every doc with `synthetic_batch="smoke_test_2026_05_10"` across `carriers / drivers / loads / rate_confirmations / broker_invoices / notices_of_assignment`. Does NOT touch any doc without the batch tag (verified by `test_clean_does_not_touch_unrelated_rows`).

## Tests

`backend/tests/test_seed_synthetic_data.py` — 15 tests, all passing:
- Counts match spec (5/3/10/8/1/1)
- Every doc tagged `synthetic=true` + correct batch
- Carriers have MC/DOT/vetting_status/email/phone in the required ranges
- Loads cover lifecycle 2/3/2/3 (available/booked/in_transit/delivered)
- Every load has a unique `CHL-TEST-NNN` BOL
- Delivered loads have carrier + `synthetic_pod_url`
- Dry-run writes nothing
- `--apply` creates expected counts
- 2nd `--apply` refuses (exit 2)
- `--clean` removes batch rows AND leaves unrelated rows untouched
- `--apply` and `--clean` are mutually exclusive
