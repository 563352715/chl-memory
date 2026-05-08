# Sub-Agent Boilerplate — Reference for All Dev-Engineer Dispatches

> **Purpose:** capture the boilerplate constraints + verification format that
> EVERY sub-agent dispatch from this codebase needs. Sub-agent prompts now
> reference this doc by path instead of repeating ~500 tokens of boilerplate
> per dispatch.
>
> **How to use:** the parent dispatch prompt should open with a single line:
> `Read C:/CHL/chl-memory/research/SUB_AGENT_BOILERPLATE.md and follow its
> constraints unless this prompt explicitly overrides.` Then the prompt only
> needs the task-specific spec, file paths, and report-format addenda.
>
> **Last updated:** 2026-05-08 EOD-14 (after iter 142.1 stages 1c-1f close +
> EOD-13 carrier-comm + Stream W batches). Re-audit when sub-agent failure
> modes shift.

---

## 1. What sub-agents must NOT touch

These are **dev-engineer's surgical-edit responsibility** because they're
monolith files where parallel sub-agent edits would conflict:

- `backend/server.py` — the FastAPI monolith (12K+ lines). Dev does the
  single batched wire-up edit ONCE per orchestration cycle covering ALL
  streams' wire-up needs.
- `frontend/src/App.js` — the React monolith. Dev wires up new tabs/views.
- `backend/.env` / `frontend/.env` — config goes through
  `scripts/set_env_var.ps1`, never edited by sub-agents.
- Auth files: `backend/auth.py`, anything under `backend/auth/`,
  `require_broker`, `get_current_user`, `issue_magic_link` — all dev-touched.
- Any module already shipped in a prior batch unless the spec EXPLICITLY
  authorizes touching it (e.g., bug-fix re-dispatch). Examples seen lately:
  `carrier_docs/document_store`, `carrier_contacts/contact_store`,
  `fmcsa_qcmobile_refresh_cron`, `integrity/budget_caps`,
  `integrity/circuit_breakers`, `integrity/operational_state`,
  `integrity/audit_trail`, `health_monitor`, `state_lock`, `evaluators/`
  (after iter 142.1 close), `outreach/`, `assignment/`, `dispatch/`.
- Operator-controlled scripts: `scripts/snapshot_to_d.ps1`,
  `scripts/backup_to_d.ps1`, `scripts/install_*.ps1`,
  `scripts/harden_chl_backend_nssm.ps1` (NSSM service installer).
- Memory files in `~/.claude/projects/c--CHL/memory/` — those are
  operator's standing directives and feedback. Sub-agents read them; only
  dev edits them, and only on operator-authorized scope.

If you (the sub-agent) believe an existing module needs a change to make
your work correct, STOP and surface it in your output report under
"Wire-up note for dev." Dev will make the surgical edit.

---

## 2. Coding constraints

### Python
- **ASCII only.** No emojis, no non-ASCII whitespace, no smart quotes. The
  Windows console + git diff tooling chokes on non-ASCII.
- **Lazy imports for external SDKs.** Top-level `import openai` /
  `import httpx` / `import motor` will crash the backend's import-smoke if
  the venv lacks the dep. Wrap inside the function that actually uses it,
  or wrap the top-level import in `try/except ImportError: <SDK> = None`
  and gate runtime calls. The backend venv on operator's box is sometimes
  ahead of / behind the spec dep list; lazy imports survive both.
- **Idempotency mandatory** on any cron/auto-action. State writes use
  `update_one` with a uniqueness guard; loops use a `should_skip_tick`
  check; outreach / DM / SMS use a time-window key (typically 5-min or
  30-min) on `(load_id, mc_number, action_type)`.
- **No real API calls in tests.** Mock external HTTP, mock the OpenAI
  client, mock motor's Mongo driver via a synthetic in-memory collection
  or `unittest.mock.AsyncMock`. Tests must run offline.
- **No emojis in strings written to logs / DB / responses.** Operator
  views logs in PowerShell; emoji rendering is inconsistent.

### Markdown
- ASCII only (same reason).
- Use code blocks with language tag (` ```python ` / ` ```bash `).
- Section headers use `##` / `###`; never deeper than 4 levels.

---

## 3. Public API conventions

These are the canonical seams `server.py` expects. Stick to these unless
the dispatch spec explicitly says otherwise.

