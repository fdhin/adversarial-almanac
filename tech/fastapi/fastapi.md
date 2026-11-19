---
technology: FastAPI
kind: overview
audience: offensive-security
layer: web-framework
language: python
first-released: 2018
license: MIT
homepage: "https://fastapi.tiangolo.com"
github-repo: "fastapi/fastapi"
provenance: "imported user-authored methodology (v0.4, 2026-11-12); overview written to vault conventions"
---

# FastAPI

> The Python web framework where type annotations ARE the API contract — which means when you face one, the contract, the validation rules, and often the full route map are already in your hands.

## What it is

FastAPI is a modern, MIT-licensed web framework for HTTP APIs in Python, created by Sebastián Ramírez ("tiangolo"), first released 2018. It composes two existing libraries: **Starlette** (ASGI layer: routing, middleware, WebSockets) and **Pydantic** (validation and serialization). Its own contribution is the glue: developers annotate the types of what comes in and goes out, and the framework enforces that contract at runtime while generating documentation from the same annotations.

For an operator, that last sentence is the important one — it means a FastAPI target tends to *leak its own design*. A live contract at `/openapi.json`, interactive browsers at `/docs` and `/redoc`, and structurally predictable errors are on by default; someone has to actively switch them off. The framework's speed and typed safety also shape its bug profile: teams ship endpoints fast, and the framework's guarantees stop exactly where application logic begins — authorization and ownership checks are hand-written, per-route, and therefore forgotten somewhere.

## The anatomy that matters on an engagement

A request traverses a fixed chain, and every link is a place where behavior — and misbehavior — lives:

    client → Uvicorn (ASGI server) → Starlette middleware → routing
           → dependency injection (auth, DB sessions) → Pydantic validation
           → handler → response_model filtering → JSON

Two properties of that chain drive most findings:

- **Two execution worlds.** `async def` handlers run on the event loop (thousands of concurrent I/O-bound connections per worker); plain `def` handlers are parked in a threadpool. Blocking work inside an `async def` route freezes every concurrent connection — an application-wide DoS primitive, not a per-caller one.
- **Guards compose additively.** Auth dependencies attach at app, router, and route level, and a route's list *adds to* its router's rather than replacing it. Effective posture is the composed stack, never what the file layout suggests — which is why missing-guard bugs (BFLA) are structural rather than typos.

The supporting cast you will meet in real targets: SQLModel/SQLAlchemy (ORM), httpx (client), python-jose/PyJWT (JWT), Beanie/Motor (MongoDB), Jinja2 (templates), Strawberry/Ariadne (GraphQL mounted next to the REST routes — resolvers do *not* inherit route dependencies, which makes the GraphQL surface a prime leak candidate).

## Where you'll meet it

- **ML and AI model serving** — FastAPI's home turf. Prediction endpoints, LLM-backed services, agent runtimes. Engagement implication: the attack surface includes agent machinery (tool endpoints, model I/O) layered on top of the API, and AI teams move fastest of all — guard coverage is thinnest here.
- **Startup product backends** — TypeScript frontends generated from `openapi.json`, mobile clients talking straight to the API. Implication: CORS and mobile-app trust decisions matter, and velocity favors mass-assignment and BOLA in billing/tenant objects.
- **Internal and data-engineering APIs** — transformation jobs, dashboards, ETL triggers. Implication: hidden routes (`include_in_schema=False`), internal admin verbs, and BackgroundTasks/Celery workers doing SSRF-friendly fetches.

## Reading the framework's mind

Five facts explain most of what a FastAPI target does on the wire — each is expanded into a full method in [[fastapi-bug-bounty|the methodology]]:

1. **Validation is not authorization.** Pydantic guarantees shape, never authority. This asymmetry is the single richest source of findings (BOLA, API1:2023).
2. **The docs are on by default** — and routes hidden from the schema still route requests.
3. **`response_model` is the field allowlist.** Without one, the whole ORM object ships — password hashes included (API3 over-exposure).
4. **Declared models are the mass-assignment candidate list** — any user-editable Pydantic model carrying `role`, `tenant_id`, `is_admin` is asking for a PATCH.
5. **The error shapes fingerprint the stack.** `404 {"detail":"Not Found"}` on an unknown route plus a `422` with `loc/msg/type` on a bad body is close to conclusive that you are talking to FastAPI/Pydantic — the first thing to establish in Phase 0.

A 15-line illustration of what the framework does with annotations — the same code a target's developers wrote to get validation and docs for free:

```python
@app.post("/invoices", response_model=InvoiceOut)
async def create_invoice(invoice: InvoiceIn, user: User = Depends(get_current_user)):
    ...   # validation, 422s, /docs, /openapi.json — all generated;
          # whether *this user* may create *this tenant's* invoice: hand-written
```

## Where to go next

[[fastapi-bug-bounty|FastAPI bug bounty methodology]] — the full field guide written for AI agents and humans: fingerprinting, OpenAPI-driven surface mapping, the BOLA two-account method, the JWT attack matrix, injection sinks, parser edge cases, CORS/Host handling, dependency CVEs, and reporting.
