# Payments Button Study -- 2026-05-08 (EOD-16-late)

## 0. OPERATOR CORRECTIONS 2026-05-08 EOD-16-late (supersede sections 5-9 below)

**Correction A:** "We factor our invoices"

Initial Framing A in section 5 ("Receivables / shipper AR aging") is conceptually
wrong. CHL never collects shipper-AR -- the factor collects directly from the
shipper. Real money flow:

  1. Load completes -> CHL generates invoice.
  2. CHL submits invoice to factor (RTS / OTR / Apex / Triumph).
  3. Factor advances CHL ~80-95% of invoice within hours.
  4. Factor collects from shipper directly.
  5. Factor releases reserve (~5-20% minus fee) to CHL when shipper pays.

The corrected sub-tabs for "Cash Flow" (if not hiding outright):

- **Factor Submissions** -- invoice -> submitted -> advanced (status, amounts,
  factor name, age). This IS what the operator wants visibility into; replaces
  Framing B as the primary view.
- **Reserve Releases** -- pending releases + completed releases (tracks factor
  collection latency from shipper).

The original "Receivables" framing is OBSOLETE. Drop it.

**Correction B:** "Stripe stays wired up but idle so we can pivot"

Do NOT deprecate Stripe Checkout (`POST /api/payments/checkout`) or Stripe
Connect (PendingCarrierPayouts). Keep both paths wired and idle. Operator
preserves optionality: if CHL ever needs to take direct shipper payments
(e.g., a shipper insists on credit card via CHL rather than ACH-to-factor)
or pay a carrier directly (e.g., factor declines a load), the rails are
still warm.

Section 6 and 7 recommendations to "DEPRECATE Stripe Checkout" and the
implied removal of Stripe Connect pay-now path are SUPERSEDED. Both stay.

**Correction C:** Operator-explicit: "If we don't need a Payments button,
maybe we can hide it somewhere?"

Operator is green-lighting code action. Sub-section 6.5 below documents the
chosen execution path: hide the Payments tab from primary navigation; leave
all code + backend modules + db.payments collection in place; PaymentsView
remains accessible via direct URL hash for dev/diagnostics if ever needed.

## 6.5. Updated execution path (HIDE, not delete)

Operator-confirmed action 2026-05-08 EOD-16-late:

| Action | Status |
|---|---|
| Top-bar Payments tab nav entry | REMOVE from primary navigation |
| `PaymentsView` component | KEEP (no breaking change) |
| db.payments collection | KEEP (load-bearing for 7+ modules) |
| `/api/payments/*` backend endpoints | KEEP (other code depends) |
| Stripe Checkout `/api/payments/checkout` | KEEP wired idle |
| Stripe Connect (PendingCarrierPayouts) | KEEP wired idle |
| Carrier-side "My Payments" tab | KEEP for now (carrier-facing surface; separate decision pending) |
| New "Cash Flow" tab | OUT OF SCOPE this iter -- defer until self-healing AI ships first |

This is the lowest-risk execution: removes the operator-irritation surface
without touching any data or money paths. PaymentsView can be unhidden in
~30 seconds by re-adding the nav entry if operator ever needs it back.

Code change: delete the single nav-array entry for `id: "payments"` in
`frontend/src/App.js`. No imports removed (PaymentsView still defined).
No backend touched.

---

## 1. Operator question (verbatim)

> "study why we might need the payments button if we never intend to pay anyone except for t use factoring for all payments"

## 2. Operator intent (dev's read)

CHL pays carriers on delivery, but never directly cuts checks. The brokerage
uses third-party factoring providers (RTS Capital, OTR Capital, Apex Capital,
Triumph) to advance the carrier the load proceeds on CHL's behalf, then
collects on the shipper invoice 30-60 days later. Carrier-side AP is therefore
not CHL's job; it's the factor's job. From that lens, a UI "Payments" tab that
implies CHL writes carrier checks is misleading and probably dead weight.

The study question is: what does the existing Payments tab actually do, who
consumes it, and -- given the factoring-first carrier-payout architecture --
should it be KEPT, RENAMED, or REMOVED.

## 3. Current implementation -- what the Payments button shows

### 3.1 Frontend entry point

- `frontend/src/App.js:2719` -- top-bar tab definition:
  `{ id: "payments", label: "Payments", icon: Money }`.
- `frontend/src/App.js:2851-2853` -- routes the tab to `<PaymentsView ...>`.
- `frontend/src/App.js:4820-5045` -- `PaymentsView` component definition.
- `frontend/src/App.js:2720` -- comment from iter 138.1:
  *"Settlements hidden from top-bar (folded conceptually into Payments +
  Accounting; still accessible via direct activeTab if needed)."*

