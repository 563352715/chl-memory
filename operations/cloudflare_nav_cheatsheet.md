# Cloudflare Dashboard Navigation Cheatsheet — 2026-05-12

**For next session: this is how the Cloudflare UI is structured AS OF 2026-05-12. Do NOT instruct operator with old UI labels ("Security" doesn't exist in left sidebar anymore — it's "Application security" under "Protect & Connect").**

## Operator credentials + IDs

| Field | Value |
|---|---|
| Account email | jasonandpugee@gmail.com |
| Account password | Vault entry `dd980c65-8a3a-4f90-950b-6c72ea26f1db` (URL field = `https://dash.cloudflare.com`, other fields blank — operator should populate after login) |
| Account ID | `b972956559f199a4dd34d074a86e7b5f` |
| Zone ID (continentalhaul.com) | `a7a3b90c0fdefcf02cafe636f10af9ba` |
| Plan | Free |

## Account-home sidebar (AS OF 2026-05-12)

```
[search bar - "Quick search Ctrl+K"]
[Ask AI button top right = "Agent Lee" — needs API token; skip]

Account home
Recents     >
Domains     >        # Overview / Registrations / Transfers — Registrar service, NOT zone config
---
Observe
  Investigate  >
  Analytics    >
---
Build
  Compute      >
  AI           >
  Storage & databases  >
  Media        >
---
Protect & Connect
  Application security  ← Bot Fight Mode, WAF, DDoS, Rate Limiting LIVE HERE
  Zero Trust            ← Cloudflare Access setup lives here
---
Networking         >
Delivery & performance >
---
Manage account     >
```

## To enable Bot Fight Mode (after operator login)

**Step 1:** Click `continentalhaul.com` in the Domains tile on Account home main panel (NOT the "Domains" sidebar item — that goes to Registrar).

**Step 2:** Once inside the continentalhaul.com zone, the LEFT SIDEBAR CHANGES. Look for **Security** (zone-level sidebar) → click **Bots** → toggle **Bot Fight Mode**.

**Alternative if zone sidebar doesn't show Security:**
- In the modern UI, the zone-level Security menu may appear under a different label. Try **"WAF"** in the sidebar, then **"Bot management"** as a sub-item.
- OR use the direct URL:
  `https://dash.cloudflare.com/b972956559f199a4dd34d074a86e7b5f/continentalhaul.com/security/bots`

## To set up Cloudflare Access (admin path gating)

From Account Home sidebar: **Protect & Connect → Zero Trust → Access → Applications**.

Setup guide on operator Desktop: `CloudflareAccess_AdminProtection_Setup_2026_05_12.md`.

## Already-toggled items (as of 2026-05-12 EOD-12)

| Item | State | Where |
|---|---|---|
| AI Crawlers — Block all pages | ON ✅ | Zone Overview page, "Control AI crawlers" section |
| Our own robots.txt deployed | ON ✅ | `/var/www/chl-public/robots.txt` on Hetzner |
| Bot Fight Mode | NOT YET | Application security → Bots (next session) |
| Content Signals Policy | DEFERRED | Zone Overview, restrictive setting recommended |
| Under Attack Mode | OFF (correct) | Zone Overview Quick Actions — panic button only |
| Development Mode | OFF (correct) | Zone Overview Quick Actions — debug-cache only |

## Useful read-only facts from current dashboard state

- **Recent audit log shows "Update Zone Bot Manage..." 6m ago** — that's the AI Crawler block toggle the operator just flipped, confirmed propagated.
- **Last 24h traffic: 36 unique visitors / 222 requests on continentalhaul.com.** chldispatch.com sees 2.2K — separate Worker deployment, will inventory next session.
- **Cache rate: 0.6%** (mostly dynamic content — expected).
- **Workers and Pages: 1 (`chldispatch`, 1 day ago)** — Cloudflare Worker on the chldispatch.com domain, needs inventory.

## Operator UI tells

- Operator gets frustrated when given URL-construction instructions ("type this exact path..."). Pattern works better: "click X in sidebar, then click Y under that".
- Operator's browser sometimes shows account-home sidebar but NOT zone-level sidebar — may be a viewport-width thing. Check screenshot first.
- "Agent Lee" search popups are NOT useful; tell operator to close + use direct navigation.
