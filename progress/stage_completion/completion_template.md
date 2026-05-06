# Completion: iter <N> stage <id> — <brief>

> Template — copy to `iter_<N>_stage_<id>.md` and fill in.

## Outcome

One paragraph summarizing what happened. Reader (operator or PM Claude) should be able to determine in 30 seconds: did this stage achieve its goal? Are there caveats?

## Action steps applied

| # | File:line | Change | Constraints honored |
|---|---|---|---|
| 1 | `path/to/file.py:N-M` | <brief description> | surgical-edit Y, fallback preserved Y |
| 2 | ... | ... | ... |

## Smoke results

| Target | Method | Expected | Actual | Status |
|---|---|---|---|---|
| `/api/...` | GET | 200, `source: llm`, ~Nk chars | 200, `source: llm`, 1024 chars | green |
| `/api/...` | POST | 200, `draft_message` string | 200, draft_message string | green |
| `module.func` | venv harness | non-None return | non-None return | green |
| `<deferred>` | n/a | n/a | deferred — <reason> | deferred |

## Verify yourself

```powershell
# Recent commits
git log --oneline -5
# Expected:
# <SHA> iter <N> stage <id>: <brief>
# ...

# Confirm action-step changes landed
Select-String -Path "<file>" -Pattern "<pattern>"
# Expected: <count>

# Confirm no unrelated changes
git show --stat HEAD
# Expected: only files listed in §Action steps
```

End-to-end check (logged-in browser at https://continentalhaul.com):
```javascript
fetch("/api/...", { method: "POST", credentials: "include", headers: {"Content-Type":"application/json"}, body: JSON.stringify({...}) })
  .then(r => r.json())
  .then(j => console.log(j))
// Expected: <shape>
```

## Caveats

- <Caveat 1, e.g., key was chat-pasted, rotation pass deferred to iter close>
- <Caveat 2>
- <Caveat 3>

## Open items / next stage

Next stage per `agenda/current_iter.md`: stage <next_id> — see `agenda/stages/stage_<next_id>.md`. Pre-conditions for entering next stage:
- This stage's commit landed: <SHA>
- Smoke green: <list>
- No regressions detected in adjacent endpoints

If pre-conditions aren't all met, do not proceed — write to `progress/current_status.md` with `BLOCKED ON <thing>` and stop.

Handoff: `progress/stage_completion/iter_<N>_stage_<id>.md` (<size> KB)

DONE
