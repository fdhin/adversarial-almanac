---
technology: OAuth 2.0 / OpenID Connect
kind: overview
audience: offensive-security
layer: authorization-protocol
first-released: 2012
homepage: "https://oauth.net/2/"
source: "rewritten from HackTricks (pentesting-web/oauth-to-account-takeover.md), CC BY-NC 4.0, accessed 2026-11-12"
provenance: "hybrid: corpus-grounded (1 ledger rows, internal coverage ledger) + HackTricks enrichment 2026-11-13; credit in source line"
---

# OAuth 2.0 / OpenID Connect

> The delegation protocol behind every "Continue with…" button. It was built to answer one question — "may this application act on this user's data" — and was never built to answer "who is this user". Most OAuth account takeovers are a client answering the second question with machinery designed for the first.

## What it is

OAuth 2.0 (IETF, RFC 6749, 2012) is a framework for **delegated authorization**: a client application asks an authorization server for narrowly scoped access to a user's resources on a resource server, the user consents, and the client receives a bearer token it can spend against the API. Passwords never cross. OpenID Connect (OIDC, OpenID Foundation, 2014) is the identity layer grafted onto the same flow: it adds a signed ID token asserting that a specific user authenticated, plus a userinfo endpoint. Between them they run social login, enterprise SSO, and the "connect your account" wiring of most SaaS integrations.

The distinction that pays on an engagement: **authorization is not authentication**. OAuth's native output — an access token — says "the holder may do X". It says nothing about who is present, and RFC 6749 never logs anyone in. "Sign in with IdP" is something the *client* constructs: run a code flow, call userinfo, then apply its own logic to mint a local session, typically "the response contained `email=victim@example.com`, therefore this is the owner of that local account". The protocol transports assertions; the client's interpretation of them is the attack surface. When a team says "we use OAuth for login", expect the linking logic, not the cryptography, to be the bug.

The third fact matters just as much: the framework is deliberately incomplete. Exact redirect matching, the `state` parameter, PKCE, code binding, audience checks — the RFCs describe them as MUST or SHOULD and leave enforcement to whoever wires the product together. Two implementers on the same team fill those gaps differently. The distance between the client's assumption ("the IdP validated that") and the authorization server's ("the client will check it") is where the takeovers live — which is why this is a bug *class*, not a list of CVEs.

## The anatomy that matters on an engagement

The authorization-code flow, the shape you will trace on nearly every target:

    browser ──▶ client app (app.example.com)
        ── 302 ──▶ authorization server /authorize
                    ?response_type=code&client_id=…
                    &redirect_uri=…&scope=…&state=…&code_challenge=…
        user authenticates and consents at the IdP
        ◀─ 302 ── redirect_uri?code=…&state=…
        client ── POST /token (code + client_secret | code_verifier) ──▶ auth server
        client ── Authorization: Bearer … ──▶ resource server / userinfo

Three structural properties drive most findings:

