---
technology: JWT (JSON Web Tokens)
kind: overview
audience: offensive-security
layer: auth-token
spec: RFC 7519 (with RFC 7515 JWS, RFC 7516 JWE, RFC 7517 JWK)
first-released: 2015
source: "rewritten from HackTricks (pentesting-web/hacking-jwt-json-web-tokens.md), CC BY-NC 4.0, accessed 2026-11-12"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# JWT (JSON Web Tokens)

> A JSON claim set that carries its own algorithm and key pointers inside the token — so every guarantee is a check the verifier had to remember to switch on.

## What it is

A JSON Web Token is an IETF standard (RFC 7519, 2015, part of the JOSE family) for packing signed JSON claims into one portable string. The compact serialization is exactly three base64url-encoded segments joined by dots: `header.payload.signature`. The header declares how the token was signed (`alg`) and optionally how to find the key (`kid`, `jku`, `x5u`, `x5c`, an embedded `jwk`). The payload is the claims set: who (`sub`), when (`iat`, `nbf`, `exp`), from where to whom (`iss`, `aud`), plus whatever authorization data the application layered on (`role`, `scope`, `tenant_id`). Only the third segment is cryptographic.

Two algorithm families dominate. **HMAC** (HS256/HS384/HS512) is symmetric: the same secret signs and verifies, so a leaked secret means anyone can mint tokens, and the secret must sit on every verifying node. **RSA** (RS256) and **ECDSA** (ES256) are asymmetric: a private key signs, a public key verifies, and the public key is usually published at a JWKS endpoint or inside a TLS certificate. The design decision that most JWT attacks live on: **`alg` is a header field — attacker-controlled input.** A verifier that dispatches on the token's declared algorithm instead of an algorithm pinned in code lets the attacker choose which check runs: none at all, an HMAC keyed with the server's own public key material, or a key the attacker effectively supplies through `jku`/`x5u`/`kid`.

The operator's mental model: base64url is an encoding, not encryption. The payload is plaintext anyone can read, and nothing in the format stops the first two segments from being edited. The signature proves exactly one thing — *these bytes were signed by whoever holds that key* — and nothing else. Freshness (`exp`, `nbf`), audience (`aud`), issuer (`iss`), and every authorization claim (`role`, `scope`) are inert strings unless the verifier explicitly reads and enforces them; JWT libraries ship with those checks as optional parameters, and an `exp` nobody validates is a token that never expires.

## The anatomy that matters on an engagement

The verification chain, with the misbehavior surface at each link:

    token = header.payload.signature
      → base64url decode          (payload readable by anyone — free intel)
      → alg dispatch              (attacker-controlled unless pinned in code)
      → key resolution            (static config | JWKS | kid file | jku/x5u URL)
      → signature verify          (the one cryptographic check)
      → registered-claim checks   (exp / nbf / iss / aud — each independently skippable)
      → application trust         (sub/role/scope become the authorization decision)

Three structural properties drive most findings:

- **The payload is intel before it is an attack surface.** Emails, internal user IDs, tenant identifiers, token lifetime, the issuing service — all leak by design. Decode every captured token; read it first, attack it second.
- **Everything upstream of the signature check is attacker input.** `alg`, `kid`, `jku`, `x5u`, `x5c`, embedded `jwk` are header fields — editable bytes that can steer *which* verification runs and *with what key*.
- **Claims are inert until enforced.** Registered claims are conventions the library checks only when told to; a verifier can check the signature perfectly and still accept a five-year-old token minted for a different audience.

## Where you'll meet it

- **SPA authentication** — the login endpoint returns a token the frontend parks in `localStorage`/`sessionStorage` (or a JS-readable cookie) and staples onto every call. Engagement implication: an XSS in the SPA *is* session theft — no HttpOnly boundary to cross — and the token's lifetime decides how long the stolen goods stay good.
- **API bearer authorization** — `Authorization: Bearer eyJ...` on mobile and server-to-server traffic. No browser, so cookie defenses (CSRF tokens, SameSite) simply do not apply; replay from anywhere is the threat model, and expiry enforcement is the only clock.
- **OAuth 2.0 / OpenID Connect** — ID tokens are JWTs by definition and access tokens often are; a third-party IdP (Entra, Auth0, Okta, Keycloak) signs while your target verifies. Implication: the app consumes tokens it did not issue, so `aud`/`iss` checks are the entire boundary — cross-service relay (a token minted for client A of the IdP accepted by client B) is the characteristic bug class.
- **Service-to-service auth in microservice estates** — shared JWKS URLs or one long-lived HS256 secret reused across verifiers. Implication: a single cracked secret or unpinned algorithm propagates one forgery across the whole estate.

## Reading the verifier's mind

Five facts predict how a JWT-verifying target behaves — each expands into a full method in [[jwt-bug-bounty|the methodology]]:

1. **Signature proves bytes, not semantics.** Cryptographic verification and claim enforcement are separate steps in every library; the gap between them is the richest finding ground.
2. **Registered claims are opt-in.** Default library behavior checks the signature and little else; `exp`, `aud`, `iss` each need an explicit parameter to matter.
3. **Algorithm comes from the token unless code pins it.** `algorithms=[...]` (or its equivalent) is the single most consequential line in a verifier.
4. **Key-selection headers are fetch instructions unless allowlisted.** `jku`/`x5u` point at URLs the server may dereference; `kid` often names a file, a DB row, or worse — all reachable from header bytes you control.
5. **Errors fingerprint the library.** Verification failure bodies (`Signature verification failed`, `Token expired`, stack-flavored 500s) identify the implementation and its defaults faster than any probe.

Both lines below "decode a token"; only one enforces anything beyond the signature:

```python
payload = jwt.decode(token, PUBLIC_KEY)                     # signature only — everything else trusted
payload = jwt.decode(token, PUBLIC_KEY, algorithms=["RS256"],
                     audience="api.example.com",
                     issuer="https://auth.example.com",
                     options={"require": ["exp", "iat", "sub"]})  # pinned alg, enforced claims
```

## Characteristic verifier mistakes

Roughly in engagement-deciding order — each is a worked method in [[jwt-bug-bounty|the methodology]]:

- **Signature not checked at all** — edit the payload, keep the stale signature, still 200.
- **`alg` accepted from the token** — the door for `alg:none` acceptance and RS256→HS256 confusion (verifying HMAC with a public key you can already download).
- **Weak HMAC secret** — HS256 under a default or dictionary secret: crack offline, then sign anything forever.
- **Registered claims unchecked** — expired tokens live on; a foreign-audience IdP token opens the API.
- **Key material taken from the token** — `jku`/`x5u` pointed at attacker-hosted JWKS; `kid` walked into predictable files, or injected into SQL/shell at the key-lookup sink.
- **Checked twice, enforced zero times** — middleware verifies the signature, business code reads `role` assuming someone else validated the rest; nobody validated the rest.

## Where to go next

[[jwt-bug-bounty|JWT bug bounty methodology]] — the phased field guide for agents: token capture and decode, the full attack matrix (signature tamper, `alg:none`, algorithm confusion, secret cracking, claim enforcement, header-key injection), vulnerable-vs-secure code pairs, and the tool map.