### Cron modules
```python
# <pkg>/<name>_cron.py
def init(db) -> None:
    """One-time setup (DB indexes, in-process state). Idempotent."""

def start_loop() -> None:
    """Kicks off the background loop. Returns immediately."""

def register_<name>_cron_health(register_health_check) -> None:
    """Calls register_health_check(name, check_fn) for /api/health/system."""
```
- Positional `db` parameter (NOT keyword-only). Server calls `init(db)`.
- `start_loop()` takes no args. Server-side wrapper inside
  `@fastapi_app.on_event("startup")` calls it.
- The `register_*_cron_health` name MUST end in `_health` and start with
  `register_` so the preflight script
  (`scripts/preflight_server_smoke.py`) recognizes the seam.

### Routers
```python
# <pkg>/<name>_router.py
from fastapi import APIRouter

def build_router(db, require_broker) -> APIRouter:
    """Returns a configured APIRouter. Positional args by convention."""
    router = APIRouter(prefix="/api/<name>", tags=["<name>"])
    # ... endpoints ...
    return router
```
- **Positional `db, require_broker`** unless explicitly noted kw-only.
  Server.py mixes both styles, but **default to positional** — kw-only
  has caused 2 service crashes (iter 142.1+). If you must use kw-only,
  EXPLICITLY state it in your output report so dev wires it up correctly.
- Endpoints prefixed with `/api/<name>`. Tags lowercase.
- Auth dependency via `Depends(require_broker)` on every endpoint that
  mutates state or returns sensitive data.

### Stores / repositories
```python
# <pkg>/<name>_store.py
class <Name>Store:
    def __init__(self, db):
        self.col = db["<collection_name>"]
    async def get(self, key): ...
    async def upsert(self, payload): ...
```
- Async motor calls, never sync pymongo.
- Return plain dicts (not Pydantic models) unless the caller asks for one.

### Health-check registration
```python
def register_<name>_health(register_health_check) -> None:
    register_health_check(
        name="<name>",
        check_fn=_check,
        timeout_s=5,
    )

def _check() -> dict:
    return {"status": "ok", "details": {...}}
```
- `_check` returns a dict with `status` (`"ok"` / `"degraded"` / `"down"`)
  and `details` (dict).
- `register_health_check` is the import from `health_monitor`. Pass it
  through; never import `health_monitor` directly inside the new module.

### Schema fields
Conventions for any new collection / DB field:
- `created_at` / `updated_at`: ISO8601 UTC strings. Use
  `datetime.now(timezone.utc).isoformat()`.
- IDs: prefer string UUIDs over ObjectIds. Use `uuid.uuid4().hex`.
- MC numbers: stored as integers (no leading zeros, no dashes).
- DOT numbers: integers.
- Phone numbers: E.164 strings ("+15551234567"), never local format.
- Money: integer cents (NOT float dollars) — avoids float-equality bugs.
- Booleans named `is_<x>` / `has_<x>` / `<x>_enabled` —- never
  ambiguous like `<x>_status: bool`.

---

## 4. Verification format (paste verbatim)

Every sub-agent prompt requires this verbatim block at the end. Paste
literally including the language tag.

````
## Smoke + pytest verification (paste verbatim in your output report)
```
ls <each path written>
python -c "import ast; ast.parse(open('<each .py written>').read())"
python -c "import <module name>"
python -m pytest <test paths> -v
```
````

The sub-agent must:
1. Run each command from `C:/CHL` (absolute paths in commands, but cwd at
   project root).
2. Paste the LITERAL output. No paraphrasing. No "[truncated]". If a
   command's output exceeds 50 lines, paste the first 30 + last 20.
3. If any command fails, the sub-agent FIXES the failure before reporting
   back. Do not return a broken state and ask dev to fix; that defeats
   the parallelism gain.

---

## 5. Output report format

Every sub-agent's final message must contain these sections in order:

```
## Files written
- <absolute path> (<line count> lines) — <one-line purpose>
- ...

## Smoke + pytest verification
<paste verbatim per Section 4>

## Wire-up note for dev
<one paragraph: what server.py edit is needed, what App.js edit is
needed, what env vars need setting via set_env_var.ps1, any operator
action required (Restart-Service, free-tier API signup, etc.)>

## Key design decisions
- <bullet 1: any non-obvious tradeoff>
- <bullet 2>
- <bullet 3>

## DO NOT commit/push
This sub-agent did not commit or push. Dev orchestrates the single
batched commit per orchestration cycle.
```

---

## 6. Anti-patterns (don't do these)

These are mistakes sub-agents have actually made. Each one cost
debugging time at integration.