### 3.2 Header label

`frontend/src/App.js:4889-4893` -- header is literally **"Driver Payments"**:

```
<h2>Driver Payments</h2>
<p>Manage payments, schedules, and process transactions</p>
```

The top-bar reads "Payments" but the page header reads "Driver Payments".

### 3.3 Data shape

`PaymentsView` calls `GET /api/payments` (`server.py:3695-3705`) and renders a
table of `db.payments` rows. Each row has columns:

| Column            | Source field                |
| ----------------- | --------------------------- |
| Driver            | `driver_name`               |
| Load Ref          | `load_reference`            |
| Gross             | `gross_amount`              |
| Net Payment       | `net_payment`               |
| Terms             | `payment_terms` (e.g. net_30) |
| Expected Date     | `expected_payment_date`     |
| Status            | pending / processing / completed / failed |
| Actions           | View / "Pay Now" (Stripe checkout) |

Summary cards (`App.js:4904-4922`):
- Total Payments (sum of all `net_payment`)
- Pending Payments (sum where status=pending)
- **"Paid to Drivers"** (sum where status=completed)

### 3.4 Backend write path

`db.payments` is populated by:

- `delivery_completion.py:215-251` -- on every delivered load, builds a
  `payment_doc` with `gross_amount`, `broker_commission` (CHL takes ~10%),
  `quick_pay_fee`, `fuel_advance`, `detention_pay`, `net_payment` to driver.
  Status is set `paid` with `paid_via: "MOCK_ACH_TRANSFER"`. So today this is
  a **placeholder** AP path -- nothing real settles.
- `server.py:3654-3691` -- `POST /api/payments` lets a broker manually create
  a DriverPayment record (the "Create Payment" button at `App.js:4895-4901`).
- `server.py:3715-3795` -- `POST /api/payments/checkout` opens a Stripe
  Checkout session against the `net_payment` amount. The "Pay Now" button at
  `App.js:4990-5000` triggers this.

### 3.5 What's actually computed per row

```
broker_commission   = gross * (commission_pct / 100)         # CHL revenue
after_commission    = gross - broker_commission              # carrier-due
quick_pay_fee       = after_commission * (term.fee_pct/100)  # 2-day = 0%
net_payment         = after_commission - quick_pay_fee
                      - fuel_advance + detention_pay
```

That `net_payment` is the **dollar amount CHL would owe the driver/carrier**
if CHL itself wrote the check.

### 3.6 Audience

The page is operator-facing (broker/dispatcher) -- requires broker role, not a
public surface. Drivers see a parallel "My Payments" view at
`App.js:10940-10983` (`CarrierPaymentsList`) inside the carrier hub, which
shows the same `db.payments` rows filtered for that driver.

### 3.7 Adjacent surfaces (NOT to be confused with Payments tab)

- **`SettlementsView.jsx`** -- the 3-pillar integrity gate
  (delivered + GPS in geofence + POD uploaded) before a payment can be funded.
  Currently hidden from the top bar per iter 138.1.
- **`PendingCarrierPayouts.jsx`** -- Stripe Connect pending-payout queue
  (`/api/stripe/connect/pending-payments`). This is a separate carrier-AP
  rail using Stripe Connect, not the Stripe Checkout flow that "Pay Now"
  triggers.
- **`StripeConnectCard.jsx`** -- carrier-side Connect onboarding +
  payment history.
- **`/api/payment_transactions`** -- Stripe session log; populated when
  someone clicks "Pay Now" on a payment row (`server.py:3766-3778`).

## 4. Shipper-AR vs Carrier-AP framing

CHL's actual money flow:

```
                      shipper rate confirmation
                              |
                              v
   Shipper ----($$ shipper-AR)----> CHL ----($$ carrier-AP)----> Carrier
        (30-60 days net)              ^                  ^
                                      |                  |
                                  broker_invoices    factor advance
                                                     (carrier paid by factor)
                                                     factor collects from CHL
                                                     when shipper pays
```

