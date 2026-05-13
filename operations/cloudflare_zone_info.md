# Cloudflare Zone + Account Identifiers — continentalhaul.com

Captured 2026-05-12 EOD-12 from the zone-overview dashboard.

## continentalhaul.com (primary marketing + admin)

| Field | Value |
|---|---|
| Zone ID | `a7a3b90c0fdefcf02cafe636f10af9ba` |
| Account ID | `b972956559f199a4dd34d074a86e7b5f` |
| Plan | Free |
| Registrar | Cloudflare (Cloudflare Registrar) |
| Domain expires | 2028-04-19 |
| DNS setup | Full |
| Status | Active |

## Account

| Field | Value |
|---|---|
| Account email | jasonandpugee@gmail.com |
| Account home password | Stored in Vault entry `dd980c65-8a3a-4f90-950b-6c72ea26f1db` (no name/username field populated yet — needs updating) |

## API tokens (managed)

- DNS-edit token issued 2026-05-12, expires 2026-06-30 (scope: DNS:Edit on continentalhaul.com only). Stored on operator Desktop as `Cloudflare_API_Token_*.md`.
- ⚠️ As of this writing, no Cloudflare token has been persisted to backend `.env` under `CLOUDFLARE_API_TOKEN`. To enable backend-driven DNS updates, set that env var on Hetzner and locally + restart `chl-backend`.

## Operational rules

- Zone ID + Account ID are NOT secrets but should not be pasted into AgentDM, sub-agent prompts, or git public-history commits. Reference by file path in this memory doc.
- API tokens ARE secrets — never paste values; only env var name + sha256[:16] + provisioned status.
- chldispatch.com is the secondary domain (also under same account) — separate Worker deployment; will inventory next session.

## Quick-reference API calls

```
# List DNS records
curl -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  https://api.cloudflare.com/client/v4/zones/a7a3b90c0fdefcf02cafe636f10af9ba/dns_records

# Purge cache (entire zone)
curl -X POST -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"purge_everything":true}' \
  https://api.cloudflare.com/client/v4/zones/a7a3b90c0fdefcf02cafe636f10af9ba/purge_cache
```