### a. kw-only build_router signature without explicit doc
```python
# BAD -- breaks server.py's positional call site
def build_router(*, db, require_broker) -> APIRouter: ...
```
Server's wire-up convention is positional. If you have a strong reason to
use kw-only, NOTE IT EXPLICITLY in your "Wire-up note for dev" so dev
adapts the call site. Otherwise default positional.

### b. Top-level import of an SDK that may be missing
```python
# BAD -- breaks backend's import-smoke if `openai` isn't in the venv
import openai
```
```python
# GOOD -- lazy import; backend boots even if SDK absent
def _client():
    import openai
    return openai.OpenAI(api_key=os.environ["OPENAI_API_KEY"])
```
Same applies to `httpx`, `reportlab`, `pypdf`, `twilio`, `plivo`,
`stripe`, `cryptography`, `psutil`, `motor` (motor is the one exception —
backend hard-depends on it; top-level import is fine). Also same for
`pydantic[email]` validators (`EmailStr`); use plain `str` + a simple
regex check unless `email-validator` is confirmed installed.

### c. Forgetting register_*_health
```python
# BAD -- module ships, server.py wires init() + start_loop(), but cron
# never appears on /api/health/system. Operator can't see if it's healthy.
```
ALWAYS ship a `register_<name>_health(register_health_check) -> None`
function and tell dev to call it from server.py.

### d. Real network calls in tests
```python
# BAD -- test makes a real HTTP call; flakes on CI / breaks offline
async def test_enricher():
    result = await enrich_carrier(MC=1234567)  # actually hits FMCSA
```
```python
# GOOD -- mock the HTTP layer
async def test_enricher(monkeypatch):
    monkeypatch.setattr("module.httpx.AsyncClient", _MockClient)
    result = await enrich_carrier(MC=1234567)
```

### e. Calling `.update_many` over a wide filter for an outcome write
Iter 142.1 verifier-caught bug: writing one outcome row that update_many'd
across all lane variants flattened lane-level granularity. Use
`update_one` scoped to the SINGLE most-recent unobserved row. The iter
145.1 self-healing layer needs the granularity preserved.

### f. Hardcoded paths inside the module
```python
# BAD -- breaks on dev machine vs prod
LOG_DIR = "C:/CHL/logs"
```
```python
# GOOD -- env var with sensible default
LOG_DIR = os.environ.get("CHL_LOG_DIR", "C:/CHL/logs")
```

### g. Forgetting `should_skip_tick` on a cron's loop
Backend startup fires multiple `on_event("startup")` callbacks in quick
succession during NSSM service restart. A cron without
`should_skip_tick(name, idempotency_window)` will double-fire on warm
restart and either burn rate limits or write duplicate rows. Always
gate cron tick logic on `should_skip_tick` (from `system_clock`).

### h. Mutating `db.loads` outside the dispatch state machine
Since iter 142.1 stages 1c-1f, `db.loads.dispatch_status` field is
controlled by the state-machine seam in `outreach/`, `assignment/`, and
`dispatch/`. New modules MUST NOT write `dispatch_status` directly;
instead call into the seam or surface a state-transition request to dev.

### i. Using emojis in commit messages, log lines, or response bodies
Operator's PowerShell / git tooling renders inconsistently. Emojis cost
debugging-time when log greps stop matching. ASCII only.

### j. Skipping the "DO NOT commit/push" closing line
Every sub-agent report MUST end with the explicit "DO NOT commit/push"
line. The orchestrator gates commits to a SINGLE per-cycle event; a
sub-agent that pushes prematurely breaks the gate and forces a revert.

---

## 7. Quick checklist (sub-agent self-audit before reporting back)

- [ ] Did I read this boilerplate doc + the dispatch's spec doc?
- [ ] All Python ASCII-only?
- [ ] All external-SDK imports lazy?
- [ ] `init` / `start_loop` / `register_*_health` / `build_router`
      signatures match Section 3 conventions?
- [ ] Tests mock all externals; offline-clean?
- [ ] Idempotency guard on every cron + auto-action?
- [ ] No writes to `db.loads.dispatch_status` outside the dispatch seam?
- [ ] No edits to server.py / App.js / auth files / .env / pre-existing
      modules listed in Section 1?
- [ ] Verification block matches Section 4 verbatim format?
- [ ] Output report has all 5 sections from Section 5?
- [ ] Final "DO NOT commit/push" line present?

If every box is checked, the sub-agent is ship-ready and the parent
verifier-gate has a clean surface to review.