| Concept                   | Module / collection             | Direction                |
| ------------------------- | ------------------------------- | ------------------------ |
| Shipper invoice (CHL AR)  | `db.broker_invoices`, `delivered_load_autofire.py`, `collections_packets.py` | shipper -> CHL    |
| Shipper Stripe checkout   | (would be POS shipper-pay flow) | shipper -> CHL    |
| CHL revenue ledger        | `db.broker_revenue_ledger`      | CHL retained share        |
| Carrier-AP (factor route) | `factor_matrix.push_load_to_factor`, `factors/rts_client.py`, `factors/otr_client.py`, `factor_performance.py` | factor -> carrier    |
| Carrier-AP (direct)       | `db.payments`, `PaymentsView` (THIS TAB) | CHL -> carrier (rare/optional)         |
| Carrier-AP (Stripe Connect)| `PendingCarrierPayouts.jsx`, `/stripe/connect/pending-payments` | CHL -> carrier via Connect     |

Operator is correct: in the factoring-first model, **CHL's
carrier-AP responsibility is ZERO**. The factor advances the carrier; the
carrier never waits on CHL. CHL's only ongoing obligation to the factor is to
forward any direct-shipper payments that mistakenly arrive at CHL accounts
(pass-through).

The "Driver Payments" tab as currently rendered is a vestige of an earlier
iteration (78.x / 138.x) where CHL was modeling itself as the payer-of-record
for drivers/carriers. That model has been superseded.

## 5. What the Payments button SHOULD show

Two valid replacement framings, depending on operator preference:

### Framing A -- Shipper AR Aging (CHL's incoming side)

The page becomes "Receivables" or "AR Aging" -- shows `db.broker_invoices`
(CHL invoices billed to shippers), buckets by aging (0-30 / 31-60 / 61-90 /
90+), per-shipper subtotals, with a "Send Reminder" / "Send Collections
Packet" action. This is information CHL actually needs and currently lacks
a top-bar surface for.

### Framing B -- Factor Settlement Status (CHL's outgoing-via-factor side)

The page becomes "Settlements" or "Factor Pipeline" -- shows
`broker_revenue_ledger` joined with factor submission status from
`factor_matrix.py` / `factor_performance.py`. Each row: load, factor
provider, submission timestamp, advance status, reconciliation status.
Operator gets visibility into the factor-AP rail without ever implying
CHL is the payer.

### Framing C -- BOTH (recommended; tab becomes umbrella)

Rename top-bar **"Payments"** -> **"Cash Flow"** with two sub-tabs:
- **Receivables** (Framing A: shipper AR)
- **Factor Pipeline** (Framing B: carrier-AP-via-factor)

The legacy `db.payments` collection stays in the backend but loses its
top-bar entry; it's kept because (a) `delivery_completion.py` writes to it
on every delivery (it's the historical source of broker_commission ledger
events), (b) `db.payments` is referenced by milestone calculations
(`milestones.py:62`), and (c) test suites depend on its shape
(`tests/test_payments_status_migration.py`). Removing the collection is
high-risk; removing the UI surface is low-risk.

## 6. Recommendation: RENAME (with internal-deprecation of write path)

| Action                | Recommendation         |
| --------------------- | ---------------------- |
| Top-bar label         | RENAME "Payments" -> "Cash Flow" |
| `PaymentsView`        | REPLACE with split view (Receivables + Factor Pipeline) |
| `db.payments` schema  | KEEP (referenced by 7+ modules; high-risk to drop) |
| `db.payments` writes  | DEPRECATE -- mark `delivery_completion.py:251` write as legacy; new path writes broker_revenue_ledger + factor_submission_log only |
| `POST /api/payments`  | KEEP for now (manual broker override flow), label as legacy in OpenAPI |
| `POST /api/payments/checkout` (Stripe Checkout) | DEPRECATE -- the Stripe Connect rail in `PendingCarrierPayouts.jsx` is the keeper; Stripe Checkout against `net_payment` is the wrong tool |
| `CarrierPaymentsList` (carrier-side) | RENAME header to "My Settlements" -- the row data is fine, just the "My Payments" label conflates carrier-collected income with broker-AP |

### Rationale (1 line each)

- **RENAME not REMOVE**: 7+ backend modules still write/read `db.payments`;
  ripping the UI is fast but the data model needs a multi-iter migration.
- **"Cash Flow" not "Payments"**: matches CHL's actual money-flow lens
  (incoming shipper-AR + outgoing factor-pipe), removes the misleading
  "we pay drivers" framing.