- **Two channels with different trust levels.** The *front channel* (redirects riding through the user's browser) is hostile: observable, replayable, shared with every script on every page involved. The *back channel* (the direct server-to-server token exchange) is not. The design survives only if the one artifact that crosses the front channel — the `code` — is short-lived, single-use, and bound to something the attacker doesn't hold, and if nothing more valuable ever crosses it.
- **Everything is a bearer artifact.** Codes, access tokens, refresh tokens: possession is authorization. Any leak — a Referer header, browser history, a log line, a postMessage — is not an "information disclosure" but a takeover primitive. That is why minor bugs escalate on OAuth targets.
- **The gaps are filled per-product.** The spec's permissive seams are implemented differently by each actor, so identical-looking flows on two targets fail in different places. You never reuse a checklist blindly; you re-walk the flow every time.

### The four actors and their characteristic mistakes

- **Resource owner (the user).** Their only power is consent — and consent fatigue is the exploit. Phishing no longer needs a fake domain when the *real* IdP page asks, truthfully, to authorize an attacker-registered application. Wherever client registration is open, evil-app registration is in scope.
- **Client (the app requesting access — usually your target).** The richest actor: skips `state` validation (login CSRF), accepts any code or token without audience checks, links federated identities by email match, still runs the implicit flow, parks tokens in URLs or `localStorage`, or ships its `client_secret` inside a mobile app.
- **Authorization server / identity provider.** Matches `redirect_uri` loosely (prefix or substring instead of exact), issues codes that are reusable or live for hours, honors a client-supplied `scope` at the token endpoint, returns unverified emails as claims, or doesn't enforce PKCE for public clients.
- **Resource server (the API behind the token).** Validates the token's signature but not its audience or scopes — so a token minted for client A works on client B's APIs (client confusion), and a read-scoped token calls write endpoints.

## The grant types you'll actually meet

- **Authorization code (+ PKCE)** — the modern default. A one-time code crosses the browser; tokens stay on the back channel. PKCE (RFC 7636) binds the code to a per-flow secret so an intercepted code is worthless. On a target, test the *binding* — downgrade and verifier enforcement — never just the presence of `code_challenge`.
- **Implicit** (`response_type=token`) — deprecated by RFC 9700, still deployed wherever legacy lives. Delivers the access token itself to the browser in the URL fragment. Finding-friendly by construction: browser history, script-readable, harvestable by any analytics SDK on the callback page.
- **Client credentials** — machine-to-machine, no user in the flow; the client authenticates with its secret. The bug class is leaked secrets (unpacked APKs, SPA bundles, CI configs) and over-broad scopes, not browser trickery.
- One to recognize on sight: **ROPC / password grant** — the client takes the raw password. Where it survives it usually sidesteps the 2FA or email-verification steps the interactive flow enforces; test it as a bypass path.

## The OIDC layer on top

OIDC promotes the same legs into an authentication protocol by adding the **ID token**: a JWT signed by the IdP carrying `iss` (who issued it), `sub` (the only stable identifier for the user — emails are mutable), `aud` (which client it was minted for), and `nonce` — a client-generated value that must come back inside the token to prove the token belongs to *this* login attempt. The takeover classes all live in the binding: nonce never checked (token replay), `aud` never checked (a token minted for your own app gets accepted by the target), `email_verified=false` ignored while the client links accounts on email match. Attacks on the token's signature and header — alg confusion, key injection — are generic JWT problems and live in [[jwt-bug-bounty|the JWT methodology]].

## Where you'll meet it

- **Social login** — the target is a relying party consuming Google, Microsoft, GitHub, Apple. You attack the client's callback handling and its account-linking logic; the IdP is out of scope and hardened. Thinnest coverage: the local "match by email, issue session" step some team wrote themselves.
- **Mobile app auth** — the client is a public app using custom URI schemes (`com.example.app:/callback`). Any other app on the device can register the same scheme and receive the redirect (scheme hijacking), and secrets embedded in the package are yours to unpack. Static analysis of the artifact pays before any dynamic testing.
- **SaaS integrations** — "Connect your account", workflow wiring, scheduled syncs holding refresh tokens for years. The target may be either side: the platform acting as IdP for thousands of tenants (redirect matching and code binding at scale) or the consumer holding over-scoped tokens.
- **Enterprise SSO** — the IdP *is* the product (Entra ID, Okta, Auth0, Keycloak). You attack the authorization server itself: discovery documents, dynamic client registration, tenant and issuer confusion, silent re-auth (`prompt=none`).

## Reading the protocol's mind

Five facts predict most of what an OAuth target does on the wire — each expands into a full method in [[oauth-bug-bounty|the methodology]]:

1. **A code or token proves permission, never identity.** Any client that mints a session from "I received a token" without binding checks is the takeover.
2. **The front channel is hostile territory.** Whatever crosses the browser can be intercepted, replayed, or delivered to the wrong place. Exact-match `redirect_uri`, `state`, and PKCE are the countermeasures, and each is optional-looking enough to be missing.
3. **Bearer means possession wins.** Every leak is a takeover; grade "minor" disclosures accordingly.
4. **Identity is only as good as its binding.** `iss` + `sub` are stable; emails are mutable and often unverified; the nonce ties an ID token to one login. Whoever ignores those bindings is trusting an attacker-controllable value.
5. **The discovery document is free recon.** `/.well-known/openid-configuration` publishes the endpoint map, supported scopes, PKCE methods, and whether client registration is open — the target's OAuth surface hands itself over before you send a single crafted request.

The smallest interesting artifact, annotated with where each guarantee actually stops:

```text
https://auth.example.com/authorize
  ?response_type=code           # "token" here = implicit flow: bearer token in the fragment
  &client_id=app_123            # public by design — quoting it makes you a legitimate caller
  &redirect_uri=https://app.example.com/auth/callback
                                # spec: exact match against registration. reality: implemented
                                # with contains/prefix/suffix somewhere — the #1 bug class
  &scope=openid email profile   # the ceiling of what the token may do, if anyone enforces it
  &state=9f2c…                  # CSRF binding — "RECOMMENDED" in RFC 6749: expect it missing
  &nonce=7b1d…                  # OIDC replay binding — client-generated, client-verified
  &code_challenge=E9Mel…        # PKCE — binds the code to this browser's verifier
```

## Where to go next

[[oauth-bug-bounty|OAuth bug bounty methodology]] — the phased field guide for agents and humans: flow mapping from the discovery document, the redirect_uri bypass ladder, login-CSRF delivery, the PKCE downgrade matrix, implicit-flow leakage, email-trust account linking, scope and consent bypasses, ID-token binding checks, and a request-shape quick reference with secure-vs-vulnerable verdicts.
