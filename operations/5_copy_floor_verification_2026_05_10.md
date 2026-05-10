# 5-Copy Floor Verification -- 2026_05_10

**Generated:** 2026-05-10 18:19:38
**Backup root:** `D:\CHL_backups`
**Thresholds:** newest <= 24 h, oldest <= 96 h
**Overall status:** `HEALTHY` (exit=0)

## Slot status

| Slot | Status | Age (h) | Size (MB) | CHL.bundle (MB) | Artifacts OK | Notes |
|------|--------|---------|-----------|-----------------|--------------|-------|
| a | OK | 17.5 | 792.01 | 7.22 | True | fresh + complete (17.5 h) |
| b | OK | 17.5 | 792.03 | 7.22 | True | fresh + complete (17.5 h) |
| c | OK | 17.5 | 792.06 | 7.22 | True | fresh + complete (17.5 h) |
| d | OK | 17.4 | 792.09 | 7.22 | True | fresh + complete (17.4 h) |

## Rotation window

- OK: rotation window healthy

- **Newest slot:** d (17.4 h old)
- **Oldest slot:** c (17.5 h old)

## Standing rule

Operator-mandated 2026-05-10 EOD-17: 5 current full copies at all times -- 1 live `C:\CHL` + 4 `D:\CHL_backups\5copy_floor_{a,b,c,d}`. Auto-rotated by `scripts\maintain_5_copies.ps1` when oldest > 24 h. NEVER overwrite partials. This script is READ-ONLY audit only.