- **Sub-tab into Receivables + Factor Pipeline**: the two views answer the
  two questions a brokerage operator actually asks daily ("when am I getting
  paid?" + "is the factor going to advance this load?").
- **Stripe Checkout deprecation**: Stripe Checkout is built for one-time
  consumer-style charges; carrier-AP belongs on Stripe Connect (already
  wired in `PendingCarrierPayouts.jsx`). The two rails should not coexist
  in production.

## 7. Removal alternative (if operator prefers)

If operator decides the factoring-first model means **zero** carrier-AP
visibility belongs in the broker UI:

| Action                              | Surface                          |
| ----------------------------------- | -------------------------------- |
| Drop top-bar "Payments" tab          | `App.js:2719` -- delete entry    |
| Drop `<PaymentsView>` mount          | `App.js:2851-2853` -- delete     |
| Keep `PaymentsView` component dead   | leave function defined; no entry |
| Keep all `/api/payments*` endpoints  | back-end reference only; future re-mount possible |
| Keep `db.payments` writes            | `delivery_completion.py:251` unchanged |
| Drop "Pay Now" button                | `App.js:4990-5000` -- delete; no UI calls Stripe Checkout against payments any more |
| Drop carrier-side "My Payments" tab  | `App.js:10548` -- delete `tab === "payments"` mount; carrier still sees pending payouts via Stripe Connect surface |
| Replace top-bar slot                 | new "Cash Flow" entry pointing at a future Receivables view (Framing A) |

Even in REMOVE mode, **db.payments stays** -- the collection is load-bearing
for milestone math, broker_revenue_ledger linkage, and historical replay.

## 8. What the operator must decide

Operator-decision items (no code change until decided):

1. **Top-bar action**: KEEP / RENAME / REMOVE.
2. **If RENAME**: confirm "Cash Flow" or pick a different label
   (alternatives: "Money", "Ledger", "AR/AP", "Receivables").
3. **Sub-tab layout**: Receivables only / Factor Pipeline only / both.
4. **Stripe Checkout deprecation**: confirm the "Pay Now" button can go away
   (one operator workflow today: emergency direct-pay a carrier when a
   factor declines a load -- needs a replacement path before "Pay Now" goes
   dark).
5. **Carrier-side "My Payments" tab**: KEEP (rename to "My Settlements") or
   REMOVE.
6. **Migration scope**: migrate `db.payments` reads to a new collection name
   (e.g., `db.driver_settlements`) for clarity, or leave the legacy name?

## 9. Estimated effort per choice

| Choice                                              | Sprint estimate | Risk    |
| --------------------------------------------------- | --------------- | ------- |
| Rename top-bar label only                            | 0.5 day         | trivial |
| Rename + drop "Pay Now" button                       | 1 day           | low     |
| Full rename + Receivables sub-tab                    | 3-5 days        | medium  |
| Full rename + Receivables + Factor Pipeline sub-tab  | 1 sprint        | medium  |
| Full removal of UI + carrier-side parallel removal   | 2 days          | low (data model untouched) |
| Full removal + db.payments collection rename         | 2 sprints       | HIGH (touches milestones, broker_revenue_ledger, 7+ modules, multiple test suites) |

## 10. Dev recommendation

**RENAME to "Cash Flow"** with a Receivables-first sub-tab as Phase 1
(0.5 day for the rename + 3-5 days for Receivables); add Factor Pipeline as
Phase 2 (1 sprint). KEEP the existing PaymentsView render behind the legacy
`?activeTab=payments` URL for one iter post-rename to give operator/dev a
fallback if the new view has a regression.

DEPRECATE the Stripe Checkout "Pay Now" button only after operator confirms
the emergency-direct-pay workflow has a replacement path (probably a
manual-override entry inside Factor Pipeline that flags the load as
"factor declined; CHL paying direct via Connect" and routes to
`PendingCarrierPayouts.jsx`).

DO NOT drop `db.payments` writes. The collection is the historical record of
every delivery's broker-commission and net-payment-to-carrier; downstream
modules (milestones, broker_revenue_ledger reconciliation, test fixtures)
depend on it. Even after the UI rename, the collection stays live.

## 11. Cross-references

- `frontend/src/App.js:2717-2853, 4820-5045, 10548, 10938-10983`
- `frontend/src/views/SettlementsView.jsx`
- `frontend/src/views/PendingCarrierPayouts.jsx`
- `frontend/src/views/StripeConnectCard.jsx`
- `backend/server.py:3650-3850` (payments router)
- `backend/delivery_completion.py:215-300` (db.payments write path)
- `backend/payment_rails.py` (status transitions)
- `backend/milestones.py:62` (db.payments reader)
- `backend/factor_matrix.py:1214` (push_load_to_factor)
- `backend/factors/rts_client.py`, `backend/factors/otr_client.py`
- `backend/factor_performance.py:83`
- `backend/tests/test_payments_status_migration.py`
- `backend/tests/test_stripe_webhook_security.py`

End of study.
